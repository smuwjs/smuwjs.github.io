---
title: Hexo的使用介绍
date: 2016-09-07 21:33:01
description: 
categories:
- Hexo
  tags:
- Hexo
  toc: true
  author:
  comments:
  original:
  permalink: 
---

　　**自用笔记：**本文属于自用笔记，不做详解，仅供参考。在此记录自己已理解并开始遵循的前端代码规范。What How Why
　　最近，使用Hexo遇到了很多问题，在设立进行整理。

<!-- more -->
### 写文章

```
$ hexo n #写文章 

    其中my new post为文章标题，执行命令后。
    会在项目\source_posts中生成my new post.md文件，用编辑器打开编写即可。

    当然，也可以直接在\source_posts中新建一个md文件，我就是这么做的。

    文章开头语法：

title: name #文章标题
date: 2015-12-25 18:29:00  #写作时间
description: #文章描述
categories: #文章分类
- 建站
tags: #文章标签
- 博客
- 建站
- Hexo
toc: true # 生成目录
author:
comments:
original:
permalink: #指定链接
---

    以上是摘要
<!--more-->

    以下是余下全文
```
### 写多种文章

```
$ hexo new [layout] "postName" #新建文章

    [layout]：其中layout是可选参数，默认值为post。
    有哪些layout呢，请到 scaffolds 目录下查看，这些文件名称就是layout名称。
    当然你可以添加自己的layout，方法就是添加一个文件即可。
    同时你也可以编辑现有的layout，比如post的layout默认是 hexo\scaffolds\post.md

    关于hexo\scaffolds\photo.md配置文件的介绍：

layout: { { layout } } #layout名称
title: { { title } } #文章标题
date: { { date } } #文章生成时间
ategories: #文章分类目录
tags:  #文章标签
-  #
photos:  #
-  #
---

layout: photo
title: 我的阅历
date: 2085-01-16 07:33:44
tags: [hexo]
photos:
- http://bruce.u.qiniudn.com/2013/11/27/reading/photos-0.jpg
- http://bruce.u.qiniudn.com/2013/11/27/reading/photos-1.jpg
```

### 自定义页面

```
    执行new page命令

$ hexo new page "about"

    在 *hexo\source\* 下会生成 *about* 目录，
    里面有个index.md，直接编辑就可以了，
    然后在主题的 *_config.yml* 中将其配置显示出来。 

    上述步骤，也可以手工生成，在 *hexo\source\* 下手工新建
     *about* 和 *index.md* 也是完全等价的。

    因为markdown对table的支持不好，我是在about中直接
    建立index.html，里面书写页面内容，hexo会帮你加上头和尾。

```
### 写页面（404）

```
$ hexo new page "404"

UUhike@UUhike-pc MINGW64 /d/Hexo/Hexo (master)
$ hexo new page "404"
INFO  Created: D:\Hexo\Hexo\source\404\index-1.md
```
```
title: 404 Not Found：该页无法显示
comments: false
permalink: /404
fancybox: false
---
<style type="text/css">
    .article-title {
        font-size: 2.1em;
    }
    strong a {
        color: #747474;
    }
    .share {
        display: none;
    }
    .player {
        margin-left: -10px;
    }
    .sign {
        text-align: right;
        font-style: italic;
    }
    #page-visit {
        display: none;
    }
    .center {
        text-align: center;
        height: 2.5em;
        font-weight: bold;
    }
    .search2 {
        height: 2.2em;
        font-size: 1em;
        width: 50%;
        margin: auto 24%;
        color: #727272;
        opacity: .6;
        border: 2px solid lightgray;
    }
    .search2:hover {
        opacity: 1;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.3)
        };
    .article-entry hr {
        margin: 0;
    }
    .pic {
        text-align: center;
        margin: 0;
    }
    .pic br {
        display: none;
    }
</style>

***

<div class="pic">
<img src="/resources/Mihawk-Wind.gif" title="Mihawk-Wind">
</div>

<p class="center">很抱歉，您所访问的地址并不存在: </p>

<p class="center"><a href="/">回主页</a> · <a href="/archives">所有文章</a> · <a href="/about">留言板</a></p>

<p class="center">可在边栏搜索框中对本站进行检索，以获取相关信息。</p>

<div style="text-align: center">
以下是博主喜欢的一些歌曲，可以听听，稍作休息~
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=320 height=330 src="http://music.163.com/outchain/player?type=0&id=112513213&auto=0&height=430"></iframe>
</div>
```

### 发布博客内容
实现发布，前提是配置好，部署到Github前需要配置_config.yml文件。

```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: github
  repository: git@github.com:zhchnchn/zhchnchn.github.io.git
  branch: master

```
更新的最新版本，可能会有Bug，自行百度，好像要修改type：git。
```
$ hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）Hexo 会监视文件变动并自动更新，您无须重启服务器。

$ hexo g #生成
$ hexo generate #生成静态页面至public目录（最终上传这个文件到GitHub）

$ hexo d == hexo deploy#部署
$ hexo d #部署 # 可与hexo g合并为 hexo d -g

$ hexo deploy -g
$ hexo server -g # 生成默认文件群再执行,开启本地静态html服务器
```

### 主题的更改

```
1. 将Git Shell 切到/D/Hexo目录下，然后执行下面的命令，将pacman下载到 themes/pacman 目录下。

$ git clone https://github.com/A-limon/pacman.git themes/pacman
2. 修改你的博客根目录/D/Hexo下的config.yml配置文件中的theme属性，将其设置为pacman。

3. 更新pacman主题

$ cd themes/pacman
$ git pull

NOTE：先备份_config.yml 文件后再升级

主题：
    yilia
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
    modernist
$ git clone https://github.com/heroicyang/hexo-theme-modernist.git themes/modernist
    jacman
$ git clone https://github.com/cnfeat/cnfeat.git themes/jacman
```

### 文章图片路径
Hexo如何方式图片，图片应该放置到哪里，不会应为上传而覆盖掉。然后把文章里的index.md删除，将文件存放在resource文件夹中间。

```
    发布页面
$ hexo new page "name" # 新建一个页面，页面名称name

    UUhike@UUhike-pc MINGW64 /d/Hexo/Hexo (master)
$ hexo new page "resoures"
    INFO  Created: D:\Hexo\Hexo\source\resoures\index.md
```

### Hexo文件备份

```
    git-backup.

    Install

    if your hexo version is 2.x.x, you should install as follow:

$ npm install hexo-git-backup@0.0.91 --save
    if version is 3.x.x, you should install as follow:

$ npm install hexo-git-backup --save
    Update

    if you install with --save, you must remove firstly when you update it.

$ npm remove hexo-git-backup
$ npm install hexo-git-backup --save
    Configure

    You should configure this plugin in _config.yml.

backup:
    type: git
    repository:
       github: git@github.com:xxx/xxx.git,branchName
       gitcafe: git@github.com:xxx/xxx.git,branchName
Using

hexo backup 
or

hexo b
Options

if you want to back up with your theme,just add theme: your theme name,your theme name in _config.yml.

backup:
    type: git
    theme: coney,landscape,xxx
    repository:
       github: git@github.com:xxx/xxx.git,branchName
       gitcafe: git@github.com:xxx/xxx.git,branchName
Attention: if you do as above, the dir themes/coney/.gitwill be removed

if you want DIY commit message, just add 'message: update xxx'.

backup:
    type: git
    message: update xxx
    repository:
       github: git@github.com:xxx/xxx.git,branchName
       gitcafe: git@github.com:xxx/xxx.git,branchName
Now you can backup all the blog!

Problems

You may get some troubles by your computer' permission。

Error: EISDIR, open

it is caused by permission. just do 'sudo hexo b'

sudo hexo b
```

# 参考资料：
[使用GitHub搭建Hexo博客](http://luuman.github.io/2015/12/25/GitHub+Hexo/)
[Hexo插件安装](http://luuman.github.io/2015/12/27/Hexo-plug/)
[hexo你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)
[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://www.jianshu.com/p/05289a4bc8b2#)