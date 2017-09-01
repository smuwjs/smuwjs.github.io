#  Eclipse关联JDK源码

>阅读JDK源码是Java程序员的基本功，也是必经之路。在使用Eclipse编写代码时，常常需要查看JDK的源码实现。第三方的依赖包源码可以用Maven辅助下载，这里主要记录下如何关联JDK源码。

## 源码

其实在安装JDK时，已经有一份源码，在目录 `%JAVA_HOME%/jdk1.8.0/`，可以看到一个src.zip的压缩包，这就是JDK的源码。

## 操作

> 有两种关联的方法可供参考。

### 方法一

> 按照下面的顺序操作，即可正常关联JDK源码。

1.打开eclipse选择Window->Preference。

![](https://imgsa.baidu.com/exp/w=480/sign=b84400bd02082838680ddd1c8898a964/730e0cf3d7ca7bcb957d6c8dbb096b63f624a80a.jpg)

2.选择Java->Installed JREs 或者直接搜索Installed JREs

![选择Java->Installed JRES](https://imgsa.baidu.com/exp/w=480/sign=615d96b1d854564ee565e53183df9cde/c9fcc3cec3fdfc03ba4c491ad13f8794a4c2265f.jpg)

3.此时"Installed JRES"右边是列表窗格，列出了系统中的 JRE 环境，选择你的JRE，然后点边上的 "Edit..."。

![](https://imgsa.baidu.com/exp/w=480/sign=7e902feccf8065387beaa51ba7dca115/9c16fdfaaf51f3de590c13c691eef01f3b2979d2.jpg)

4.会出现一个窗口(Edit JRE)，选中rt.jar文件的这一项。 

![](https://imgsa.baidu.com/exp/w=480/sign=b4dd6e899816fdfad86cc7e6848e8cea/960a304e251f95cac2fa0714cc177f3e67095200.jpg)

5.展开后，可以看到“Source Attachment:(none)”，点这一项，点右边的按钮“Source Attachment...”, 选择你的JDK目录下的 “src.zip”文件。

![](https://imgsa.baidu.com/exp/w=480/sign=1d85011982d6277fe912333018391f63/08f790529822720e967777587ecb0a46f31fabd3.jpg)

6.在对话框中，点击External File，选择你所安装的jdk目录下的src.zip文件，一路点OK即可。

![](https://imgsa.baidu.com/exp/w=480/sign=8992f37c386d55fbc5c6772e5d234f40/b7003af33a87e9509a2f6cce15385343fbf2b45c.jpg)

> **注：**
> dt.jar是关于运行环境的类库,主要是swing的包 。
> dt.jar是关于运行环境的类库,主要是swing的包 。
> tools.jar是关于一些工具的类库 。
> dt.jar是关于运行环境的类库,主要是swing的包 。
> tools.jar是关于一些工具的类库 。
> rt.jar包含了jdk的基础类库，也就是你在java doc里面看到的所有的类的class文件。

### 方法二

> 如果不想去设置，也有懒方法。

打开一个`.java`文件中，点击Java原生对象，如Object，就会弹出Class File Editor ，点击Attach Source按钮，选择External location --> External File，输入src.zip压缩包所在路径，确认即可关联。操作示意图如下。

1.点击Object对象。

   ![ctrl + 鼠标左键，点击Object对象](http://upload-images.jianshu.io/upload_images/51561-acf984a9692a56b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2.弹出Class File Editor关联窗口。

   ![Class File Editor关联窗口，点击Attach Source按钮](http://upload-images.jianshu.io/upload_images/51561-07c77fa3ecf216dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   ​

3.输入路径，关联src.zip压缩包。

   ![输入路径，关联src.zip压缩包](http://upload-images.jianshu.io/upload_images/51561-a4f810b653d86312.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


4.如果出现如下源码，即关联成功。


![效果展示](http://upload-images.jianshu.io/upload_images/51561-e4a691ff5c42fb9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
