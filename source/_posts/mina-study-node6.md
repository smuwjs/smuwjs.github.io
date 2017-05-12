---
title: mina学习笔记六：补刀
date: 2016-09-08 20:12:18
categories:
- mina
tags:
- java 
- mina
toc: true
author:
comments:
original:
permalink: 
---

[原文：http://blog.csdn.net/yoara/article/details/37612703](http://blog.csdn.net/yoara/article/details/37612703)

补充一些可能没讲到的细节。

# 1.WriteRequest

每一个session.write()操作都会被封装成writeRequest并被添加到WriteRequestQueue队列中。过滤器的每一次责任链流转都是以writeRequest为传参的。我们的老祖父AbstractIoService在实例化时，自带了一个非常重要的属性字段。是不得不注意的参数哟，虽然他对我们基本上是透明的。

```java
private IoSessionDataStructureFactory sessionDataStructureFactory = new DefaultIoSessionDataStructureFactory();  
```



这个参数就包括了两个鸟不得的结构体，一个就是attribute的map，另一个就是我们可爱的WriteRequestQueue了。

# 2.IoBuffer

IoBuffer是mina框架对Nio中Buffer的再封装，Buffer应已经是很好用了，mina为什么还要继续封装呢？他给出的原因有2：

*   Buffer没有提供实用的工具型getter和setter方法，比如fill, get/putString, and get/putAsciiInt() 。
*   原生的Buffer是固定长度的，非常不利于用于操作可变长度的数据。

说实话，这两个原因看上去都不怎么靠谱，要我说，真正的原因就是，框架没有点自己的封装就不是高大上了嘛~哈哈。

mina自身也有点后悔这样的选择，据说在mina3中将采取不同的策略。下面是官方的原话：

>     This will change in MINA 3\. The main reason why MINA has its own wrapper on top of nio ByteBuffer is to have extensible buffers.This was a very bad decision.Buffers are just buffers : a temporary place to store temporary data, before it is used. Many other solutions exist, like defining a wrapper which relies on a list of NIO ByteBuffers, instead of copying the existing buffer to a bigger one just because we want to extend the buffer capacity. It might also be more comfortable to use an InputStream instead of a byte buffer all along the filters, as it does not imply anything about the nature of the stored data : it can be a byte array, strings, messages... Last, not least, the current implementation defeat one of the target : zero-copy strategy (ie, once we have read the data from the socket, we want to avoid a copy being done later). As we use extensible byte buffers, we will most certainly copy those data if we have to manage big messages. Assuming that the MINA ByteBuffer is just a wrapper on top of NIO ByteBuffer, this can be a real problem when using direct buffers.

# 3.用到的设计模式

## 3.1抽象工厂ProtocolCodecFactory

机智的过滤器ProtocolCodecFilter需要机智的解编码器。以TextLineCodecFactory为例： 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/96615731-file_1493967211098_8576.jpg)

## 3.2 建造者模式

这里的导演类就是我们添加filter的设置代码，而产生出的产品则是顺序不同的filter链。 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/13183021-file_1493967252599_17ab6.jpg)

## 3.3 适配器模式

    ![](http://opesdt6ii.bkt.clouddn.com/17-5-5/66833683-file_1493967343756_e360.jpg)

## 3.4 装饰者模式

writeRequest这一家子就是很好的例子。

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/58251518-file_1493967389693_127e8.jpg)

## 3.5 外观模式

我们劳苦功高的IoServiceListenerSupport，为filter和listener提供了一致性的操作接口 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/1227133-file_1493967428109_d710.jpg)

## 3.6 桥接模式

这就太多了，但凡存在属性字段是接口的，几乎都是桥接模式。只画个类图示意： 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/96996656-file_1493967457215_11800.jpg)

## 3.7 组合模式

过滤器链里面的用到的模式之一。 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/44112552-file_1493967480186_154c.jpg)

## 3.8 享元模式

每个在数组pool里的IoProcessor都是一个享元。

## 3.9 模板方法

大量的实现都用到了模板方法，这个模式是子类延迟实现的典范。 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/55458890-file_1493967502366_8754.jpg)

## 3.10 观察者模式

在背后默默支持我们的IoServiceListenerSupport，就担负着通知观察者的重任。 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/12974433-file_1493967526941_cfe7.jpg)

## 3.11 策略模式

每一种filter的具体实现都是一种策略

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/54278099-file_1493967569443_f597.jpg)

## 3.12 责任链模式

在过滤器链中，每个节点都持有对下一个节点的引用。其中节点并非filter实例，而是封装了filter、上一个filter、下一个filter、下一个filter操作对象的Entry对象。 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/76149157-file_1493967589076_4720.jpg)

## 3.13 命令模式

比如session.wirte，发送的消息将被封装到WriteRequest中，最终经过一系列的过滤器被提交到WriteRequestQueue中等待发送。IoProcessor通过轮询将消息队列flush出去。在这里，消息的请求者是session、消息发送实现者是Processor，而消息及消息处理的future被封装到WriteRequest这个对象中。     一般命令模式的主体会持有执行者的引用，命令模式通过调用执行者的方法执行命令，这里是放到一个队列里被轮询执行，所以不能算是完整的命令模式。但命令模式关注的是“行为请求者”和“行为执行者”的解耦，并封装调用请求的变化，这里是符合这个思想的。 

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/25316091-file_1493967611506_333b.jpg)

# 4.为什么NioDatagramAcceptor不使用SimpleIoProcessorPool

我们知道在前面讲到NioSocketAcceptor在初始化时会生成SimpleIoProcessorPool的IoProcessor的池，池中每个processor都会处理一组session。而NioDatagramAcceptor没有使用池化的IoProcessor，而是选择实现了IoProcessor，即他本身就是一个IoProcessor，在他自身的executer中只有一个Acceptor勤劳的为他做着接受新数据的

但是为什么呢？

我们知道，UDP是无连接的协议，select()应答器响应有通道的数据到来时，通过receive()方法获取buffer内容。NioDatagramAcceptor只开启了单个DatagramChannel用于数据传输，因为他不需要像NioSocketAcceptor那样维持一个通讯会话session。     对于远程地址相同的请求，NioDatagramAcceptor使用可重用的ExpiringSessionRecycler对象sessionRecycler来复用session。而NioSocketAcceptor会为把每个session分配到指定的IoProcessor池中的一个去处理。

```java
AbstractPollingConnectionlessIoAcceptor.newSessionWithoutLock( SocketAddress remoteAddress, SocketAddress localAddress ) throws Exception  
    {  
        H handle = boundHandles.get( localAddress );  
  
        if ( handle == null )  
        {  
            throw new IllegalArgumentException( "Unknown local address: " + localAddress );  
        }  
  
        IoSession session;  
  
        synchronized ( sessionRecycler )  
        {  
            session = sessionRecycler.recycle( remoteAddress );  
  
            if ( session != null )  
            {  
                return session;  
            }  
  
            // If a new session needs to be created.  
            S newSession = newSession( this, handle, remoteAddress );  
            getSessionRecycler().put( newSession );  
            session = newSession;  
        }  
  
        initSession( session, null, null );  
  
        try  
        {  
            this.getFilterChainBuilder().buildFilterChain( session.getFilterChain() );  
            getListeners().fireSessionCreated( session );  
        }  
        catch ( Throwable t )  
        {  
            ExceptionMonitor.getInstance().exceptionCaught( t );  
        }  
  
        return session;  
    }  
```

究其原因，应该是NioDatagramAcceptor对于会话的维护不需要付出NioSocketAcceptor那么大的代价。NioDatagramAcceptor只用了一个Selector去监听会话请求，而NioSocketAcceptor用了1个监听连接、CPU+1个去监听不同的session，可见一斑。

# 结语

客户端的IoConnector部分就不分析了。相信通过这几节的了解，框架结构都非常清楚了。应该没有别的落下= =不过其中有很多对网络的实现方面的细节还不是很清楚，看来要了解网络传输底层的东西才行。

版权声明：本文为博主原创文章，未经博主允许不得转载。