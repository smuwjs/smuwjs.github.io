---
tags: 'Git'
categories: 'Git'
date: 2016-09-11 11:12:58
---

###### Update
* <font color=red>update: Windows用户在初始化Souretree工具时，需要用到Atlassian ID，新建用户时需要加载google 验证码，这一步需要VPN的支持，请注意。</font>
* 找到一个版本的SourceTree工具可以不需要以上验证也能使用，下载链接：[SourceTreeSetup_1.6.14.exe](https://pan.baidu.com/s/1bJOzOq)

<br>


##### 补充1 ：Git学习网站
1. [猴子都能学会的git教程](http://backlogtool.com/git-guide/cn/)
2. [常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
3. [廖雪峰的git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
4. [Why Git is Better than X](http://zoomq.qiniudn.com/ZQScrapBook/ZqFLOSS/data/20081210180347/)

##### 补充2 ：利用Git协同开发
1. [团队中的Git实践](https://ourai.ws/posts/working-with-git-in-team/)
2. [Git 使用规范流程](http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)
3. [Git分支管理模型](http://nvie.com/files/Git-branching-model.pdf)
4. [图解Git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html)

<hr>

## 1. 关于 SourceTree
#### SourceTree 是一款免费且同时支持Windows 和Mac 的git项目管理软件，本文旨在给大家介绍这款应用的基础使用，并用它来管理你的项目。
>官网： https://www.sourcetreeapp.com/

## 2. git帐号建立
### 1. 新员工入职之后，你的公司邮箱内会收到一封来自Gitlab的邮件，如下图：
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree02.jpg)

### 2. 点击邮件中的 “Click here to set your password”，设置gitlab登陆密码。
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsouretree03.jpg)

### 3. 登陆gitlab帐号，将会出现这个界面：
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree05.jpg)

### 4. 设定个人信息：
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsouretree06.jpg)

### 5. sshKey
#### 5.1 在Linux和Mac上生成sshkey：

```
➜  ~ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/Alex/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/Alex/.ssh/id_rsa.
Your public key has been saved in /Users/Alex/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:98jE3jhBhFT5nQlZRj34a3WOk1t6XF+Dbf/hliXl4WQ Alex@Alex-Mac
The key's randomart image is:
+---[RSA 2048]----+
|       ..oo. +=. |
|        ... oo ..|
|          .. o.o.|
|         o  . +E+|
|        S =   **=|
|         = * .=BB|
|          * o oOO|
|           .  +o*|
|              .oo|
+----[SHA256]-----+
➜  ~ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCvuQAq65b+nLZPqqc3b3Mj9e7Pt8oWKasJFa2QH1VIEkDvxKLFGcHsT7Ur4zXwEi9YiW2tVRrBSjcMALxuBjVm2IxYV6Lk8SLuGadyYy5telWGJmHsQ3VIPRuKwpzTkLN643kjqc6JFSlnZG/XoP9SPtCOsp2ql4u0s7Auc2bZay4RaTDXbcpJVU9OA0xM8Zy4oTTNYdZ4tvGittVmn+wLrhN255J7clORF5126dmDYxV3E8ZboaDdQpdLGIWmDNcBJQvl0CLwpKUCi7EUDqDVtm4bNgwIX9fEIkTxGdaWjBW1iXBk8TGXWkgB+Qp8B1IwaJ4GHUwUhQrefWvw9XeJ Alex@Alex-Mac
➜  ~
```

#### 5.2 在Windows上生成sshkey：
#### 因为windows没有自带openssl模块，所以在Windows环境中使用第三方工具puttygen.exe生成sshkey [下载地址](http://the.earth.li/~sgtatham/putty/0.67/x86/puttygen.exe)

#### 步骤如下：

![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree13.jpg)
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree14.jpg)
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree15.jpg)

#### 当sourcetree首次启动时，会弹出加载sshkey的提示，按提示操作，找到之前保存的private.ppk文件
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree12.jpg)

#### 5.3 上传sshkey：
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree07.jpg)

### 6. 回到 dashboard ，点击项目名称进入详情：
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree08.jpg)

### 7. 使用souretree将项目从git服务器clone到本地

#### 7.1 安装souretree 软件 ［略］

#### 7.2 clone项目到本地
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree09.jpg)
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree10.jpg)

### 8. 进入项目工作台：
![image](http://samzong.oss-cn-shenzhen.aliyuncs.com/2016%2F04%2Fsourcetree11.jpg)


#### 9. 关于sourcetree工具的使用，下面是一些git操作的释疑。
* 检出仓库: 将在本地创建一个git仓库的克隆版本
* 工作流: 本地仓库由 git 维护的三棵“树”组成。第一个是 工作目录，它持有实际文件；第二个是 缓存区（Index），它像个缓存区域，临时保存改动；最后是 HEAD，指向最近一次提交后的结果。
* 提交：可以计划改动（把它们添加到缓存区),将改动提交到了 HEAD，但是还没到提交到远端仓库。
* 拉取：从远端仓库拉取最新版本状态，特别是在其他人员有所改动之后。
* 推送：改动现在已经在本地仓库的 HEAD 中了。这时可以使用它将这些改动提交到远端仓库。
* 分支：分支是用来将特性开发分离出来的。在创建仓库的时候，master 是“默认的”。创建分支将可以从主线开发上分离开来，然后在不影响主线的同时继续工作，完成后再将它们合并到主分支上。
* 合并： 将分支功能并入主分支。
