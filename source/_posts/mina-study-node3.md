---
title: mina学习笔记三：一切的源头IoService
date: 2016-09-08 20:12:15
categories:
- mina
tags:
- Java 
- MINA
toc: true
author:
comments:
original:
permalink: 
---

[原文:http://blog.csdn.net/yoara/article/details/37382137](http://blog.csdn.net/yoara/article/details/37382137)

# 1.IoService介绍

从上节的例子已经了解到，创建服务端服务第一步是：

```java
IoAcceptor acceptor = new NioSocketAcceptor();
```

而创建客户端连接的第一步是：

```java
IoConnector connector = new NioSocketConnector();  
```

这两个接口的父接口正是IoService。IoService是mina的核心组件，他提供标准的I/O 服务并且管理I/O 会话Session。IoService为大部分mina服务提供了底层的API支持。AbstractIoService是IoService的子类，他提供了基本的服务实现。先由下图简单的了解下IoService及其子类AbstractIoService的在mina体系结构中所担负的责任。 

![图3.1](http://opesdt6ii.bkt.clouddn.com/17-5-5/38311166-file_1493954966129_17b01.png)

职责分解图 如图所见，IoService的权责主要包括：

*   会话Session管理：创建和销毁会话，检测session等待等。
*   过滤器链管理：管理过滤器链，提供多个切面的服务，并允许用户在运行时动态变更过滤器链。
*   处理调用：当有新消息或其他session生命周期触发事件响应时，回调用户的业务代码。
*   统计管理：更新消息发送量、字节发送量等等信息。
*   监听器管理：提供服务各个生命周期触发时间的监听器管理，如服务有效时、服务空闲时、会话创建时等。
*   传输管理：可在服务端客户端有效的控制数据流的传输，平衡负载。

# 2.IoService API分析

那了解了IoService所承担的责任，以及知道他是服务端和客户端的共同祖先接口，我们有必要看一下他的API 清单：

*   void addListener(IoServiceListener)：为service添加监听器
*   void removeListener(IoServiceListener)：移除一个监听器
*   Set<WriteFuture> broadcast(Object)：向持有的所有session广播消息
*   void dispose()：关闭service并释放相关联的资源，如果有session还未关闭，此方法会一直堵塞。
*   void dispose(boolean)：如果参数为true，service会一直阻塞直到ExecutorService线程池关闭后，才会关闭。
*   boolean isDisposed()：是否已经注销service
*   boolean isDisposing()：是否正在注销service
*   boolean isActive()：是否service还在服务
*   long getActivationTime()：获取最新活动的时间，如果sevice已经停止，则返回最后一次活动时间
*   DefaultIoFilterChainBuilder getFilterChain()：获得默认的过滤器链，如果用户没有使用自定义的，将会抛IllegalStateException异常。
*   void setFilterChainBuilder(IoFilterChainBuilder)：设置过滤器链对象
*   IoFilterChainBuilder getFilterChainBuilder()：获得过滤器链对象
*   void setHandler(IoHandler)：设置处理类
*   IoHandler getHandler()：获得处理类
*   int getManagedSessionCount()：获得有效的连接session数量
*   Map<Long, IoSession> getManagedSessions() ：获得当前只读的有效session
*   int getScheduledWriteBytes()：获取尚未发送的字节数
*   int getScheduledWriteMessages()：获取尚未发送的消息数量
*   IoSessionConfig getSessionConfig()：获得连接配置信息
*   void setSessionDataStructureFactory(IoSessionDataStructureFactory)：设置初始化session用的数据信息，注意，服务启动后不可设置。
*   IoSessionDataStructureFactory getSessionDataStructureFactory()：获得初始化session时用到的一些数据信息
*   IoServiceStatistics getStatistics()：获得统计数据信息
*   TransportMetadata getTransportMetadata()：session相关的元数据信息

# 3.从IoAcceptor开始吧

    显然，该接口的名称来源于耳熟能详的accept()方法。mina框架已经为我们封装了大部分网络通讯的实现类。因此我们大可不必自己重新去实现（除非有特殊的应用场景）。我们可根据自己的情况从下选择：

*   NioSocketAcceptor ：非阻塞的套接字TCP（Socket） IoAcceptor
*   NioDatagramAcceptor : 非阻塞的数据包UDP IoAcceptor
*   AprSocketAcceptor : 基于 APR 的阻塞式套接字 IoAcceptor
*   VmPipeSocketAcceptor : 基于虚拟机管道的 IoAcceptor

    下图是IoAccceptor一支的类图结构：

![图3.2 IoAccceptor类图结构](http://opesdt6ii.bkt.clouddn.com/17-5-5/35734540-file_1493955049172_17775.jpg)



## 3.1 创建IoAcceptor

上节我们已经了解到，IoService开启服务的第一步是创建IoAcceptor：

```java
//首先，我们为服务端创建IoAcceptor，NioSocketAcceptor是基于NIO的服务端监听器
IoAcceptor acceptor = new NioSocketAcceptor();
```

NioSocketAcceptor 继承自 AbstractPollingIoAcceptor<NioSession, ServerSocketChannel>并实现了SocketAcceptor接口。NioSocketAcceptor共有四个构造函数签名：

*   NioSocketAcceptor()
*   NioSocketAcceptor(int)
*   NioSocketAcceptor(IoProcessor<NioSession>)
*   NioSocketAcceptor(Executor, IoProcessor<NioSession>)

分别调用父类的四个对应构造函数如下。从这里我们知道，IoAcceptor在实例化是必须依赖3个接口，他们分别是IoSessionConfig、IoProcessor、Executor。

*   AbstractPollingIoAcceptor(IoSessionConfig, Class<? extends IoProcessor<S>>)
*   AbstractPollingIoAcceptor(IoSessionConfig, Class<? extends IoProcessor<S>>, int)
*   AbstractPollingIoAcceptor(IoSessionConfig, IoProcessor<S>)
*   AbstractPollingIoAcceptor(IoSessionConfig, Executor, IoProcessor<S>)

### 3.1.1先从IoSessionConfig说起

    无论是NioSocketAcceptor或者NioDatagramAcceptor，具体实现类都是new一个DefaultDatagramSessionConfig实例做为传参： 

```java
public NioSocketAcceptor() {
        super(new DefaultSocketSessionConfig(), NioProcessor.class);
        ((DefaultSocketSessionConfig) getSessionConfig()).init(this);
    }
```

boolean tcpNoDelay：表示立即发送数据。默认false

boolean reuseAddress：表示是否允许重用Socket所绑定的本地地址。默认false

int soLinger：表示当执行Socket的close()方法时，是否立即关闭底层的Socket。默认-1

int sendBufferSize：表示发送数据的缓冲区的大小。默认-1

int receiveBufferSize：表示接收数据的缓冲区的大小。默认-1

boolean keepAlive：表示对于长时间处于空闲状态的Socket，是否要自动把它关闭。默认false

boolean oobInline：表示是否支持发送一个字节的TCP紧急数据。默认false。 int trafficClass：IP服务类型，底成本：0x02/高可靠：0x04/最高吞吐量：0x08/最小延迟：0x10

    同时祖父类AbstractIoSessionConfig还包括如下参数：

    private int minReadBufferSize = 64;

    private int readBufferSize = 2048;

    private int maxReadBufferSize = 65536;

    private int idleTimeForRead;

    private int idleTimeForWrite;

    private int idleTimeForBoth;

    private int writeTimeout = 60;

    private boolean useReadOperation;//当IoSession.read()方法可用时，为true。接受到的所有消息将被存储在内部的BlockingQueue中，使用客户端程序可使用更加方便的读取方式。但使该操作生效并不会在服务端应用中产生什么好处反倒会导致不可预料的内存泄露，默认是关闭的。

    private int throughputCalculationInterval = 3;//每次throughputCalculation（吞吐量计算？）的间隔时间，默认是3秒。

这些设置主要是传输相关的参数设置，默认就可以了，我们经常用到的估计就是BufferSize和idleTime了。

### 3.1.2 IoProcessor

    如果自己不实现IoProcess的话，默认的传递参数是NioProcessor.class（NioDatagramAcceptor构造函数是不需要该参数的）。在父类AbstractPollingIoAcceptor将调用

```java
new SimpleIoProcessorPool<S>(processorClass)
```

方法新建一个SimpleIoProcessorPool的实例。IoProcessor提供一个IoProcessor[处理器数+1] 数组类型的池，未处理每一种IoSessions分类。大多数的Services的实现子类都在内部使用该IoProcessor已达到在多核环境下更好的性能。我们不需要直接使用它。但是，如果在本地JVM中需要存在多个IoServices，那有必要让这些services共享一个SimpleIoProcessorPool。如使用一下方法：

```java
SimpleIoProcessorPool<NioSession> pool = new SimpleIoProcessorPool<NioSession>(NioProcessor.class, 16);

SocketAcceptor acceptor = new NioSocketAcceptor(pool);
SocketConnector connector = new NioSocketConnector(pool);
```

来看一看SimpleIoProcessorPool的构造方法吧：其中，传递的参数processorType为NioProcessor.class

```java
public SimpleIoProcessorPool(Class<? extends IoProcessor<S>> processorType, Executor executor, int size) {
        if (processorType == null) {
            throw new IllegalArgumentException("processorType");
        }

        if (size <= 0) {
            throw new IllegalArgumentException("size: " + size + " (expected: positive integer)");
        }

        // 事实上，<span style="font-family: Arial, Helvetica, sans-serif;">executor在框架内必然是null，除非我们实现自己的子类。</span>
        createdExecutor = (executor == null);

        if (createdExecutor) {
            <span style="color:#ff0000;">this.executor = Executors.newCachedThreadPool();</span>
            //饱和策略选择了由调度者执行的机制。不过newCachedThreadPool不是无限线程池么，怎么又回饱和呢？
	    //难道是并发过载导致线程生成不过来？这样的话，负荷过载就会蔓延到IoServices的主线程，这样的饱和策略真的好么？
	    //我觉得不如抛弃任务，并抛出异常让上层捕获比较好。
            ((ThreadPoolExecutor) this.executor).setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        } else {
            this.executor = executor;
        }

        pool = new IoProcessor[size];

        boolean success = false;
        Constructor<? extends IoProcessor<S>> processorConstructor = null;
        boolean usesExecutorArg = true;

        try {
            // 首先保证至少能生成一个processor：默认的话就是newCachedThreadPool()返回的ThreadPoolExecutor，可自行实现
	    // 这里的初始化策略是：1.首先调用参数类型为ExecutorService 子类的构造函数，
	    // 如果失败则2.调用参数类型为Executor子类的构造函数，
	    // 否则3.调用无参的构造函数
            try {
                try {
                    processorConstructor = processorType.getConstructor(ExecutorService.class);
                    pool[0] = processorConstructor.newInstance(this.executor);
                } catch (NoSuchMethodException e1) {
                    // To the next step...
                    try {
                        processorConstructor = processorType.getConstructor(Executor.class);
                        pool[0] = processorConstructor.newInstance(this.executor);
                    } catch (NoSuchMethodException e2) {
                        // To the next step...
                        try {
                            processorConstructor = processorType.getConstructor();
                            usesExecutorArg = false;
                            pool[0] = processorConstructor.newInstance();
                        } catch (NoSuchMethodException e3) {
                            // To the next step...
                        }
                    }
                }
            } catch (RuntimeException re) {
                LOGGER.error("Cannot create an IoProcessor :{}", re.getMessage());
                throw re;
            } catch (Exception e) {
                String msg = "Failed to create a new instance of " + processorType.getName() + ":" + e.getMessage();
                LOGGER.error(msg, e);
                throw new RuntimeIoException(msg, e);
            }

            if (processorConstructor == null) {
                // Raise an exception if no proper constructor is found.
                String msg = String.valueOf(processorType) + " must have a public constructor with one "
                        + ExecutorService.class.getSimpleName() + " parameter, a public constructor with one "
                        + Executor.class.getSimpleName() + " parameter or a public default constructor.";
                LOGGER.error(msg);
                throw new IllegalArgumentException(msg);
            }

            // 生成其他的Processor，重复上述步骤
            for (int i = 1; i < pool.length; i++) {
                try {
                    if (usesExecutorArg) {
                        pool[i] = processorConstructor.newInstance(this.executor);
                    } else {
                        pool[i] = processorConstructor.newInstance();
                    }
                } catch (Exception e) {
                    // Won't happen because it has been done previously
                }
            }

            success = true;
        } finally {
            if (!success) {
                dispose();
            }
        }
    }
```

    如果创建失败，则调用dispose方法，遍历IoProcess[]数组并释放所有的资源。     有人会问了，那SimpleIoProcessorPool、IoProcess和IoServices的服务之间有什么关联，为什么NioDatagramAcceptor不需要IoProcess呢？我们将在后面详细阐述。

### 3.1.3 Executor

    如果不传入Executor的而实现，mina会默认生成一个newCashedThreadPool，具体代码在非常底层的AbstractIoService类中，即无论是哪种连接方式、服务端、客户端，Executor的默认初始化方式都是一致的。

```java
        if (executor == null) {
            this.executor = Executors.newCachedThreadPool();
            createdExecutor = true;
        } else {
            this.executor = executor;
            createdExecutor = false;
        }
```

又有问题了，这里的Executor和SimpleIoProcessorPool中为每个IoProcess所共享的Executor有什么关系呢？再卖个关子。

## 3.2 IoAcceptor的构造函数做了什么？

搞清楚了传入参数，我们来看下构造器做了哪些初始化的动作。以NioSocketAcceptor为例。上面我们已经知道底层的AbstractIoService类做了executor的初始化，不仅如此，在这之前他还做了元数据判断和初始化监听器链的工作：

```java
        if (!getTransportMetadata().getSessionConfigType().isAssignableFrom(sessionConfig.getClass())) {
            throw new IllegalArgumentException("sessionConfig type: " + sessionConfig.getClass() + " (expected: "
                    + getTransportMetadata().getSessionConfigType() + ")");
        }

        // 创建监听器链，并增加第一个监听器
	// 该监听器的作用就是在service被激活时，设置IoServiceStatistics的
	// setLastReadTime、setLastWriteTime、setLastThroughputCalculationTime为激活时间
        listeners = new IoServiceListenerSupport(this);
        listeners.add(serviceActivationListener);
```



同时，AbstructIoService类还实例化了IoFilterChainBuilder 对象，用于维护过滤器链。 

```java
private IoFilterChainBuilder filterChainBuilder = new DefaultIoFilterChainBuilder();
```

有必要提一下IoServiceListenerSupport，此类是所有监听器对象的持有类并管理着监听器的回调入口，同时还管理着当前Service的所有session。

```java
    /** 当前service的引用 **/
    private final IoService service;

    /** 基于COW线程保护的listeners集合 */
    private final List<IoServiceListener> listeners = new CopyOnWriteArrayList<IoServiceListener>();

    /** 线程安全的session集合 */
    private final ConcurrentMap<Long, IoSession> managedSessions = new ConcurrentHashMap<Long, IoSession>();

    /**  session集合的只读视图  **/
    private final Map<Long, IoSession> readOnlyManagedSessions = Collections.unmodifiableMap(managedSessions);
```

    以其中的方法fireServiceActivated()为例，此方法是Service被激活时调用 

```java
    public void fireServiceActivated() {
        if (!activated.compareAndSet(false, true)) {
            // 如果已经激活，则返回
            return;
        }
	//设置激活时间
        activationTime = System.currentTimeMillis();

        // 观察者模式，回调观察者listener的serviceActivated()方法
        for (IoServiceListener listener : listeners) {
            try {
                listener.serviceActivated(service);
            } catch (Throwable e) {
                ExceptionMonitor.getInstance().exceptionCaught(e);
            }
        }
    }
```

监听器的每个方法都是在IoServiceListenerSupport里被回调的：

```java
fireServiceActivated()
fireServiceDeactivated()
fireSessionCreated(IoSession)
fireSessionDestroyed(IoSession)
```

同时，service的getActivationTime()、isActive()方法等跟生命周期相关的信息获取方法，都是通过IoServiceListenerSupport的同名方法获得，可以说，IoServiceListenerSupport贯穿了整个IoService的生命周期。

至此，AbstructIoService完成了他的工作，接着AbstructIoAcceptor把defaultLocalAddresses设置为null，**不知道这个defaultLocalAddresses的什么用？**

```java
super(sessionConfig, executor);
defaultLocalAddresses.add(null);
```

    AbstructIoAcceptor老爹唱罢，儿子AbstractPollingIoAcceptor登场。这儿子就干了一件正经事儿，回调了孙子NioSocketAccept的init()方法。

```java
protected void init() throws Exception {
    selector = Selector.open();
}
```

于是，选择器开启。在NIO的时代，Selector.open后紧接着就是注册了channel了，然后select()阻塞起等待连接了。而我们的mina接着是怎么做的呢？我们先跳过fliter链的设置和连接参数的设置，进入 

```java
//绑定端口
acceptor.bind(new InetSocketAddress(PORT));
```

在一步老精彩了，儿子AbstractPollingIoAcceptor再也不是打酱油的角色了。老爹霹雳啪啦一大串模板方法，引出儿子关键的一步：

```java
...
try {
    <span style="color:#ff0000;">Set<SocketAddress> addresses = bindInternal(localAddressesCopy);</span>

    synchronized (boundAddresses) {
        boundAddresses.addAll(addresses);
    }
}
...
```

我们看看bindInternal在AbstractPollingIoAcceptor中做了什么 

```java
    protected final Set<SocketAddress> bindInternal(List<? extends SocketAddress> localAddresses) throws Exception {
	// 创建了与selector注册相关的任务，并添加至registerQueue中
        AcceptorOperationFuture request = new AcceptorOperationFuture(localAddresses);

        registerQueue.add(request);

        // 在私有变量executor线程池中启动具体的Acceptor执行线程
        <span style="color:#ff0000;">startupAcceptor();</span>

        // As we just started the acceptor, we have to unblock the select()
        // in order to process the bind request we just have added to the
        // registerQueue.
        try {
            lock.acquire();

            // 让线程池任务运行Acceptor任务
	    // 实际上不需要这一步，我们知道wakeup()方法，如果当前没有select()执行，则会解除下一次select()阻塞状态
            Thread.sleep(10);
	    // 这个wakeup()方法很巧妙，因为Acceptor监听线程已经开启
	    // 但是没有连接接入，因此select()方法是阻塞的，
	    // 为了让必要的request完成操作，调用一次wakeup();
            wakeup();
        } finally {
            lock.release();
        }

        // 等待request任务完成注册
        request.awaitUninterruptibly();

        if (request.getException() != null) {
            throw request.getException();
        }

        // Update the local addresses.
        // setLocalAddresses() shouldn't be called from the worker thread
        // because of deadlock.
        Set<SocketAddress> newLocalAddresses = new HashSet<SocketAddress>();

        for (H handle : boundHandles.values()) {
            newLocalAddresses.add(localAddress(handle));
        }

        return newLocalAddresses;
    }
```

这里我们看到，bindInternal()方法主要完成了selector的注册和启动。其中主要的方法是startupAcceptor();

```java
    private void startupAcceptor() throws InterruptedException {
        if (!selectable) {
            registerQueue.clear();
            cancelQueue.clear();
        }
        // 如果acceptor已经启动，则什么也不做，否则启动之。
        Acceptor acceptor = acceptorRef.get();

        if (acceptor == null) {
            lock.acquire();
            <span style="color:#ff0000;">acceptor = new Acceptor();</span>

            if (acceptorRef.compareAndSet(null, acceptor)) {
                executeWorker(acceptor);
            } else {
                lock.release();
            }
        }
    }
```

Acceptor继承自Runnable，用于提交到线程池中执行，而这个线程池就是作为参数（或者默认newCashedThreadPool返回）的Executor。Acceptor线程任务以循环的方式调用select()，第一次运行时将注册选择器。后续主要完成3个工作：

```java
	// 将 selector 设置监听 OP_ACCEPT，只在选择器第一次运行执行该代码，该方法依赖registerQueue队列
	nHandles += registerHandles();		    
                    
	// 如有连接接入，处理session
	processHandles(selectedHandles());
                    
	// 关闭selector监听，若关闭，则循环break跳出，线程任务结束，<span style="font-family: Arial, Helvetica, sans-serif;">该方法依赖cancelQueue队列</span>
	nHandles -= unregisterHandles();
```

启动和关闭selector的操作比较NIO，就不解释了，直接截取registerHandles()和unregisterHandles()部分方法源码

```java
    protected ServerSocketChannel open(SocketAddress localAddress) throws Exception {
        // Creates the listening ServerSocket
        ServerSocketChannel channel = ServerSocketChannel.open();

        boolean success = false;

        try {
            // This is a non blocking socket channel
            channel.configureBlocking(false);

            // Configure the server socket,
            ServerSocket socket = channel.socket();

            // Set the reuseAddress flag accordingly with the setting
            socket.setReuseAddress(isReuseAddress());

            // and bind.
            socket.bind(localAddress, getBacklog());

            // Register the channel within the selector for ACCEPT event
            channel.register(selector, SelectionKey.OP_ACCEPT);
            success = true;
        } finally {
            if (!success) {
                close(channel);
            }
        }
        return channel;
    }

    protected void close(ServerSocketChannel handle) throws Exception {
        SelectionKey key = handle.keyFor(selector);

        if (key != null) {
            key.cancel();
        }

        handle.close();
    }
```

processHandles(selectedHandles());

而最最关键也最最精彩的则是处理客户端连接的部分，我们下节继续。

版权声明：本文为博主原创文章，未经博主允许不得转载。
