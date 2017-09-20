---
title: mina学习笔记二：从官方例子开始
date: 2016-09-08 20:12:14
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

[原文：http://blog.csdn.net/yoara/article/details/37324821    ](http://blog.csdn.net/yoara/article/details/37324821)

本节内容只介绍mina总体架构，同时给出官方的几个实用TCP、UDP的经典服务端客户端例子。

# mina体系结构

mina位于用户程序和网络处理之间，将用户从复杂的网络处理中解耦，那我们就可以更加关注业务领域。

![图2.1 mina鸟瞰图](http://opesdt6ii.bkt.clouddn.com/17-5-5/73185035-file_1493951689595_62d1.png)

让我们再深入进去，mina框架内部的各个组件是怎么协调工作的呢？ ![mina组件结构图](http://opesdt6ii.bkt.clouddn.com/17-5-5/1757032-file_1493951826742_1606b.png?imageView/2/w/450)

显然，mina框架被分成了主要的3个组件部分：

*   I/O Service，具体提供服务的组件。
*   I/O Filter Chain，过滤器链，用于支持各种切面服务。
*   I/O Handler，用于处理用户的业务逻辑。

相对应的，为了创建一个基于mina的应用程序，我们需要：

*   创建I/O Service ：可选择mina提供的Services如（*Acceptor）或实现自己的Service。
*   创建I/O Filter ：同样可以选择mina提供的各类filter，也可以实现自己的编解码过滤器等。
*   实现I/O Handler，实现Handler接口，处理各种消息。

# 服务端结构

服务端的作用就是开启监听端口，等待请求的到来、处理他们、以及将发送对请求的响应。同时，服务端会为每个连接创建session，在session周期内提供各种精度的服务，比如连接创建时(sessionCreated(IoSession session))、连接等待时(sessionIdle(IoSession session, IdleStatus status))、连接销毁时(sessionClosed(IoSession session))等。mina的api为TCP/UDP提供的一致性Server端操作。 ![Server结构图](http://opesdt6ii.bkt.clouddn.com/17-5-5/13929582-file_1493951496846_fb87.png)

*   IOAcceptor 监听来自网络的请求。
*   当新的连接建立时，一个新的session会被创建，该session用作对同一IP/端口组合的客户端提供服务。
*   数据包需经过一系列的过滤器，这些过滤器可用来修改数据包的内容（如转换对象、添加或修改信息等），其中将原始字节流转换成POJO对象是非常有用的。当然这需要解编码器提供支持。
*   最后这些数据包或转化后的对象将交由IOHandler处理，我们将实现IOHandler用于处理具体的业务逻辑。

# 客户端结构

客户端需要连接服务端，发送请求并处理响应。实际上客户端的结构和服务端极其相似。 

![图2.4 客户端结构](http://opesdt6ii.bkt.clouddn.com/17-5-5/95294309-file_1493953292315_102b1.png)

*   客户端首先需要创建IOConnector对象，绑定服务端的IP和端口。
*   一旦连接成功，一个于本次连接绑定的session对象将被创建。
*   客户端发送给服务端的请求都需要经过一系列的fliter。
*   同样，响应消息的接受也会经过一系列的filter再到IOHandler被处理。

所以整体上，mina提供良好的一致性调用和封装结构。在使用mina创建基于网络的程序应用时，投入的学习成本比较低。

# TCP连接的例子

依赖的Jar包：

*   MINA 2.x Core
*   JDK 1.5 或以上
*   SLF4J 1.3.0 或以上
    *   Log4J 1.2： slf4j-api.jar, slf4j-log4j12.jar, Log4j 1.2.x
    *   Log4J 1.3： slf4j-api.jar, slf4j-log4j13.jar, Log4j 1.3.x（注意，请确认log4j的版本）

    TCP Server端代码：
```java
public class MinaTimeTest {
	private static final int PORT = 9123;

	public static void main(String[] args) throws IOException {
		//首先，我们为服务端创建IoAcceptor，NioSocketAcceptor是基于NIO的服务端监听器
		IoAcceptor acceptor = new NioSocketAcceptor();
		//接着，如结构图示，在Acceptor和IoHandler之间将设置一系列的Fliter
		//包括记录过滤器和编解码过滤器。其中TextLineCodecFactory是mina自带的文本解编码器
		acceptor.getFilterChain().addLast("logger", new LoggingFilter());
		acceptor.getFilterChain().addLast("codec",
				new ProtocolCodecFilter(new TextLineCodecFactory(Charset.forName("UTF-8"))));
		//配置事务处理Handler，将请求转由TimeServerHandler处理。
		acceptor.setHandler(new TimeServerHandler());
		//配置Buffer的缓冲区大小
		acceptor.getSessionConfig().setReadBufferSize(2048);
		//设置等待时间，每隔IdleTime将调用一次handler.sessionIdle()方法
		acceptor.getSessionConfig().setIdleTime(IdleStatus.BOTH_IDLE, 10);
		//绑定端口
		acceptor.bind(new InetSocketAddress(PORT));
	}
	
	static class TimeServerHandler extends IoHandlerAdapter {

		public void exceptionCaught(IoSession session, Throwable cause)
				throws Exception {
			cause.printStackTrace();
		}

		public void messageReceived(IoSession session, Object message)
				throws Exception {
			String str = message.toString();
			if (str.trim().equalsIgnoreCase("quit")) {
				session.close(false);
				return;
			}
			Date date = new Date();
			session.write(date.toString());
			System.out.println("Message written...");
		}
		
		public void sessionIdle(IoSession session, IdleStatus status)
				throws Exception {
			System.out.println("IDLE " + session.getIdleCount(status));
		}
	}
}
```

    TCP Client端代码：
```java
public class TimeClient {
	private static final String HOSTNAME = "127.0.0.1";
	private static final int PORT = 9123;
	private static final long CONNECT_TIMEOUT = 30 * 1000L; // 30 seconds

	public static void main(String[] args) throws Throwable {
		//创建Connector连接器
		NioSocketConnector connector = new NioSocketConnector();
		//设置连接超时时间
		connector.setConnectTimeoutMillis(CONNECT_TIMEOUT);
		
		connector.getFilterChain().addLast("codec",
				new ProtocolCodecFilter(new TextLineCodecFactory()));
		connector.getFilterChain().addLast("logger", new LoggingFilter());
		//非常简单的处理Handler，向服务器发送一个数字
		connector.setHandler(new ClientSessionHandler(1));
		IoSession session = null;

		try {
			//连接远程主机，设置IP和端口
			ConnectFuture future = connector.connect(new InetSocketAddress(
					HOSTNAME, PORT));
			//等待连接建立
			future.awaitUninterruptibly();
			//连接建立后返回会话session
			session = future.getSession();
		} catch (RuntimeIoException e) {
			System.err.println("Failed to connect.");
			e.printStackTrace();
			Thread.sleep(5000);
		} finally{
			if(session!=null){
				//等待本次连接通话结束，不可中断式的阻塞等待
				session.getCloseFuture().awaitUninterruptibly();
			}
		}
		//关闭连接
		connector.dispose();
	}
	static class ClientSessionHandler extends IoHandlerAdapter {
	    private final int value;
	    public ClientSessionHandler(int value) {
	        this.value = value;
	    }
	    @Override
	    public void sessionOpened(IoSession session) {
	    	session.write(value);
	    }

	    @Override
	    public void messageReceived(IoSession session, Object message) {
	        System.out.println(message);
	        session.close(true);
	    }

	    @Override
	    public void exceptionCaught(IoSession session, Throwable cause) {
	        session.close(true);
	    }
	}
}
```


# UDP连接的例子

UDP服务端：

```java
public class UdpServer {
	private final static int PORT = 9234;
	public static void main(String[] args) throws IOException {
		//创建UDPAcceptor
		NioDatagramAcceptor acceptor = new NioDatagramAcceptor(null);
		//这次不设置字符解编码器filter，消息直接用Buffer字节流传递
		acceptor.getFilterChain().addLast("logger", new LoggingFilter());
		acceptor.setHandler(new UDPHandler());
		acceptor.getSessionConfig().setIdleTime(IdleStatus.BOTH_IDLE, 10);
		acceptor.getSessionConfig().setReuseAddress(true);//<span style="font-family: Arial, Helvetica, sans-serif;">NioDatagramAcceptor 设置为null，才能重复使用端口</span>
		acceptor.bind(new InetSocketAddress(PORT));
	}
	static class UDPHandler extends IoHandlerAdapter{
		@Override
		public void messageReceived(IoSession session, Object message)
				throws Exception {
			IoBuffer buffer = (IoBuffer)message;
			System.out.println(buffer.getLong());
			session.close(false);
		}
		@Override
		public void sessionIdle(IoSession session, IdleStatus status)
				throws Exception {
			System.out.println("IDLE " + session.getIdleCount(status));
		}
	}
}
```


UDP客户端：

```java
public class UdpClient {
	private static IoSession session = null;
	public static void main(String[] args) {
		NioDatagramConnector connect = new NioDatagramConnector();
		connect.setHandler(new UDPClientHandler());
		try{
			ConnectFuture future = connect.connect(new InetSocketAddress("127.0.0.1",9234));
			future.awaitUninterruptibly();
			session = future.getSession();
			//增加连接建立完成后的监听器。
			//若在建立完成后才添加监听器，监听器将马上执行
			future.addListener(new IoFutureListener<ConnectFuture>(){
	            public void operationComplete(ConnectFuture future) {
	                if( future.isConnected() ){
	                    try {
	                        sendData();
	                    } catch (InterruptedException e) {
	                        e.printStackTrace();
	                    }
	                } else {
	                    System.out.println(("Not connected...exiting"));
	                }
	            }
	        });
		}catch(Exception e){
			
		}finally{
			if(session!=null){
        	    session.getCloseFuture().awaitUninterruptibly();
        	}
		}
		connect.dispose();
	}

    private static void sendData() throws InterruptedException {
        long free = Runtime.getRuntime().freeMemory();
        IoBuffer buffer = IoBuffer.allocate(8);
        buffer.putLong(free);
        buffer.flip();
        session.write(buffer);
        //因为是UDP，客户端需主动关闭连接
        session.close(false);
    }
	static class UDPClientHandler extends IoHandlerAdapter{}
}
```

# 结语

这一节介绍了mina的组件结构和框架体系，用几个案例简单介绍了下mina的使用方法。在日常的开发中，其实已经足够了。

版权声明：本文为博主原创文章，未经博主允许不得转载。
