---
title: mina学习笔记五：做嫁衣的IoFilter和IoListener
date: 2016-09-08 20:12:17
categories:
- mina
tags:
- java mina
toc: true
author:
comments:
original:
permalink: 
---

[原文：http://blog.csdn.net/yoara/article/details/37597141](http://blog.csdn.net/yoara/article/details/37597141)    

总有那么一群人，他们默默无闻的工作在自己的岗位上，你永远都接触不到他们，可是离了他们完整的做好一件事。譬如只注意在荧光屏上的明星，却不知道谁给他们打得灯光、谁给他们踩得位、谁给他们化妆端茶送水等等。IoFilter也是这群人中的一员，每个session的请求和事件离不了他们，IoFilter在IoService和IoHandler之间提供了各个层面的切面支持，让其他两个组件安心做自己的事而不用关注其他细节。软件工程的魅力也在于此——解耦。

# 1.IoFilter的一家子

IoFilter家族的类图结构非常简单明快，mina非常贴心的为广大使用者提供了丰富的Filter实现子类，很多标准服务我们都不需要自己实现。 

图5.1 IoFilter类图     我们先简单介绍一下这个家族成员和他们各自的作用吧：
| Filter                       | class                                    | Description                              |
| ---------------------------- | ---------------------------------------- | ---------------------------------------- |
| Blacklist                    | [BlacklistFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/firewall/BlacklistFilter.html) | 黑名单过滤器                                   |
| BufferedWrite                | [BufferedWriteFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/buffer/BufferedWriteFilter.html) | 发送缓存过滤器，缓存发送的消息，避免短小消息频繁发送               |
| Compression                  | [CompressionFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/compression/CompressionFilter.html) | 数据压缩过滤器                                  |
| ConnectionThrottle           | [ConnectionThrottleFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/firewall/ConnectionThrottleFilter.html) | 连接控制过滤器，对同一IP地址频繁的创建连接的时间间隔进行控制          |
| ErrorGenerating              | [ErrorGeneratingFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/errorgenerating/ErrorGeneratingFilter.html) | 花数据过滤器，增加、修改、移除接受的数据包内容，可作为加密的方式         |
| Executor                     | [ExecutorFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/executor/ExecutorFilter.html) | 处理线程池过滤器，让每个请求或者事件都通过线程池去执行              |
| FileRegionWrite              | [FileRegionWriteFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/stream/FileRegionWriteFilter.html) | 文件转换过滤器，通常应该由IoProcess做这件事，但若需要压缩或者修改时，可通过过滤器链之间的配合来实现 |
| KeepAlive                    | [KeepAliveFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/keepalive/KeepAliveFilter.html) | 心跳包过滤器，在idle状态时发送心跳包，并能对超时进行处理           |
| Logging                      | [LoggingFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/logging/LoggingFilter.html) | 日志记录过滤器，最常用之一                            |
| MDC Injection                | [MdcInjectionFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/logging/MdcInjectionFilter.html) | 日志信息注入过滤器，MDC(Mapped Diagnostic Context有译作线程映射表)是日志框架维护的一组信息键值对，可向日志输出信息中插入一些想要显示的内容。 |
| Noop                         | [NoopFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/util/NoopFilter.html) | 用作内部测试的filter，什么也没做                      |
| Profiler                     | [ProfilerTimerFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/statistic/ProfilerTimerFilter.html) | 时间分析过滤器，记录各种事件消耗的时间                      |
| ProtocolCodec                | [ProtocolCodecFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/codec/ProtocolCodecFilter.html) | 编解码过滤器，最常用之二                             |
| Proxy                        | [ProxyFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/proxy/filter/ProxyFilter.html) | 是IoConnector在连接握手时自动加入的过滤器，握手成功后就透明了     |
| Reference counting           | [ReferenceCountingFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/util/ReferenceCountingFilter.html) | 引用数过滤器，能记录该过滤器被加入或移除过滤器链的次数，真实使用是继承他。    |
| RequestResponse              | [RequestResponseFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/reqres/RequestResponseFilter.html) | 继承WriteRequest                           |
| SessionAttributeInitializing | [SessionAttributeInitializingFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/util/SessionAttributeInitializingFilter.html) | 初始化过滤器                                   |
| StreamWrite                  | [StreamWriteFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/stream/StreamWriteFilter.html) | InputStream直接转换成IoBuffer的过滤器             |
| SslFilter                    | [SslFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/ssl/SslFilter.html) | TCP/IP层面的SSl加解密过滤器                       |
| WriteRequest                 | [WriteRequestFilter](http://mina.apache.org/mina-project/xref/org/apache/mina/filter/util/WriteRequestFilter.html) | 简化IoFilter IoEventType.WRITE事件的实现的抽象过滤器  |

还有两个FIlter是mina自带并且不为用户管理的，当生成过滤器链自动注入：

*   HeadFilter（当发生write操作时，将写buffer加入到session.scheduledWriteMessages队列，并执行发送调用IoProcessor执行write()操作），位于过滤器链头。
*   TailFilter（当所有过滤器都处理完后，他将调用IoHandler的对应方法）。位于过滤器链尾。

# 2.IoFilter执行顺序

搞清楚了 

![图5.2 过滤器调用顺序图](http://opesdt6ii.bkt.clouddn.com/17-5-5/15329899-file_1493966521689_24fe.png)

在事件端，我们以messageSent事件被执行的代码来详解下过程；在操作端我们的例子是session.write。

## 2.1 messageSent顺序调用过滤器链

    当IoProcessor的remove(session)方法被调用时，该session将被添加进Processor内部的removingSessions的ConcurrentLinkedQueue等待被移除。

```java
public final void remove(S session) {  
    scheduleRemove(session);  
    startupProcessor();  
}  
  
private void scheduleRemove(S session) {  
    removingSessions.add(session);  
}  
```

当IoProcessor工作线程执行一个循环，调用removeSessions()方法，状态为SessionState.OPENED的session将被移除，移除的代码中就包括刷新消息队列，其中代码如下，从而引发一次过滤器链的调用。 

```java
if (message instanceof IoBuffer) {  
    IoBuffer buf = (IoBuffer) message;  
    if (buf.hasRemaining()) {  
        buf.reset();  
        failedRequests.add(req);  
    } else {  
        IoFilterChain filterChain = session.getFilterChain();  
        filterChain.fireMessageSent(req);  
    }  
} else {  
    failedRequests.add(req);  
}  
```

最终调用的是TailFilter，他的代码如下：

```java
private static class TailFilter extends IoFilterAdapter {  
  
        public void sessionCreated(NextFilter nextFilter, IoSession session) throws Exception {  
            session.getHandler().sessionCreated(session);  
        }  
  
        public void sessionOpened(NextFilter nextFilter, IoSession session) throws Exception {  
            session.getHandler().sessionOpened(session);  
        }  
  
        public void sessionClosed(NextFilter nextFilter, IoSession session) throws Exception {  
            AbstractIoSession s = (AbstractIoSession) session;  
            try {  
                s.getHandler().sessionClosed(session);  
            } finally {  
                try {  
                    s.getWriteRequestQueue().dispose(session);  
                } finally {  
                    try {  
                        s.getAttributeMap().dispose(session);  
                    } finally {  
                        try {  
                            // Remove all filters.  
                            session.getFilterChain().clear();  
                        } finally {  
                            if (s.getConfig().isUseReadOperation()) {  
                                s.offerClosedReadFuture();  
                            }  
                        }  
                    }  
                }  
            }  
        }  
  
        public void sessionIdle(NextFilter nextFilter, IoSession session, IdleStatus status) throws Exception {  
            session.getHandler().sessionIdle(session, status);  
        }  
  
        public void exceptionCaught(NextFilter nextFilter, IoSession session, Throwable cause) throws Exception {  
            AbstractIoSession s = (AbstractIoSession) session;  
            try {  
                s.getHandler().exceptionCaught(s, cause);  
            } finally {  
                if (s.getConfig().isUseReadOperation()) {  
                    s.offerFailedReadFuture(cause);  
                }  
            }  
        }  
  
        public void messageReceived(NextFilter nextFilter, IoSession session, Object message) throws Exception {  
            session.getHandler().messageReceived(s, message);  
        }  
  
        public void messageSent(NextFilter nextFilter, IoSession session, WriteRequest writeRequest) throws Exception {  
            session.getHandler().messageSent(session, writeRequest.getMessage());  
        }  
  
        public void filterWrite(NextFilter nextFilter, IoSession session, WriteRequest writeRequest) throws Exception {  
            nextFilter.filterWrite(session, writeRequest);  
        }  
  
        public void filterClose(NextFilter nextFilter, IoSession session) throws Exception {  
            nextFilter.filterClose(session);  
        }  
    }  
```

## 2.2 session.write逆序调用过滤器链

在Handler中持有session的对象，调用session.write(date.toString());，session会调用具体方法：

```java
AbstractIoSession.write(Object message) {  
    ...  
        IoFilterChain filterChain = getFilterChain();  
        filterChain.fireFilterWrite(writeRequest);  
    ...  
    }  
```

```java
DefaultIoFilterChain.fireFilterWrite(WriteRequest writeRequest) {  
        Entry tail = this.tail;  
        callPreviousFilterWrite(tail, session, writeRequest);  
    } 
```

触发逆序的过滤器链，最终到HeadFilter中：

```java
...其他Filter  
HeadFilter.filterWrite(NextFilter nextFilter, IoSession session, WriteRequest writeRequest) throws Exception {  
        AbstractIoSession s = (AbstractIoSession) session;  
        ...  
            WriteRequestQueue writeRequestQueue = s.getWriteRequestQueue();  
  
            if (!s.isWriteSuspended()) {  
                if (writeRequestQueue.size() == 0) {  
                    // We can write directly the message  
                    s.getProcessor().write(s, writeRequest);  
                } else {  
                    s.getWriteRequestQueue().offer(s, writeRequest);  
                    s.getProcessor().flush(s);  
                }  
            } else {  
                s.getWriteRequestQueue().offer(s, writeRequest);  
            }  
        }  
```

# 3.IoHandler API

    Handler虽然是直面用户的对象，但由上述可知，他其实只是整个操作链上最后的一环，也是最简单的一环，给出IoHander的API清单

*   sessionCreated(IoSession)：session创建时调用
*   sessionOpened(IoSession)：session打开时调用，实际上是在sessionCreated后立即执行
*   sessionClosed(IoSession)：session关闭时调用
*   sessionIdle(IoSession, IdleStatus)：session空闲时调用
*   exceptionCaught(IoSession, Throwable)：异常时调用
*   messageReceived(IoSession, Object)：接收到新的request时调用
*   messageSent(IoSession, Object)：发送在消息队列中未完成的消息时调用

# 4\. IoFilter API

和Handler有一部分一致：

*   init() filter初始化方法，在下面4个on方法调用之前必须先被调用。
*   destroy() 该方法被ReferenceCountingFilter引用到，如果不继承ReferenceCountingFilter，不需实现该方法。
*   onPreAdd(IoFilterChain, String, NextFilter) 过滤器被添加之前调用。
*   onPostAdd(IoFilterChain, String, NextFilter) 过滤器被添加之后调用。
*   onPreRemove(IoFilterChain, String, NextFilter) 过滤器被移除之前调用。
*   onPostRemove(IoFilterChain, String, NextFilter) 过滤器被移除之后调用。
*   sessionCreated(NextFilter, IoSession) 
*   sessionOpened(NextFilter, IoSession)
*   sessionClosed(NextFilter, IoSession)
*   sessionIdle(NextFilter, IoSession, IdleStatus)
*   exceptionCaught(NextFilter, IoSession, Throwable)
*   messageReceived(NextFilter, IoSession, Object)
*   messageSent(NextFilter, IoSession, WriteRequest)
*   filterClose(NextFilter, IoSession)  session.close(）方法中调用。
*   filterWrite(NextFilter, IoSession, WriteRequest) session.write(）方法中调用。

# 5. IoListener

上面用了比较多的篇幅介绍了IoFilter，现在我们也该讲讲二号后台人物——IoListerner。在mina中主要有两类，一个是IoServiceListener，另一个就是IoFutureListener。

## 5.1 IoServiceListener    

    IoServiceListener负责Service的各个生命周期节点事件通知。我们曾在学习笔记三中提到过IoServiceListenerSupport，此类对象是该service所有IoServiceListener对象的持有类并管理着监听器的回调入口，同时还管理着当前Service的所有session。当时没有详细的介绍，现在我们重点说说这个类在做嫁衣方面的独到之处。     IoServiceListenerSupport是在AbstractIoService的构造函数中被生成的，也就是说，恭喜，无论你是Acceptor还是Connector，你都拥有这个强大的助手了。为了支持监听器的功能，可爱的祖父AbstractIoService还贴心的为我们实现好了addListener(IoServiceListener listener)和removeListener(IoServiceListener listener)方法。     我们先看看的API吧：

*   serviceActivated(IoService) 当service生效时被调用。当新的service被bind时或第一个session生成时，IoServiceListenerSupport.fireServiceActivated被调用同时调用此方法。

*   serviceIdle(IoService, IdleStatus) 当service空闲时被调用，不过此方法没有在mina中真正使用。
*   serviceDeactivated(IoService) 当service失效时被调用。当service被unbind或最后个session注销时，IoServiceListenerSupport.fireServiceDeactivated被调用同时调用此方法。

*   sessionCreated(IoSession) 当新的session生成时被调用。IoServiceListenerSupport.fireSessionCreated
*   sessionDestroyed(IoSession) 当session被注销时调用。IoServiceListenerSupport.fireSessionDestroyed

如此一看，还是很简单清晰的，监听器可以在任意时刻被添加或移除。我们的使用方法也很简单：

```java
IoServiceListener listener = new IoServiceListener() {  
    public void sessionDestroyed(IoSession session) throws Exception {  
    }  
    public void sessionCreated(IoSession session) throws Exception {  
    }  
    public void serviceIdle(IoService service, IdleStatus idleStatus)  
            throws Exception {  
    }  
    public void serviceDeactivated(IoService service) throws Exception {  
    }  
    public void serviceActivated(IoService service) throws Exception {  
    }  
};  
acceptor.addListener(listener);  
acceptor.removeListener(listener);  

```

## 5.1 IoFutureListener    

还有一种Listener就是IoFutureListener，他在学习笔记四种也出现过，确实也比较简单，能在IoFuture任务完成后被调用，唯一的API：

```java
public interface IoFutureListener<F extends IoFuture> extends EventListener {    
    void operationComplete(F future);    
}
```

因为大部分的future都被mina集成在框架里，所以future的listener支持一项特性：若任务已经执行完后添加的listener将被立刻执行。我们先看看future是怎么实现该功能的。

```java
public IoFuture addListener(IoFutureListener<?> listener) {  
    if (listener == null) {  
        throw new IllegalArgumentException("listener");  
    }  
  
    boolean notifyNow = false;  
    synchronized (lock) {  
        if (ready) {  
            notifyNow = true;  
        } else {  
            if (firstListener == null) {  
                firstListener = listener;  
            } else {  
                if (otherListeners == null) {  
                    otherListeners = new ArrayList<IoFutureListener<?>>(1);  
                }  
                otherListeners.add(listener);  
            }  
        }  
    }  
是这里了！如果ready完成了，则马上执行  
    if (notifyNow) {  
        notifyListener(listener);  
    }  
    return this;  
}  
```

那不用说就知道啦，在notiffyListener中将会回调监听器的方法。

# 6\. 结语

mina框架核心部件基本上都讲的差不多了，看看如果有需要补余的，留在下一章节。

版权声明：本文为博主原创文章，未经博主允许不得转载。
