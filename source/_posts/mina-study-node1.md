---
title: mina学习笔记一：mina上路
date: 2016-09-08 20:12:13
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
[原文：http://blog.csdn.net/yoara/article/details/37117477](http://blog.csdn.net/yoara/article/details/37117477)

很久以前用mina做过项目，当时的感觉就是很轻松简单，几行代码就能写好复杂的网络连接雏形，不用考虑请求阻塞，不用考虑客户端的负荷压力。做了好些年业务代码，有时候回头看看自己的技术底蕴真的有些欠缺。最近重拾起NIO和并发编程的内容，java api了解了大概，通过对mina的学习，也算是巩固和升华吧。

起篇的意义在于对mina的背景了解和mina框架的鸟瞰，NIO是什么？为什么还需要在NIO的基础上重新开发出一套mina框架？带着问题我们进入主题。

# **NIO VS IO**

NIO即所谓的new IO(也称为Non-Blocking IO)，是Java在1.4版本引入的用以解决java.io在系统伸缩性上瓶颈的新的架构设计。java.io类主要分为字符流（Reader/Writer）和字节流(Input/OutputStream)，无一例外的是阻塞式的。阻塞式代码带来的问题就是系统的性能瓶颈就受制于串行的挂起等待，线程因为磁盘IO操作或套接字的连接传输等而被挂起，在这其间不仅CPU资源被闲置浪费，也不能通过横向的增加硬件设备以得到很好的缓解负荷提升性能的目的。

于是NIO引入了一套非阻塞的机制。 java.nio框架主要包含以下几个构成部分：

*   Buffer：缓冲区，数据的载体。
*   Channel：数据通道，他要么从Buffer中获取数据并向flie或socket写，要么从file或socket中获取数据往Buffer中写。
*   Selector：提供多路复用的非阻塞IO选择器。
*   Charset：用于字节到Unicode字符集的转码工具。
*   Regexp：正则表达式工具类

NIO提供多路复用的机制，他把读写IO的操作委托给操作系统（select()/poll()）。当操作系统从数据源读入数据，并将它存放到nio指定的Buffer缓冲区后，就会提醒Selector选择器，某一Channel通道的数据已经准备就绪。这时Selector告诉用户线程数据已到位可以来做对应的业务处理了。如图1.1

![图1.1 nio总体结构](http://opesdt6ii.bkt.clouddn.com/17-5-5/46097041-file_1493950446253_9186.png?imageView/2/w/300)
    相较于IO，NIO的优势在于，用户线程不需要在串行的等待数据的IO操作，操作系统代替了用户去准备，这时用户线程可以从当前阻塞中脱离并去做其他的事情。同时在服务器端也不必为连接客户端而持有大量的处理线程，只需单线程的selector负责监听即可实现高伸缩性的网络应用。（事实上NIO主要分为文件(File)IO和套接字(Socket)IO，File 类操作依旧是阻塞式的。FileChannel没有实现SelectedChannel，而选择器只接受实现自此接口的通道注册。本质的原因是基于文件的IO操作系统已经有了足够的优化和预读取机制，并且更重要的一点是可以实现异步IO。而多路复用其实是一种同步的IO，具体可参详LInux的5种IO方式。）

# 为什么需要mina

相较于业务功能的开发，网络通讯更偏向于底层。可能大多数程序员在学校里学完以后，在以后的工作中就很难用到。毕竟，Java的好处就是取之不尽的框架，不同的人把焦点集中在不同层面，既避免了重复造轮子也提高了生产效率。

对于阻塞式的IO，线程需要等待；而对于非阻塞式的IO，仍然需要单线程去轮询selector的状态。mina将封装了这些网络层的处理，对用户是透明的。它简化了TCP、UDP、串行通信程序等网络连接的使用；简化了服务端和客户端的使用。在高扩展性、性能和内存使用（据说netty做的更好，不过这两个框架基本思想差不多）方面给用户提供有力的支持。

mina是简单但功能齐全的网络应用程序框架，它的主要特性包括：

*   为各种网络通讯提供统一的API。
    *   基于Java NIO 的TCP/IP UDP/IP。
    *   基于RXTX的串行通信程序。
    *   基于VM内部的管道通讯。
    *   支持自定义实现。
*   可扩展的过滤器链，类似Serlvet的过滤器模型。
*   底层和抽象层的数据API。
    *   底层：可基于ByteBuffer使用。
    *   抽象层：用户自定义的消息对象和解编码方式。
*   高度可定制的线程模型。
    *   单线程
    *   单线程池
    *   多个线程池
*   基于Java 5 SSLEngine进行了封装，支持开箱即用的SSL、TLS、StartTLS等。（？）
*   过载保护和流量控制。
*   可使用mock对象进行方便的单元测试。
*   集成JMX的管理架构
*   支持流IO（Stream-Base I/O）的操作，通过实现StreamIoHandle。
*   可被常用的容器框架集成管理，如Spring。
*   能从Netty平滑的迁移过度到Mina。

# 结语

  总体介绍就是这样了，下一节从官方的例子开始，一步一步的剖析mina吧。

版权声明：本文为博主原创文章，未经博主允许不得转载。

