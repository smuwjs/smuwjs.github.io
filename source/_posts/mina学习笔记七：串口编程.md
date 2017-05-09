---
title: mina学习笔记七：串口编程
date: 2016-09-08 20:12:19
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
[原文：http://blog.csdn.net/yoara/article/details/37726817](http://blog.csdn.net/yoara/article/details/37726817)

# 1.基于java.comm的串口编程

以前做过一个针对串口扫描枪解析的项目，当时是用的java.comm包。回忆一下当时是怎么做的。

第一步：下载comm包，它包含有三个文件win32com.dll、comm.jar、javax.comm.properties

第二步：分别将他们拷到对应的文件夹

```java
xcopy "system\win32com.dll" "%JAVA_HOME%\jre6\bin\"  /E  
xcopy "system\comm.jar" "%JAVA_HOME%\jre6\lib\"  /E  
xcopy "system\javax.comm.properties" "%JAVA_HOME%\jre6\lib\"  /E  
```

第三步：编码，代码大致如下

```java
private CommPortIdentifier portId;  
private SerialPort serialPort;  
private InputStream is;  
private ReaderThread t;  
private static final String PortName = "COM1";  //串口号  
private static final int DATABITS = 8;      //数据位  
private static final int STOPBITS = 1;      //停止位  
private static final int RATE = 9600;           //波特率  
try{   
    this.portId = CommPortIdentifier.getPortIdentifier(getPortName());  
    this.serialPort = ((SerialPort)this.portId.open("Bar_Scan", 20000));  
    this.is = this.serialPort.getInputStream();  
    this.serialPort.addEventListener(this.t);  
    this.serialPort.notifyOnDataAvailable(true);  
    this.serialPort.setSerialPortParams(getRATE(), getDATABITS(),   
      getSTOPBITS(), 0);  
  
    this.t.init(this.serialPort, this.is);  
}catch(){}  
```

```java
public class ReaderThread extends Thread  
  implements SerialPortEventListener{  
  private SerialPort serialPort;  
  private InputStream is;  
  public void init(SerialPort serialPort, InputStream is) {  
     this.serialPort = serialPort;  
     this.is = is;   
  }  
  public void serialEvent(SerialPortEvent event) {  
    switch (event.getEventType()) {  
    case SerialPortEvent.DATA_AVAILABLE:  
      while (true) try {  
        ...;  
        int b = this.is.read();  
        ...  
        }  
        catch (IOException e){  
            log4J.error(e);  
        }  
    }  
  }  
}  
```



# 2.为什么用mina封装串口通讯

可以看出，用sun自带的comm方式针对串口编程也很简单，同时comm包自带了非常丰富的参考例子。但是用mina封装的好处是能复用mina的连接模型，包括filter、listener、handler等等，把对底层的通讯和业务解耦，是系统结构清晰，权责分明。     不过估计mina也觉得用串口编程或通过mina框架串口编程的人士不会很多，所以在官方的标准发布包中并未包含serial部分，需要重新下载。     同时该serial包依赖于rxtx的串口并口包。由于sun的comm包很久都没有维护了，RXTX事实上是对comm再支持，他是开源的，基本思想和开发的模式跟comm几乎一样。编程时最明显的不同是要包含的包名由javax.comm.*改成了gnu.io.*。

## 2.1 RXTX的配置方式

RXTX官网的下载链接失效了，可以使用[这里的](http://mfizz.com/oss/rxtx-for-java)。和comm一样，RXTX在使用前需要进行配置（Window下，linux请看包内install）

```java
Copy RXTXcomm.jar ---> <JAVA_HOME>\jre\lib\ext  
Copy rxtxSerial.dll ---> <JAVA_HOME>\jre\bin  
Copy rxtxParallel.dll ---> <JAVA_HOME>\jre\bin  
```

## 2.2 mina中的集成使用方式

串行通信只提供了一个IoConnector接口，因为串行通信是基于点对点的。要连接到一个串行通信端口上，需要创建一个SerialConnector。

```java
//创建串口连接  
SerialConnector connector = new SerialConnector();  
//绑定处理handler  
connector.setHandler(new MyHandler());  
//设置过滤器  
connector.getFilterChain().addLast("logger",new LoggingFilter());  
```

现在创建一个地址连接到一个串行端口：

```java
//配置串口连接  
SerialAddress address = new SerialAddress  
    ("COM1", 9600, DataBits.DATABITS_8,StopBits.BITS_1 , Parity.NONE, FlowControl.NONE);  
```

第一个参数是串行端口标识符，对于Windows系统来说，串行端口被称作“COM1”、“COM2”等，对于Linux和Unix系统来说，则是"/dev/ttyS0"、"/dev/ttyS1"和"/dev/ttyUsb0"等。

剩余的参数取决于所使用的设备，在SerialAddress类中定义了相关的枚举量可参考：

*   **波特率：**这是一个衡量符号传输速率的参数。指的是信号被调制以后在单位时间内的变化，即单位时间内载波参数变化的次数，如每秒钟传送240个字符，而每个字符格式包含10位（1个起始位，1个停止位，8个数据位），这时的波特率为240Bd，比特率为10位*240个/秒=2400bps。一般调制速率大于波特率，比如曼彻斯特编码）。通常电话线的波特率为14400，28800和36600。波特率可以远远大于这些值，但是波特率和距离成反比。高波特率常常用于放置的很近的仪器间的通信，典型的例子就是GPIB设备的通信。

*   **数据位：**这是衡量通信中实际数据位的参数。当计算机发送一个信息包，实际的数据不会是8位的，标准的值是6、7和8位。如何设置取决于你想传送的信息。比如，标准的ASCII码是0～127（7位）。扩展的ASCII码是0～255（8位）。如果数据使用简单的文本（标准 ASCII码），那么每个数据包使用7位数据。每个包是指一个字节，包括开始/停止位，数据位和奇偶校验位。由于实际数据位取决于通信协议的选取，术语“包”指任何通信的情况。

*   **停止位：**用于表示单个包的最后一位。典型的值为1，1.5和2位。由于数据是在传输线上定时的，并且每一个设备有其自己的时钟，很可能在通信中两台设备间出现了小小的不同步。因此停止位不仅仅是表示传输的结束，并且提供计算机校正时钟同步的机会。适用于停止位的位数越多，不同时钟同步的容忍程度越大，但是数据传输率同时也越慢。

*   **奇偶校验位：**在串口通信中一种简单的检错方式。有四种检错方式：偶、奇、高和低。当然没有校验位也是可以的。对于偶和奇校验的情况，串口会设置校验位（数据位后面的一位），用一个值确保传输的数据有偶个或者奇个逻辑高位。例如，如果数据是011，那么对于偶校验，校验位为0，保证逻辑高的位数是偶数个。如果是奇校验，校验位为1，这样就有3个逻辑高位。高位和低位不真正的检查数据，简单置位逻辑高或者逻辑低校验。这样使得接收设备能够知道一个位的状态，有机会判断是否有噪声干扰了通信或者是否传输和接收数据是否不同步。

最后建立链接

```java
ConnectFuture future = connector.connect(address);  
future.await();  
IoSession session = future.getSession();  
session.write("good mina");  
```

## 2.3 测试效果

手头上没有串口工具，所以用VSPD虚拟了串口出来。测试可用正常。  

![](http://opesdt6ii.bkt.clouddn.com/17-5-5/32390574-file_1493967899939_b28b.jpg)

# 3.mina serial处理方式分析

连接代码被调用时SerialConnector.connect0方法被执行 

```java
SerialPort serialPort = initializePort("Apache MINA", portId, portAddress);  
  
ConnectFuture future = new DefaultConnectFuture();  
SerialSessionImpl session = new SerialSessionImpl(this, getListeners(), portAddress, serialPort);  
initSession(session, future, sessionInitializer);  
session.start();  
return future;  
```

串口的session，不仅继承了AbstractIoSession ，同时他还实现了SerialSession, SerialPortEventListener。

```java
class SerialSessionImpl extends AbstractIoSession implements SerialSession, SerialPortEventListener  
```

我们注意他的start方法

```java
void start() throws IOException, TooManyListenersException {  
    inputStream = port.getInputStream();  
    outputStream = port.getOutputStream();  
    ReadWorker w = new ReadWorker();  
    w.start();  
    port.addEventListener(this);  
    ((SerialConnector) getService()).getIdleStatusChecker0().addSession(this);  
    try {  
        getService().getFilterChainBuilder().buildFilterChain(getFilterChain());  
        serviceListeners.fireSessionCreated(this);  
    } catch (Throwable e) {  
        getFilterChain().fireExceptionCaught(e);  
        processor.remove(this);  
    }  
}  
```

其中有个ReadWorker线程，他首先等待Object readReadyMonitor的资源，如果readReadyMonitor被notify，那线程就开始读数据

```java
try {  
    readReadyMonitor.wait();  
} catch (InterruptedException e) {  
    log.error("InterruptedException", e);  
}  
if (isClosing() || !isConnected()) {  
    break;  
}  
int dataSize;  
try {  
    dataSize = inputStream.available();  
    byte[] data = new byte[dataSize];  
    int readBytes = inputStream.read(data);  
  
    if (readBytes > 0) {  
        IoBuffer buf = IoBuffer.wrap(data, 0, readBytes);  
        buf.put(data, 0, readBytes);  
        buf.flip();  
        getFilterChain().fireMessageReceived(buf);  
    }  
} catch (IOException e) {  
    getFilterChain().fireExceptionCaught(e);  
}  
```

而readReadyMonitor被notify的事件就发生在串口有数据读入，因SerialSessionImpl实现了SerialPortEventListener，所以他就可以监听事件

```java
public void serialEvent(SerialPortEvent evt) {  
    if (evt.getEventType() == SerialPortEvent.DATA_AVAILABLE) {  
        synchronized (readReadyMonitor) {  
            readReadyMonitor.notifyAll();  
        }  
    }  
}  
```

# 结语

补充了一下mina对串口的支持，框架确实是个剥离业务与底层的好东西。

版权声明：本文为博主原创文章，未经博主允许不得转载。
