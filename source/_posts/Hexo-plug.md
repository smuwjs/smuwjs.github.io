title: Hexo插件安装
date: 2016-09-07 22:33:55
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
## 同步instagram图片

>新建页面

```
执行命令：
hexo new page "instagram"
```
>修改文件

```
在目录yourBlog\source\instagram下，修改index.md内容为：

---
layout: post
slug: "instagram"
title: "相册"
noDate: "true"
---
<div class="instagram" data-client-id="a0d4bd    ab154b4c689aff70602cb34a2c" data-user-id="438522285">
    <a href="http://instagram.com/litten225" target="_blank" class="open-ins">图片来自instagram，正在加载中…</a>
</div>
<script src="/js/jquery.lazyload.js"></script>
<script src="/js/instagram.js"></script>
```
>获取ID

```
data-client-id：
[注册新客户端](http://instagram.com/developer/clients/manage/)
获取CLIENT ID：a0d4bdab154b4c689aff70602cb34a2c
用client_id去换取token：
在浏览器中请求：

    https://instagram.com/oauth/authorize/?client_id={CLIENT_ID}&redirect_uri={REDIRECT_URI}&response_type=token
    https://instagram.com/oauth/authorize/?client_id=a0d4bdab154b4c689aff70602cb34a2c&redirect_uri=http://luuman.github.io/&response_type=token
    花括号里面的值，对应上一步最终得到的client_id和自己设定的redirect_uri。
    请求到的是一个授权页面，授权完毕后，则重定向到你的redirect_uri。
    注意看授权成功后的url，hash部分会附带给你的token。至此，token成功获取，将至放在data-client-id。

data-user-id：

需要到[lookup-user-id](http://jelled.com/instagram/lookup-user-id#)填写用户名并查找。
```
## 分享按钮
>添加代码

```
以下代码是百度分享的页面24页面分享的代码，可以自行选择以下标签。

配置

vim themes/light/layout/_partial/article.ejs
删除
<%-partial('post/share')%>
替换为刚刚获取的分享代码

<div class="bdsharebuttonbox">
    <a href="#" class="fx fa-weibo bds_tsina" data-cmd="tsina" title="分享到新浪微博"></a>
    <a href="#" class="fx fa-weixin bds_weixin" data-cmd="weixin" title="分享到微信"></a>
    <a href="#" class="fx fa-qq bds_sqq" data-cmd="sqq" title="分享到QQ好友"></a>
    <a href="#" class="fx fa-facebook-official bds_fbook" data-cmd="fbook" title="分享到Facebook"></a>
    <a href="#" class="fx fa-twitter bds_twi" data-cmd="twi" title="分享到Twitter"></a>
    <a href="#" class="fx fa-linkedin bds_linkedin" data-cmd="linkedin" title="分享到linkedin"></a>
    <a href="#" class="fx fa-files-o bds_copy" data-cmd="copy" title="分享到复制网址"></a>

    <a href="#" class="fx fa-plus-square bds_more" data-cmd="more"></a>
    <a href="#" class="fx fa-tencent-weibo bds_tqq" data-cmd="tqq" title="分享到腾讯微博"></a>
    <a href="#" class="fx fa-renren bds_renren" data-cmd="renren" title="分享到人人网"></a>
    <a href="#" class="fx fa-paw bds_tieba" data-cmd="tieba" title="分享到百度贴吧"></a>
    <a href="#" class="fx fa-envelope-o bds_mail" data-cmd="mail" title="分享到邮件分享"></a></div>
    <a href="#" class="fx fa-print bds_print" data-cmd="print" title="分享到打印"></a>
    <a href="#" class="fx fa-share-alt bds_mshare" data-cmd="mshare" title="分享到一键分享"></a>
    <a href="#" class="fx bds_douban" data-cmd="douban" title="分享到豆瓣网"></a>
    <a href="#" class="fx fa- bds_qzone" data-cmd="qzone" title="分享到QQ空间"></a>
    <a href="#" class="fx fa- bds_evernotecn" data-cmd="evernotecn" title="分享到印象笔记"></a>
</div>
<script>window._bd_share_config={"common":{"bdSnsKey":{},"bdText":"","bdMini":"1","bdMiniList":false,"bdPic":"","bdStyle":"2","bdSize":"24"},"share":{}};with(document)0[(getElementsByTagName('head')[0]||body).appendChild(createElement('script')).src='http://bdimg.share.baidu.com/static/api/js/share.js?v=89860593.js?cdnversion='+~(-new Date()/36e5)];</script>
```
>修改样式

```
\themes\spfk\source\css\_partial\article.styl

/* bd share button box */
.bdshare-button-style2-24{
  width: 330px;
  margin: auto;
  .fx {
      color: #999;
      padding: 0;
      margin: 6px;
      height: 54px;
      display: inline-block;
      font: normal normal normal 36px/1 FontAwesome;
      background: none;
      text-rendering: auto;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
  }
  .fx:hover{
    color: #FFF;
    opacity: 1;
  }
}

themes\spfk\source\css\_partial\mobile.styl
/* bd share button box */
.bdshare-button-style2-24{
  width: 289px;
  .fx {
      font: normal normal normal 30px/1 FontAwesome;
  }
}
```

## 百度统计

>添加代码

```
去百度统计获取统计代码

vim themes/light/layout/_partial/baidu_analytics.ejs

<% if (theme.baidu_analytics){ %>
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "//hm.baidu.com/hm.js?819b1c6493df653afb8c784db6";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();
</script>

<% } %>

vim hexo/themes/light/_config.yml

baidu_analytics: true

vim hexo/themes/light/layout/_partial/head.ejs
在/head前添加

<%- partial('baidu_analytics') %>
</head>
```

## 不蒜子
>添加代码

```
themes/你的主题/layout/_partial/footer.ejs

<footer id="footer">
    <div class="outer">
        <div id="footer-info">
            <div class="footer-left">
                &copy; <%= date(new Date(), 'YYYY') %> <%= config.author || config.title %>
            </div>
            <div class="footer-right">
                <a href="http://hexo.io/" target="_blank">Hexo</a>  Theme <a href="https://github.com/luuman/hexo-theme-spfk" target="_blank">spfk</a> by luuman
            </div>
        </div>
        <div class="visit">
            <span id="busuanzi_container_site_pv" style='display:none'>
                <span id="site-visit" >本站到访数: 
                    <span id="busuanzi_value_site_uv"></span>
                </span>
            </span>
            <span id="busuanzi_container_page_pv" style='display:none'>
                <span id="page-visit">, 本页阅读量: 
                    <span id="busuanzi_value_page_pv"></span>
                </span>
            </span>
        </div>
    </div><script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js">
</script>
</footer>
```

>修改样式

移动端出现上移themes\spfk\source\css\_partial\mobile.styl
```
#footer {
    bottom: 10px;
    .footer-left{
        float: initial;
        margin-bottom: 10px;
    }
    .footer-right{
        float: initial;
        margin-bottom: 10px;
    }
}
#container .visit {
    margin-top: 1em;
}
```


## 版权声明

>添加代码

```
\layout\_partial\post\nav.ejs

<% if (post.original != false && !is_page()){ %>
    <div class="copyright">
        <p><span>本文标题:</span><a href="<%- url_for(post.path) %>"><%= post.title %></a></p>
        <p><span>文章作者:</span><a href="/" title="请访问 <%=theme.author%> 博客"><%=theme.author%></a></p>
        <!-- <p><span>发布时间:</span><%= post.date.format("YYYY年MM月DD日 - HH时mm分") %></p> -->
        <!-- <p><span>最后更新:</span><%= post.updated.format("YYYY年MM月DD日 - HH时mm分") %></p> -->
        <p>
            <span>原始链接:</span><a class="post-url" href="<%- url_for(post.path) %>" title="<%= post.title %>"><%= post.permalink %></a>
            <span class="copy-path" data-clipboard-text="原文: <%= post.permalink %>　　作者: <%=theme.author%>" title="点击复制文章链接"><i class="fa fa-clipboard"></i></span>
            <script src="/js/clipboard.min.js"></script>
            <script> var clipboard = new Clipboard('.copy-path'); </script>
        </p>
        <p>
            <span>许可协议:</span><i class="fa fa-creative-commons"></i> <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/" title="中国大陆 (CC BY-NC-SA 3.0 CN)">"署名-非商用-相同方式共享 3.0"</a> 转载请保留原文链接及作者。
        </p>
    </div>
<% } %>

<% if (post.prev || post.next){ %>
<nav id="article-nav">
  <% if (post.prev){ %>
    <a href="<%- url_for(post.prev.path) %>" id="article-nav-newer" class="article-nav-link-wrap">
      <strong class="article-nav-caption"><</strong>
      <div class="article-nav-title">
        <% if (post.prev.title){ %>
          <%= post.prev.title %>
        <% } else { %>
          (no title)
        <% } %>
      </div>
    </a>
  <% } %>
  <% if (post.next){ %>
    <a href="<%- url_for(post.next.path) %>" id="article-nav-older" class="article-nav-link-wrap">
      <div class="article-nav-title"><%= post.next.title %></div>
      <strong class="article-nav-caption">></strong>
    </a>
  <% } %>
</nav>
<% } %>
```

>修改样式
```
\themes\spfk\source\css\_partial\article.styl

/* copyright */
.copyright {
    width: 85%;
    max-width: 45em;
    margin: 0 auto;
    padding: .5em 1.8em;
    border: 1px solid lightgray;
    font-size: .93em;
    line-height: 1.6em;
    word-break: break-word;
    background: rgba(255, 255, 255, .4);
    span {
        margin-right: 1em;
        color: #B5B5B5;
        font-weight: bold;
    }
    a {
        color: gray;
        &:hover {
            font-weight: bold;
            color: #a3d2a3;
            text-decoration: underline;
        }
    }
    &:hover p .copy-path::after {
        content: "复制";
    }
    .post-url:hover {
        font-weight: normal;
    }
    .copy-path {
        margin-left: 1em;
        &:hover {
            color: gray;
            cursor: pointer;
        }
    }
}
```

## 社交账号图标修改

>添加代码

```
\themes\spfk\layout\_partial\left-col.ejs

<nav class="header-nav">
    <div class="social">
        <% for (var i in theme.subnav){ %>
            <a class="fl <%= i %>" target="_blank" href="<%- url_for(theme.subnav[i]) %>" title="<%= i %>"><%= i %></a>
        <%}%>
    </div>
</nav>
```

>修改样式

## 站内搜索框

>添加代码
用ajax+json搜索


[完美解决Hexo静态博客搜索问题](http://www.netcan666.com/2015/11/20/%E5%AE%8C%E7%BE%8E%E8%A7%A3%E5%86%B3Hexo%E9%9D%99%E6%80%81%E5%8D%9A%E5%AE%A2%E6%90%9C%E7%B4%A2%E9%97%AE%E9%A2%98/)

## 站点Swiftype搜索框

>添加代码

```
\themes\spfk\layout\_partial\left-col.ejs

<form>                
    <input type="text" class="st-default-search-input search" id="search" placeholder=" Search..." autocomplete="off" autocorrect="off" autocapitalize="off">
</form>


D:\Hexo\Hexo\themes\spfk\layout\_partial\after-footer.ejs

<script type="text/javascript">
  window.onload = function(){
    document.getElementById("search").onclick = function(){
        console.log("search")
        search();
    }
  }
  function search(){
    (function(w,d,t,u,n,s,e){w['SwiftypeObject']=n;w[n]=w[n]||function(){
    (w[n].q=w[n].q||[]).push(arguments);};s=d.createElement(t);
    e=d.getElementsByTagName(t)[0];s.async=1;s.src=u;e.parentNode.insertBefore(s,e);
    })(window,document,'script','//s.swiftypecdn.com/install/v2/st.js','_st');

    _st('install','A1Pz-LKMXbrzcFg2FWi6','2.0.0');
  }
</script>
```

>修改样式
```
.search {
    width: 68%;
    height: 18px;
    margin-top: 1px;
    padding: 0;
    font-family: inherit;
    border: 2px solid transparent;
    border-bottom: 2px solid lightgray;
    border-radius: 2px;
    opacity: .7;
    background: none;
    &:hover {
        border: 0px solid lightgray;
        opacity: 1;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
    };
}


display: inline-block;
    width: 190px;
    height: 16px;
    padding: 7px 11px 7px 28px;
    border: 1px solid #bbb;
    border: 1px solid rgba(0,0,0,0.25);
    font-weight: 400;
    color: #444;
    font-size: 14px;
    line-height: 16px;
    box-sizing: content-box;
    background: #fff 8px 8px no-repeat url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA0AAAANCAYAAABy6%2BR8AAAACXB…Hx4Taq1nrnKaW8K6XUUsrHWuvNevdRRLzFGwzvDbXAB9cDAHvhedDruuxSAAAAAElFTkSuQmCC);
    background-clip: padding-box;
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    -ms-border-radius: 5px;
    -o-border-radius: 5px;
    border-radius: 5px;
    -webkit-box-shadow: none;
    -moz-box-shadow: none;
    box-shadow: none;
    font-family: "Helvetica Neue",Helvetica,Arial,"Lucida Grande",sans-serif;
```

## RSS不显示

```
    安装RSS插件

$ npm install hexo-generator-feed --save

    开启RSS功能

    编辑hexo/themes_config.yml，添加如下代码：

    rss: /atom.xml #rss地址  默认即可
```

## sitemap网站地图

```
1、安装插件：

$ npm install hexo-generator-sitemap --save
$ npm install hexo-generator-baidu-sitemap --save

2、在博客目录的hexo/_config.yml中添加如下代码：

# 自动生成sitemap编辑

sitemap:
path: sitemap.xml
baidusitemap:
path: baidusitemap.xml

3、hexo编译的时候会自动在根目录生成站点地图

    UUhike@UUhike-pc MINGW64 /d/Hexo/Hexo (master)
    $ npm install hexo-generator-sitemap --save
    npm WARN install Couldn't install optional dependency: Unsupported
    npm WARN install Couldn't install optional dependency: Unsupported
    hexo-site@0.0.0 D:\Hexo\Hexo
    └── hexo-generator-sitemap@1.0.1


    UUhike@UUhike-pc MINGW64 /d/Hexo/Hexo (master)
    $ npm install hexo-generator-baidu-sitemap --save
    npm WARN install Couldn't install optional dependency: Unsupported
    npm WARN install Couldn't install optional dependency: Unsupported
    hexo-site@0.0.0 D:\Hexo\Hexo
    └── hexo-generator-baidu-sitemap@0.1.2


    UUhike@UUhike-pc MINGW64 /d/Hexo/Hexo (master)
    $ hexo g
    INFO  Files loaded in 2.42 s
    INFO  Generated: baidusitemap.xml
    INFO  Generated: sitemap.xml
    INFO  2 files generated in 477 ms

```
## 多说评论插件

>添加代码

```
    ├── spfk
        ├── _config.yml #主题配置文件
    #是否开启多说评论，填写你在多说申请的项目名称 duoshuo: duoshuo-key（http://duoshuo-key.duoshuo.com/）
    #若使用disqus，请在博客config文件中填写disqus_shortname，并关闭多说评论
    duoshuo: true


复制到 themes\landscape\layout\_partial\article.ejs把

<% if (!index && post.comments && config.disqus_shortname){ %>
<section id="comments">
<div id="disqus_thread">
<noscript>Please enable JavaScript to view the <a href="//disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
</div>
</section>
<% } %>

改为

<% if (!index && post.comments && config.disqus_shortname){ %>
<section id="comments">
<!-- 多说评论框 start -->
<div class="ds-thread" data-thread-key="<%= post.layout %>-<%= post.slug %>" data-title="<%= post.title %>" data-url="<%= page.permalink %>"></div>
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:'<%= config.disqus_shortname %>'};
  (function() {
    var ds = document.createElement('script');
    ds.type = 'text/javascript';ds.async = true;
    ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
    ds.charset = 'UTF-8';
    (document.getElementsByTagName('head')[0] 
     || document.getElementsByTagName('body')[0]).appendChild(ds);
  })();
  </script>
<!-- 多说公共JS代码 end -->
</section>
<% } %>
```

>修改样式：[V说：](http://www.vsay.cn/one-more-custom-css-lets-you-say-comments-city.html)[设计达人：](http://www.shejidaren.com/use-css3-to-create-a-beautiful-comment-ui.html)[设计达人：](http://www.shejidaren.com/examples/comment-ui/)

>添加方法：
添加方法：打开「后台」 > 「多说评论」 > 「设置」 > 「基本设置」 > 然后把样式粘贴到「自定义CSS」文本框 > 「保存」

个性样式：

```
#ds-thread #ds-reset ul.ds-comments-tabs li.ds-tab a.ds-current{border:0;color:#848568;text-shadow:none;background:#dddfc2}#ds-thread #ds-reset .ds-highlight{font-family:Arial,Helvetica,sans-serif;font-size:14px;font-weight:700;color:#848568!important}#ds-thread #ds-reset ul.ds-comments-tabs li.ds-tab a.ds-current:hover{color:#696a52;background:#d4d6ba}#ds-thread #ds-reset a.ds-highlight:hover{color:#696a52!important}#ds-thread{padding-left:30px}#ds-thread #ds-reset #ds-hot-posts,#ds-thread #ds-reset li.ds-post{overflow:visible}#ds-thread #ds-reset .ds-post-self{padding:10px 0 10px 10px}#ds-thread #ds-reset .ds-post-self,#ds-thread #ds-reset li.ds-post{border:0!important}#ds-reset .ds-avatar,#ds-thread #ds-reset ul.ds-children .ds-avatar{position:absolute;top:26px;left:-14px;padding:5px;width:36px;height:36px;box-shadow:-1px 0 1px rgba(0,0,0,.15) inset;border-radius:46px;background:#E5E6D0}#ds-thread #ds-reset ul.ds-children .ds-avatar{left:-23px}#ds-thread .ds-avatar a{display:inline-block;padding:1px;width:32px;height:32px;border:1px solid #b9baa6;border-radius:50%;background-color:#fff!important}#ds-thread .ds-avatar a:hover{border-color:#de5a4e}#ds-thread .ds-avatar>img{margin:2px 0 0 2px}#ds-thread #ds-reset .ds-replybox{box-shadow:none}#ds-reset .ds-replybox.ds-inline-replybox a.ds-avatar,#ds-thread #ds-reset ul.ds-children .ds-replybox.ds-inline-replybox a.ds-avatar{left:0;top:0;padding:0;width:32px!important;height:32px!important;background:0 0;box-shadow:none}#ds-reset .ds-replybox.ds-inline-replybox a.ds-avatar img{width:32px!important;height:32px!important;border-radius:50%}#ds-reset .ds-replybox .ds-avatar img,#ds-reset .ds-replybox a.ds-avatar{padding:0;width:50px!important;height:50px!important;border-radius:5px}#ds-reset .ds-avatar img{width:32px!important;height:32px!important;border-radius:32px;box-shadow:0 1px 3px rgba(0,0,0,.22);-webkit-transition:.4s all ease-in-out;-moz-transition:.4s all ease-in-out;-o-transition:.4s all ease-in-out;-ms-transition:.4s all ease-in-out;transition:.4s all ease-in-out}.ds-post-self:hover .ds-avatar img{-webkit-transform:rotate(360deg);-moz-transform:rotate(360deg);-o-transform:rotate(360deg);-ms-transform:rotate(360deg);transform:rotate(360deg)}#ds-thread #ds-reset .ds-comment-body{background:#F0F0E3;padding:15px 15px 12px 32px;border-radius:5px;box-shadow:0 1px 2px rgba(0,0,0,.15),0 1px 0 rgba(255,255,255,.75) inset}#ds-thread #ds-reset .ds-comment-body p{color:#787968}#ds-thread #ds-reset .ds-comments a.ds-user-name{font-weight:700;color:#696A52!important}#ds-thread #ds-reset .ds-comments a.ds-user-name:hover{color:#D32!important}#ds-thread #ds-reset #ds-hot-posts{border:0}#ds-reset #ds-hot-posts .ds-gradient-bg{background:0 0}#ds-reset #ds-bubble #ds-ctx .ds-ctx-entry{padding:0}#ds-reset #ds-bubble #ds-ctx-bubble .ds-avatar a,#ds-reset #ds-bubble .ds-avatar{position:static;padding:0;border:0;background:0 0;box-shadow:none}#ds-reset #ds-bubble #ds-ctx-bubble .ds-avatar a,#ds-reset #ds-bubble .ds-avatar img{width:45px!important;height:45px!important}#ds-reset #ds-bubble .ds-user-name{padding-left:13px}#ds-reset .ds-comment-body #ds-ctx{border-left:1px solid #b9baa6;background-color:#e8e8dc!important}#ds-reset #ds-ctx{margin-right:-15px}#ds-reset #ds-ctx .ds-ctx-entry{position:relative;padding:10px 30px 10px 10px}#ds-reset #ds-ctx .ds-ctx-entry .ds-avatar{top:6px;left:5px;background:0 0;box-shadow:none}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-body{margin-left:46px}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-content{color:#787968}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-head a{color:#696A52;font-weight:700}
```

个性样式：
[震动效果]: http://www.vsay.cn/wp-content/uploads/2012/09/7.gif
```
#ds-reset .ds-avatar img,#ds-reset .ds-avatar img:hover{-webkit-animation-fill-mode:both;-moz-animation-fill-mode:both;-ms-animation-fill-mode:both;-o-animation-fill-mode:both;animation-fill-mode:both;-webkit-animation-duration:0s;-moz-animation-duration:0s;-ms-animation-duration:0s;-o-animation-duration:0s;animation-duration:0s;-webkit-animation-duration:.7s;-moz-animation-duration:.7s;-ms-animation-duration:.7s;-o-animation-duration:.7s;animation-duration:.7s}@-webkit-keyframes wobble{0%{-webkit-transform:translateX(0)}15%{-webkit-transform:translateX(-25%) rotate(-5deg)}30%{-webkit-transform:translateX(20%) rotate(3deg)}45%{-webkit-transform:translateX(-15%) rotate(-3deg)}60%{-webkit-transform:translateX(10%) rotate(2deg)}75%{-webkit-transform:translateX(-5%) rotate(-1deg)}100%{-webkit-transform:translateX(0)}}@-moz-keyframes wobble{0%{-moz-transform:translateX(0)}15%{-moz-transform:translateX(-25%) rotate(-5deg)}30%{-moz-transform:translateX(20%) rotate(3deg)}45%{-moz-transform:translateX(-15%) rotate(-3deg)}60%{-moz-transform:translateX(10%) rotate(2deg)}75%{-moz-transform:translateX(-5%) rotate(-1deg)}100%{-moz-transform:translateX(0)}}@-o-keyframes wobble{0%{-o-transform:translateX(0)}15%{-o-transform:translateX(-25%) rotate(-5deg)}30%{-o-transform:translateX(20%) rotate(3deg)}45%{-o-transform:translateX(-15%) rotate(-3deg)}60%{-o-transform:translateX(10%) rotate(2deg)}75%{-o-transform:translateX(-5%) rotate(-1deg)}100%{-o-transform:translateX(0)}}@keyframes wobble{0%{transform:translateX(0)}15%{transform:translateX(-25%) rotate(-5deg)}30%{transform:translateX(20%) rotate(3deg)}45%{transform:translateX(-15%) rotate(-3deg)}60%{transform:translateX(10%) rotate(2deg)}75%{transform:translateX(-5%) rotate(-1deg)}100%{transform:translateX(0)}}#ds-reset .ds-avatar img:hover{-webkit-animation-name:wobble;-moz-animation-name:wobble;-o-animation-name:wobble;animation-name:wobble}
```

个性样式：
[MOxFIVE]: http://www.vsay.cn/wp-content/uploads/2012/09/7.gif
```
#ds-ssr{display:none !important}#ds-reset{font-weight:normal;font-size:13px;font-size-adjust:none;color:#333;line-height:1;text-align:left}#ds-reset *{-webkit-box-sizing:content-box;-moz-box-sizing:content-box;box-sizing:content-box}#ds-reset input[type=button],#ds-reset input[type=submit],#ds-reset input[type=reset],#ds-reset button{-webkit-box-sizing:border-box;-moz-box-sizing:border-box;box-sizing:border-box}#ds-reset div,#ds-reset ul,#ds-reset ol,#ds-reset li,#ds-reset ul li,#ds-reset ol li,#ds-reset iframe,#ds-reset h1,#ds-reset h2,#ds-reset h3,#ds-reset h4,#ds-reset h5,#ds-reset h6,#ds-reset p,#ds-reset img,#ds-reset blockquote,#ds-reset a,#ds-reset span,#ds-reset pre,#ds-reset code,#ds-reset strong,#ds-reset sub,#ds-reset sup,#ds-reset fieldset,#ds-reset form,#ds-reset label,#ds-reset input,#ds-reset textarea,#ds-reset select{border:0;padding:0;margin:0;vertical-align:baseline;font:inherit;line-height:inherit;background:none;width:auto;float:none;overflow:visible;transition:none}#ds-reset strong{font-weight:bold}#ds-reset p{text-indent:0;clear:none}#ds-reset span,#ds-reset strong,#ds-reset label,#ds-reset input{display:inline;margin:0}#ds-reset textarea:focus,#ds-reset input:focus{outline:none}#ds-reset ul,#ds-reset ol,#ds-reset ul li,#ds-reset ol li{list-style:none;display:block}#ds-reset input,#ds-reset button{-webkit-border-radius:3px;border-radius:3px}#ds-indicator{display:none;position:fixed;z-index:99999;top:150px;left:0;padding:8px;border-bottom-right-radius:3px;border-top-right-radius:3px;width:16px;height:16px;background:#666 url("data:image/gif;base64,R0lGODlhEAAQAPQAAGZmZv///2lpadzc3K+vr/r6+ufn5319fZmZmfDw8Le3t8DAwHV1daOjo4eHh9LS0srKygAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACH/C05FVFNDQVBFMi4wAwEAAAAh/hpDcmVhdGVkIHdpdGggYWpheGxvYWQuaW5mbwAh+QQJCgAAACwAAAAAEAAQAAAFUCAgjmRpnqUwFGwhKoRgqq2YFMaRGjWA8AbZiIBbjQQ8AmmFUJEQhQGJhaKOrCksgEla+KIkYvC6SJKQOISoNSYdeIk1ayA8ExTyeR3F749CACH5BAkKAAAALAAAAAAQABAAAAVoICCKR9KMaCoaxeCoqEAkRX3AwMHWxQIIjJSAZWgUEgzBwCBAEQpMwIDwY1FHgwJCtOW2UDWYIDyqNVVkUbYr6CK+o2eUMKgWrqKhj0FrEM8jQQALPFA3MAc8CQSAMA5ZBjgqDQmHIyEAIfkECQoAAAAsAAAAABAAEAAABWAgII4j85Ao2hRIKgrEUBQJLaSHMe8zgQo6Q8sxS7RIhILhBkgumCTZsXkACBC+0cwF2GoLLoFXREDcDlkAojBICRaFLDCOQtQKjmsQSubtDFU/NXcDBHwkaw1cKQ8MiyEAIfkECQoAAAAsAAAAABAAEAAABVIgII5kaZ6AIJQCMRTFQKiDQx4GrBfGa4uCnAEhQuRgPwCBtwK+kCNFgjh6QlFYgGO7baJ2CxIioSDpwqNggWCGDVVGphly3BkOpXDrKfNm/4AhACH5BAkKAAAALAAAAAAQABAAAAVgICCOZGmeqEAMRTEQwskYbV0Yx7kYSIzQhtgoBxCKBDQCIOcoLBimRiFhSABYU5gIgW01pLUBYkRItAYAqrlhYiwKjiWAcDMWY8QjsCf4DewiBzQ2N1AmKlgvgCiMjSQhACH5BAkKAAAALAAAAAAQABAAAAVfICCOZGmeqEgUxUAIpkA0AMKyxkEiSZEIsJqhYAg+boUFSTAkiBiNHks3sg1ILAfBiS10gyqCg0UaFBCkwy3RYKiIYMAC+RAxiQgYsJdAjw5DN2gILzEEZgVcKYuMJiEAOwAAAAAAAAAAAA==") 8px 8px no-repeat;*background-image:url("//static.duoshuo.com/images/loading.gif");*position:absolute;-webkit-box-sizing:content-box;-moz-box-sizing:content-box;box-sizing:content-box}#ds-waiting{cursor:wait;display:block;width:16px;height:11px;padding:0 0 3px 5px;margin:0;background:url("data:image/gif;base64,R0lGODlhEAALAPQAAP///z2LqeLt8dvp7u7090GNqz2LqV+fuJ/F1IW2ycrf51aatHWswaXJ14i4ys3h6FmctUCMqniuw+vz9eHs8fb5+meku+Tu8vT4+cfd5bbT3tbm7PH2+AAAAAAAAAAAACH/C05FVFNDQVBFMi4wAwEAAAAh/hpDcmVhdGVkIHdpdGggYWpheGxvYWQuaW5mbwAh+QQJCwAAACwAAAAAEAALAAAFLSAgjmRpnqSgCuLKAq5AEIM4zDVw03ve27ifDgfkEYe04kDIDC5zrtYKRa2WQgAh+QQJCwAAACwAAAAAEAALAAAFJGBhGAVgnqhpHIeRvsDawqns0qeN5+y967tYLyicBYE7EYkYAgAh+QQJCwAAACwAAAAAEAALAAAFNiAgjothLOOIJAkiGgxjpGKiKMkbz7SN6zIawJcDwIK9W/HISxGBzdHTuBNOmcJVCyoUlk7CEAAh+QQJCwAAACwAAAAAEAALAAAFNSAgjqQIRRFUAo3jNGIkSdHqPI8Tz3V55zuaDacDyIQ+YrBH+hWPzJFzOQQaeavWi7oqnVIhACH5BAkLAAAALAAAAAAQAAsAAAUyICCOZGme1rJY5kRRk7hI0mJSVUXJtF3iOl7tltsBZsNfUegjAY3I5sgFY55KqdX1GgIAIfkECQsAAAAsAAAAABAACwAABTcgII5kaZ4kcV2EqLJipmnZhWGXaOOitm2aXQ4g7P2Ct2ER4AMul00kj5g0Al8tADY2y6C+4FIIACH5BAkLAAAALAAAAAAQAAsAAAUvICCOZGme5ERRk6iy7qpyHCVStA3gNa/7txxwlwv2isSacYUc+l4tADQGQ1mvpBAAIfkECQsAAAAsAAAAABAACwAABS8gII5kaZ7kRFGTqLLuqnIcJVK0DeA1r/u3HHCXC/aKxJpxhRz6Xi0ANAZDWa+kEAA7AAAAAAAAAAAA") 0 0 no-repeat;*background-image:url("//static.duoshuo.com/images/waiting.gif")}#ds-reset .ds-highlight{color:#d32 !important}#ds-reset .ds-rounded{-webkit-border-radius:3px;border-radius:3px}#ds-reset .ds-rounded-top{-webkit-border-top-right-radius:3px;-webkit-border-top-left-radius:3px;border-top-right-radius:3px;border-top-left-radius:3px}#ds-reset .ds-rounded-bottom{border-bottom-left-radius:3px;border-bottom-right-radius:3px;-webkit-border-bottom-left-radius:3px;-webkit-border-bottom-right-radius:3px}#ds-reset .ds-gradient-bg{background:url("//static.duoshuo.com/images/bg_sprites.png") 0 -60px repeat-x}#ds-reset .ds-avatar{box-shadow:0 1px 1px rgba(255,255,255,0.75);position:relative;-webkit-border-radius:3px;border-radius:3px;background-color:#fff;float:left}#ds-reset .ds-avatar img{display:block;width:50px;height:50px;max-width:none;box-shadow:0 1px 3px rgba(0,0,0,0.22);-webkit-border-radius:3px;border-radius:3px}#ds-reset .ds-avatar .ds-service-icon{display:block;position:absolute;bottom:-4px;right:-4px}#ds-reset img.ds-smiley{margin:0;padding:0;display:inline;border:none}#ds-reset .ds-icon{vertical-align:middle;display:inline-block;overflow:hidden;background:transparent url("//static.duoshuo.com/images/sprites.png") no-repeat;_background-image:url("//static.duoshuo.com/images/sprites.gif")}#ds-reset a .ds-icon{opacity:.6;-webkit-transition:opacity .15s linear;-moz-transition:opacity .15s linear;transition:opacity .15s linear}#ds-reset a:hover .ds-icon{opacity:1}#ds-reset .ds-service-list a{vertical-align:middle;padding-right:3px}#ds-reset .ds-service-list li:hover a{color:#333}#ds-reset .ds-service-icon,#ds-reset .ds-service-icon-grey{width:16px !important;height:16px !important;line-height:100px;display:inline-block;background:url("//static.duoshuo.com/images/service-icons-color.png?v=2") no-repeat;_background-image:url("//static.duoshuo.com/images/service-icons-color.gif?v=2");overflow:hidden}#ds-reset .ds-service-icon-grey{background-image:url("//static.duoshuo.com/images/service-icons-grey.png");_background-image:url("//static.duoshuo.com/images/service-icons-grey.gif")}#ds-reset .ds-service-link{height:16px !important;line-height:16px;padding-left:20px;display:block;background:url("//static.duoshuo.com/images/service-icons-color.png?v=2") no-repeat;_background-image:url("//static.duoshuo.com/images/service-icons-color.gif?v=2");overflow:hidden}#ds-reset .ds-weibo{background-position:0 0}#ds-reset .ds-renren{background-position:0 -32px}#ds-reset .ds-qqt{background-position:0 -64px}#ds-reset .ds-kaixin{background-position:0 -80px}#ds-reset .ds-douban{background-position:0 -96px}#ds-reset .ds-qzone{background-position:0 -128px}#ds-reset .ds-duoshuo{background-position:0 -144px}#ds-reset .ds-qq{background-position:0 -192px}#ds-reset .ds-baidu{background-position:0 -208px}#ds-reset .ds-google{background-position:0 -240px}#ds-reset .ds-weixin{background-position:0 -272px}#ds-reset .ds-wechat{background-position:0 -272px}.ds-icons-32 a{display:block;cursor:pointer;width:32px !important;height:32px !important;background:url("//static.duoshuo.com/images/icons_large.png") no-repeat !important;overflow:hidden;text-indent:-9999px}.ds-icons-32 a.ds-weibo{background-position:-37px 0 !important}.ds-icons-32 a.ds-qzone{background-position:0 0 !important}.ds-icons-32 a.ds-qqt{background-position:-74px 0 !important}.ds-icons-32 a.ds-renren{background-position:-148px 0 !important}.ds-icons-32 a.ds-kaixin{background-position:-111px 0 !important}.ds-icons-32 a.ds-weixin{background-position:-224px 0 !important}.ds-icons-32 a.ds-wechat{background-position:-224px 0 !important}.ds-icons-32 a.ds-qq{background-position:-488px 0 !important}.ds-icons-32 a.ds-douban{background-position:-186px 0 !important}.ds-icons-32 a.ds-baidu{background-position:-262px 0 !important}#ds-reset #ds-ctx{padding:0;margin:8px 0;max-width:640px}#ds-reset #ds-ctx .ds-ctx-entry{padding:6px;margin:0;border-bottom:1px solid #e6e6e6}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-head a{color:#e77064}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-head a:hover{color:#d32}#ds-reset #ds-ctx .ds-ctx-entry .ds-avatar{margin:0;width:30px;height:30px}#ds-reset #ds-ctx .ds-ctx-entry .ds-avatar img{width:30px;height:30px;box-shadow:none}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-body{margin-left:38px}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-head{position:relative;_zoom:1;line-height:1em;padding:1px 0 0;margin:0 0 .25em}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-nth{color:#999;font-size:12px;position:absolute;top:2px;right:0}#ds-reset #ds-ctx .ds-ctx-entry .ds-time{font-size:12px;*font-size:12px;margin-left:8px;color:#999;_zoom:1}#ds-reset #ds-ctx .ds-ctx-entry .ds-ctx-content{position:relative;_zoom:1;padding:0;margin:0;overflow:hidden;line-height:1.5em}#ds-reset #ds-ctx .ds-ctx-entry:hover .ds-comment-actions{display:block}#ds-reset #ds-ctx .ds-comment-actions{bottom:0;right:0;line-height:18px;position:absolute;display:none;*display:block}#ds-reset #ds-ctx .ds-comment-actions a{margin:0 0 0 6px}#ds-reset.ds-touch #ds-ctx .ds-ctx-entry .ds-comment-actions{display:block}#ds-reset .ds-comment-body #ds-ctx{border-left:3px solid #ccc;background-color:rgba(0,0,0,0.03)}#ds-reset.ds-no-opacity .ds-comment-body #ds-ctx{background:#f8f8f8}#ds-reset .ds-dialog-body #ds-ctx .ds-ctx-entry:hover .ds-comment-actions{display:none}#ds-thread{clear:both;position:relative;overflow:visible;_zoom:1}#ds-thread #ds-reset a{cursor:pointer;text-decoration:none;color:#777;background-color:transparent;-webkit-transition:color .15s linear;-moz-transition:color .15s linear;transition:color .15s linear}#ds-thread #ds-reset a:hover{color:#333}#ds-thread #ds-reset ul,#ds-thread #ds-reset ul li{background:none;margin:0;padding:0}#ds-thread #ds-reset .ds-arrow{position:absolute;width:0;height:0;font-size:0 !important;line-height:0 !important;_border-right-color:#ffc0cb !important;_border-left-color:#ffc0cb !important;_filter:chroma(color=#ffc0cb) !important}#ds-thread #ds-reset .ds-arrow-down{border-left:5px solid transparent;border-right:5px solid transparent;border-top:5px solid #fff}#ds-thread #ds-reset button{cursor:pointer;margin:0;padding:0;border-radius:0}#ds-thread #ds-reset .ds-meta{position:relative;padding:8px 0;line-height:24px;border-bottom:1px solid rgba(0,0,0,0.13)}#ds-thread #ds-reset .ds-like-tooltip{position:absolute;z-index:9999;background-color:#fcfcfc;background-repeat:repeat-x;background-image:-khtml-gradient(linear, left top, left bottom, from(#fcfcfc), to(#f9f9f9));background:-moz-linear-gradient(top, #fcfcfc 0, #f9f9f9 100%);background:-webkit-gradient(linear, left top, left bottom, color-stop(0, #fcfcfc), color-stop(100%, #f9f9f9));background:-webkit-linear-gradient(top, #fcfcfc 0, #f9f9f9 100%);background:-ms-linear-gradient(top, #fcfcfc 0, #f9f9f9 100%);background:linear-gradient(top, #fcfcfc 0, #f9f9f9 100%);filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#fcfcfc',endColorstr='#f9f9f9',GradientType=0);border:1px solid #aaa;box-shadow:0 0 2px rgba(0,0,0,0.2);text-shadow:0 1px 0 #fff;font-size:12px;padding:8px 14px}#ds-thread #ds-reset .ds-like-tooltip ul{width:84px;float:left}#ds-thread #ds-reset .ds-like-tooltip li{line-height:16px;margin:6px 0}#ds-thread #ds-reset .ds-like-tooltip p{clear:both;margin:6px 0}#ds-thread #ds-reset .ds-like-tooltip .ds-like-tooltip-footer{text-align:right}#ds-thread #ds-reset a.ds-like-thread-button{color:#555;padding:4px 8px;border:1px solid #ccc;border-bottom-color:#aaa;box-shadow:inset 0 0 1px #fff;margin-right:15px;text-shadow:0 1px 0 #fff;background-color:#e0e0e0;background-repeat:no-repeat;background-image:-webkit-gradient(linear, 0 0, 0 100%, from(#fff), color-stop(25%, #fff), to(#e0e0e0));background-image:-webkit-linear-gradient(#fff, #fff 25%, #e0e0e0);background-image:-moz-linear-gradient(top, #fff, #fff 25%, #e0e0e0);background-image:-ms-linear-gradient(#fff, #fff 25%, #e0e0e0);background-image:linear-gradient(#fff, #fff 25%, #e0e0e0)}#ds-thread #ds-reset a.ds-like-thread-button .ds-icon-heart{position:relative;top:-2px;opacity:1}#ds-thread #ds-reset a.ds-like-thread-button span{color:#555}#ds-thread #ds-reset .ds-thread-cancel-like{display:none}#ds-thread #ds-reset a.ds-thread-liked{background:#e9e9e9}#ds-thread #ds-reset a.ds-thread-liked:hover .ds-thread-cancel-like{display:inline}#ds-thread #ds-reset a.ds-thread-liked:hover .ds-thread-like-text{display:none}#ds-thread #ds-reset #ds-hot-posts{border:1px solid #ccc;overflow:hidden;margin:8px 0;padding:0;_height:100%}#ds-thread #ds-reset .ds-header{font-weight:bold;font-size:14px;color:#555;line-height:30px;padding:0 12px}#ds-thread #ds-reset .ds-toolbar{position:relative;z-index:5;font-size:12px;padding:8px 0;width:100%;clear:both}#ds-thread #ds-reset .ds-toolbar:after{content:".";display:block;height:0;clear:both;visibility:hidden}#ds-thread #ds-reset .ds-visitor{float:right;line-height:1.5em;margin-right:6px}#ds-thread #ds-reset .ds-account-control{float:right;position:relative;font-size:12px;cursor:pointer;text-align:right;line-height:18px;padding-bottom:3px;width:75px}#ds-thread #ds-reset .ds-account-control span{display:block;float:left;color:#999}#ds-thread #ds-reset .ds-account-control ul{display:none;position:absolute;top:19px;left:0;border:1px solid #aaa;background:#f8f8f8;box-shadow:inset 0 1px 1px #fff,0 1px 1px rgba(0,0,0,0.3);border-radius:3px;text-align:center}#ds-thread #ds-reset .ds-account-control ul li a{border-top:1px solid #fff;border-bottom:1px solid #ddd;display:block;padding:3px 10px;text-shadow:0 1px 0 #fff}#ds-thread #ds-reset .ds-account-control ul li a:hover{color:#555}#ds-thread #ds-reset .ds-account-control.ds-active span{color:#555}#ds-thread #ds-reset .ds-account-control.ds-active ul{display:block}#ds-thread #ds-reset .ds-alert{margin:.5em 0;border:1px solid #fbeed5;border-radius:3px;padding:6px 6px;color:#c09853;background-color:#fcf8e3;line-height:1.5em}#ds-thread #ds-reset .ds-login-buttons{width:100%;position:relative;padding:10px 0 6px}#ds-thread #ds-reset .ds-login-buttons p{float:left;line-height:24px;margin:0}#ds-thread #ds-reset .ds-login-buttons .ds-service-list li{float:left;height:16px;width:54px;padding:4px 0;margin:0 0 0 6px}#ds-thread #ds-reset .ds-login-buttons .ds-more-services{color:#d32 !important;line-height:16px;display:block}#ds-thread #ds-reset .ds-login-buttons .ds-more-services:hover{color:#e77064 !important}#ds-thread #ds-reset .ds-login-buttons .ds-additional-services{display:none}#ds-thread #ds-reset .ds-login-buttons .ds-social-links{float:left;width:306px}#ds-thread #ds-reset .ds-login-buttons:after{content:".";display:block;height:0;clear:both;visibility:hidden}#ds-thread #ds-reset a.ds-unread-comments-count{display:none;background-color:#d32;color:#fff !important;margin-right:6px;padding:1px 5px;font-weight:bold;-webkit-border-radius:5px;border-radius:5px;box-shadow:inset 0 1px 1px rgba(255,255,255,0.4),0 1px 1px rgba(0,0,0,0.3);text-shadow:0 1px 1px rgba(0,0,0,0.3)}#ds-thread #ds-reset a.ds-unread-comments-count:hover{background:#f00}#ds-thread #ds-reset .ds-replybox{width:auto;font-size:12px;z-index:3;margin:8px 0;padding:0 0 0 60px;position:relative;_zoom:1}#ds-thread #ds-reset .ds-replybox:after{content:".";display:block;height:0;clear:both;visibility:hidden}#ds-thread #ds-reset .ds-replybox .ds-avatar{position:absolute;top:0;left:0}#ds-thread #ds-reset .ds-replybox .ds-avatar img{width:50px;height:50px;visibility:visible;margin:0}#ds-thread #ds-reset .ds-inline-replybox{margin:8px 0 2px 0;padding-left:38px}#ds-thread #ds-reset .ds-inline-replybox .ds-avatar img{width:30px;height:30px;box-shadow:0 1px 2px rgba(0,0,0,0.22)}#ds-thread #ds-reset .ds-textarea-wrapper{position:relative;border:1px solid #ccc;border-bottom:none;padding-right:20px;background:#fff url("//static.duoshuo.com/images/bg_sprites.png") 0 -90px repeat-x;overflow:hidden}#ds-thread #ds-reset .ds-textarea-wrapper textarea{box-shadow:none;-webkit-appearance:none;overflow:auto;padding:10px;height:54px;margin:0;resize:none;color:#999;width:100%}#ds-thread #ds-reset .ds-textarea-wrapper textarea:focus{color:#333}#ds-thread #ds-reset .ds-textarea-wrapper .ds-hidden-text{word-wrap:break-word;visibility:hidden;position:absolute;top:0;left:10px;right:10px}#ds-thread #ds-reset .ds-textarea-wrapper textarea,#ds-thread #ds-reset .ds-textarea-wrapper .ds-hidden-text{display:block;font-family:"Helvetica Neue",Helvetica,Arial,sans-serif;font-size:13px;line-height:20px;border:none}#ds-thread #ds-reset .ds-post-toolbar{position:relative;width:100%;box-shadow:0 1px 0 rgba(255,255,255,0.6)}#ds-thread #ds-reset .ds-post-toolbar span,#ds-thread #ds-reset .ds-post-toolbar input,#ds-thread #ds-reset .ds-post-toolbar label,#ds-thread #ds-reset .ds-post-toolbar a{vertical-align:middle;width:auto}#ds-thread #ds-reset .ds-post-options{position:relative;margin-right:100px;height:30px;border:1px solid #ccc;border-right:none;border-bottom-color:#aaa;border-bottom-left-radius:3px;-webkit-border-bottom-left-radius:3px}#ds-thread #ds-reset .ds-toolbar-buttons{position:absolute;top:5px;left:6px}#ds-thread #ds-reset .ds-toolbar-button{display:block;width:19px !important;height:19px;float:left;margin-right:4px;background:transparent url("//static.duoshuo.com/images/sprites.png") no-repeat;_background-image:url("//static.duoshuo.com/images/sprites.gif");vertical-align:middle;opacity:.6;-webkit-transition:opacity .15s linear;-moz-transition:opacity .15s linear;transition:opacity .15s linear}#ds-thread #ds-reset .ds-toolbar-button:hover{opacity:1}#ds-thread #ds-reset .ds-add-image{background-position:0 -48px}#ds-thread #ds-reset .ds-add-image:hover{background-position:0 -66px}#ds-thread #ds-reset .ds-add-emote{background-position:0 -12px}#ds-thread #ds-reset .ds-add-emote:hover{background-position:0 -30px}#ds-thread #ds-reset .ds-sync{font-size:12px;color:#999;line-height:30px;position:absolute;right:5px}#ds-thread #ds-reset .ds-sync label{color:#777;cursor:pointer;-webkit-transition:color .15s linear;-moz-transition:color .15s linear;transition:color .15s linear}#ds-thread #ds-reset .ds-sync label:hover{color:#555}#ds-thread #ds-reset .ds-sync a.ds-service-icon,#ds-thread #ds-reset .ds-sync a.ds-service-icon-grey{margin:7px 2px 7px 3px}#ds-thread #ds-reset .ds-post-button{font-family:"Helvetica Neue",Helvetica,Arial,sans-serif;position:absolute;right:0;top:0;height:32px;width:100px;text-align:center;text-shadow:0 1px 0 #fff;color:#555;font-size:14px;font-weight:bold;border:1px solid #ccc;border-bottom-color:#aaa;border-bottom-right-radius:3px;-webkit-border-bottom-right-radius:3px;background-color:#e6e6e6;background-repeat:no-repeat;background-image:-webkit-gradient(linear, 0 0, 0 100%, from(#fcfcfc), color-stop(25%, #fcfcfc), to(#e6e6e6));background-image:-webkit-linear-gradient(#fcfcfc, #fcfcfc 25%, #e6e6e6);background-image:-moz-linear-gradient(top, #fcfcfc, #fcfcfc 25%, #e6e6e6);background-image:-ms-linear-gradient(#fcfcfc, #fcfcfc 25%, #e6e6e6);background-image:linear-gradient(#fcfcfc, #fcfcfc 25%, #e6e6e6);-webkit-transition:all .15s linear;-moz-transition:all .15s linear;transition:all .15s linear;box-shadow:inset 0 0 1px #fff}#ds-thread #ds-reset .ds-post-button:hover{background-position:0 -15px;color:#333}#ds-thread #ds-reset .ds-post-button:active{-webkit-box-shadow:inset 0 2px 4px rgba(0,0,0,0.15),0 1px 2px rgba(0,0,0,0.05);box-shadow:inset 0 2px 4px rgba(0,0,0,0.15),0 1px 2px rgba(0,0,0,0.05)}#ds-thread #ds-reset .ds-comments-info{width:100%;font-size:13px;margin-top:10px;padding:8px 0;line-height:25px;position:relative}#ds-thread #ds-reset .ds-sort{position:absolute;right:0;top:8px}#ds-thread #ds-reset .ds-sort a{color:#999;padding:0 4px;margin:0 2px}#ds-thread #ds-reset .ds-sort a:hover{color:#333}#ds-thread #ds-reset .ds-sort a.ds-current,#ds-thread #ds-reset .ds-sort a:active{color:#d32}#ds-thread #ds-reset ul.ds-comments-tabs .ds-highlight{margin:0 2px 0 0}#ds-thread #ds-reset ul.ds-comments-tabs .ds-service-icon{vertical-align:middle;margin:0 2px 1px 0}#ds-thread #ds-reset li.ds-tab{display:inline;font-size:13px;margin:0 5px 0 0;padding:0}#ds-thread #ds-reset li.ds-tab a{text-shadow:0 1px 0 #fff;padding:3px 5px;display:inline;-webkit-border-radius:5px;border-radius:5px}#ds-thread #ds-reset li.ds-tab a.ds-current{border:1px solid #ccc;background-color:rgba(0,0,0,0.04)}#ds-thread #ds-reset li.ds-tab a:hover{background-color:rgba(0,0,0,0.04)}#ds-thread #ds-reset .ds-comments{width:100%;border-bottom:1px solid rgba(0,0,0,0.13)}#ds-thread #ds-reset .ds-comments:after{content:".";display:block;height:0;clear:both;visibility:hidden}#ds-thread #ds-reset li.ds-post{width:100%;overflow:hidden;clear:both;border-top:1px solid rgba(0,0,0,0.13);margin:0;padding:0;list-style:none}#ds-thread #ds-reset li.ds-post-placeholder{text-align:center;color:#999;padding:1em 0}#ds-thread #ds-reset .ds-post-self{position:relative;padding:10px;border-top:1px solid rgba(255,255,255,0.7)}#ds-thread #ds-reset .ds-post-self:after{content:".";display:block;height:0;clear:both;visibility:hidden}#ds-thread #ds-reset .ds-post-self:hover .ds-post-delete,#ds-thread #ds-reset .ds-post-self:hover .ds-post-report{display:inline-block}#ds-thread #ds-reset.ds-touch .ds-post-self .ds-post-delete,#ds-thread #ds-reset.ds-touch .ds-post-self .ds-post-report{display:inline-block}#ds-thread #ds-reset .ds-comment-body{padding-left:60px}#ds-thread #ds-reset .ds-comment-body p{font-size:13px;line-height:1.5em;margin:.5em 0;word-wrap:break-word}#ds-thread #ds-reset .ds-comment-body img{max-width:100%;vertical-align:text-bottom;box-shadow:none}#ds-thread #ds-reset .ds-comment-body embed{max-width:100%}#ds-thread #ds-reset .ds-comment-body code{display:block;font-size:12px;font-family:Monaco,Menlo,Consolas,"Courier New",monospace;padding:8px 12px;background-color:#f0f0f0;margin:8px 0;border-radius:3px;border:1px solid #ddd;color:#666}#ds-thread #ds-reset .ds-comment-body a{color:#777}#ds-thread #ds-reset .ds-comment-body a:hover{color:#555}#ds-thread #ds-reset a.ds-comment-context{position:relative;margin:.5em 0;color:#e77064}#ds-thread #ds-reset a.ds-comment-context:hover{color:#d32}#ds-thread #ds-reset #ds-bubble{position:absolute;background-color:#fff;-webkit-border-radius:5px;border-radius:5px;padding:10px;color:#333;border:1px solid #aaa;box-shadow:0 0 2px rgba(0,0,0,0.2);z-index:9999;font-size:13px}#ds-thread #ds-reset #ds-bubble a{color:#e77064}#ds-thread #ds-reset #ds-bubble a:hover{color:#d32}#ds-thread #ds-reset #ds-bubble p{line-height:18px}#ds-thread #ds-reset #ds-bubble .ds-arrow-border{border-top-color:#aaa;bottom:-6px}#ds-thread #ds-reset #ds-bubble .ds-arrow{left:32px;bottom:-5px}#ds-thread #ds-reset #ds-ctx-bubble{width:300px}#ds-thread #ds-reset #ds-ctx-bubble .ds-bubble-footer{padding:6px 0 0 0;line-height:18px}#ds-thread #ds-reset #ds-user-card{width:276px;min-height:50px}#ds-thread #ds-reset #ds-user-card .ds-avatar{margin-right:10px}#ds-thread #ds-reset #ds-user-card .ds-avatar img{width:50px;height:50px}#ds-thread #ds-reset #ds-user-card .ds-user-name{vertical-align:top;display:inline-block;max-width:8em;overflow:hidden;white-space:nowrap;text-overflow:ellipsis;font-size:14px}#ds-thread #ds-reset #ds-user-card .ds-user-card-meta{margin:14px 0 0 62px}#ds-thread #ds-reset #ds-user-card .ds-user-description{border-top:1px dotted #ddd;margin-top:.5em;color:#aaa}#ds-thread #ds-reset #ds-user-card .ds-service-icon{margin-right:3px}#ds-thread #ds-reset .ds-comment-header{padding-top:1px}#ds-thread #ds-reset .ds-comment-footer{line-height:1.5em}#ds-thread #ds-reset .ds-comment-footer a{margin:0 6px 0 0;padding:0 6px 0 0}#ds-thread #ds-reset .ds-comment-actions a{font-size:12px;color:#999}#ds-thread #ds-reset .ds-comment-actions a .ds-icon{position:relative;top:-1px}#ds-thread #ds-reset .ds-user-name{color:#777;font-size:13px;margin-right:8px}#ds-thread #ds-reset .ds-post-liked .ds-icon-like{background-position:0 -130px}#ds-thread #ds-reset .ds-post-liked a.ds-post-likes{color:#d32}#ds-thread #ds-reset .ds-reply-active{display:block}#ds-thread #ds-reset .ds-reply-active .ds-post-reply{color:#333}#ds-thread #ds-reset .ds-reply-active .ds-post-reply .ds-icon{opacity:1}#ds-thread #ds-reset .ds-post-delete,#ds-thread #ds-reset .ds-post-report{display:none}#ds-thread #ds-reset .ds-icon-heart{width:14px;height:13px;background-position:0 -130px}#ds-thread #ds-reset .ds-icon-settings{width:12px;height:12px;margin:3px 4px 0;opacity:1}#ds-thread #ds-reset .ds-icon-like{width:14px;height:13px;background-position:0 -117px}#ds-thread #ds-reset .ds-icon-share{width:18px;height:13px;background-position:0 -234px}#ds-thread #ds-reset .ds-icon-reply{width:18px;height:13px;background-position:0 -105px}#ds-thread #ds-reset .ds-icon-delete{width:13px;height:13px;background-position:0 -176px}#ds-thread #ds-reset .ds-icon-report{width:12px;height:12px;background-position:0 -189px}#ds-thread #ds-reset .ds-time{font-size:12px;*font-size:12px;margin-right:8px;color:#999;_zoom:1}#ds-thread #ds-reset ul.ds-children{margin-left:38px}#ds-thread #ds-reset ul.ds-children .ds-avatar{width:30px;height:30px}#ds-thread #ds-reset ul.ds-children .ds-avatar img{width:30px;height:30px}#ds-thread #ds-reset ul.ds-children .ds-post-self{padding-left:0}#ds-thread #ds-reset ul.ds-children .ds-comment-body{padding-left:38px}#ds-thread #ds-reset .ds-paginator{border-bottom:1px solid rgba(0,0,0,0.13);text-align:right;padding-bottom:15px;clear:both;line-height:1em}#ds-thread #ds-reset .ds-paginator div.ds-border{border-top:1px solid rgba(255,255,255,0.7);margin-bottom:15px}#ds-thread #ds-reset .ds-paginator a{font-size:12px;margin:0 3px;padding:2px 5px;border:1px solid transparent}#ds-thread #ds-reset .ds-paginator a:hover,#ds-thread #ds-reset .ds-paginator a.ds-current{-webkit-border-radius:3px;border-radius:3px;background-color:rgba(0,0,0,0.03)}#ds-thread #ds-reset .ds-paginator a.ds-current{color:#d32;border:1px solid #ccc}#ds-thread #ds-reset .ds-powered-by{font-size:12px;text-align:right;padding:10px 0}#ds-thread #ds-reset .ds-powered-by a{color:#999;text-decoration:none}#ds-thread #ds-reset .ds-powered-by a:hover{color:#555}#ds-thread #ds-reset.ds-no-transition .ds-post-button,#ds-thread #ds-reset.ds-no-transition .ds-more a{background:url("//static.duoshuo.com/images/bg_sprites.png") repeat-x !important}#ds-thread #ds-reset.ds-no-transition .ds-post-button:hover,#ds-thread #ds-reset.ds-no-transition .ds-more a:hover{background-position:0 -30px !important}#ds-thread #ds-reset.ds-no-transition .ds-like-thread-button{background:url("//static.duoshuo.com/images/bg_sprites.png") repeat-x}#ds-thread #ds-reset.ds-no-opacity li.ds-post{border-top:1px solid #ddd}#ds-thread #ds-reset.ds-no-opacity .ds-comments,#ds-thread #ds-reset.ds-no-opacity .ds-paginator{border-bottom:1px solid #ddd}#ds-thread #ds-reset.ds-no-opacity .ds-post-self{border-top:none}#ds-thread #ds-reset.ds-no-opacity .ds-tab a.ds-current{background:#f8f8f8}#ds-thread #ds-reset.ds-ie6 .ds-post-report,#ds-thread #ds-reset.ds-ie6 .ds-post-delete{display:inline !important}#ds-thread #ds-reset.ds-ie6 .ds-textarea-wrapper{padding:10px 10px}#ds-thread #ds-reset.ds-ie6 .ds-textarea-wrapper textarea{width:95%;color:#333;padding:0}#ds-thread #ds-reset.ds-ie6 .ds-post{width:100%;float:left}#ds-thread #ds-reset.ds-ie6 .ds-tab a.ds-current{padding-bottom:5px}#ds-thread #ds-reset.ds-ie6 #ds-ctx-bubble .ds-arrow{bottom:-8px}#ds-thread #ds-reset.ds-ie6 #ds-ctx-bubble .ds-arrow-border{bottom:-9px}#ds-thread.ds-narrow #ds-reset .ds-post-self{padding:8px}#ds-thread.ds-narrow #ds-reset .ds-comment-body,#ds-thread.ds-narrow #ds-reset .ds-replybox{padding-left:38px}#ds-thread.ds-narrow #ds-reset .ds-avatar img{width:30px;height:30px}#ds-thread.ds-narrow #ds-reset .ds-post-button{width:70px}#ds-thread.ds-narrow #ds-reset .ds-post-options{margin-right:70px}#ds-smilies-tooltip{border:1px solid #aaa;position:absolute;width:400px;background-color:#fff;z-index:9999;box-shadow:0 0 2px rgba(0,0,0,0.2);text-shadow:0 1px 0 #fff;-webkit-border-radius:3px;border-radius:3px}#ds-smilies-tooltip a{cursor:pointer}#ds-smilies-tooltip ul{list-style-type:none;padding:0;margin:0}#ds-smilies-tooltip ul.ds-smilies-tabs{width:119px;position:absolute;top:0;left:0;height:159px;overflow-y:auto;background:#f8f8f8;border-right:1px solid #ccc;border-top-left-radius:3px;border-bottom-left-radius:3px}#ds-smilies-tooltip ul.ds-smilies-tabs li a{color:#999;padding:6px 10px;display:block;border-bottom:1px solid #ccc;background-color:#fff;background-repeat:repeat-x;background-image:-khtml-gradient(linear, left top, left bottom, from(#fff), to(#e9e9e9));background:-moz-linear-gradient(top, #fff 0, #e9e9e9 100%);background:-webkit-gradient(linear, left top, left bottom, color-stop(0, #fff), color-stop(100%, #e9e9e9));background:-webkit-linear-gradient(top, #fff 0, #e9e9e9 100%);background:-ms-linear-gradient(top, #fff 0, #e9e9e9 100%);background:linear-gradient(top, #fff 0, #e9e9e9 100%);filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#ffffff',endColorstr='#e9e9e9',GradientType=0);font-size:12px;line-height:1.3em}#ds-smilies-tooltip ul.ds-smilies-tabs li:first-child a{border-top-left-radius:3px}#ds-smilies-tooltip ul.ds-smilies-tabs li a.ds-current{color:#666;background:#e3e3e3;filter:none;box-shadow:inset 0 0 4px rgba(0,0,0,0.1)}#ds-smilies-tooltip .ds-smilies-container{padding:10px 5px;height:140px;margin-left:125px;overflow-y:auto}#ds-smilies-tooltip .ds-smilies-container li{list-style:none;float:left;width:26px;height:26px;text-align:center;cursor:pointer}#ds-smilies-tooltip .ds-smilies-container li img{visibility:visible}#ds-smilies-tooltip .ds-smilies-container li:hover{position:relative;top:-1px}#ds-wrapper{left:0;right:0;top:20%;bottom:0;width:100%;margin:auto;z-index:9999;position:fixed;_position:absolute;text-align:center;color:#777}#ds-wrapper .ds-dialog,#ds-wrapper #ds-reset.ds-dialog{margin:0 auto;text-align:left;_zoom:1;width:480px;max-width:100%}#ds-wrapper #ds-reset.ds-dialog-bind-more .ds-service-list{width:50%}#ds-wrapper #ds-reset a{cursor:pointer;text-decoration:none;color:#777}#ds-wrapper #ds-reset .ds-dialog-inner{width:100%;position:relative;border:1px solid #aaa;background:#fff url("//static.duoshuo.com/images/bg_sprites.png") 0 -90px repeat-x;text-shadow:0 1px 0 #fff;box-shadow:inset 0 1px 1px #fff,0 2px 6px rgba(0,0,0,0.4)}#ds-wrapper #ds-reset .ds-dialog-inner .ds-unread-list{max-height:300px;overflow-y:auto}#ds-wrapper #ds-reset .ds-control-group{margin:18px 0;position:relative;padding-right:80px;max-width:166px}#ds-wrapper #ds-reset .ds-control-group input{color:#777;width:100%;font-size:13px;border:1px solid #ccc;padding:4px 80px 6px 6px;box-shadow:inset 0 1px 3px rgba(0,0,0,0.1)}#ds-wrapper #ds-reset .ds-control-group input:focus{border-color:#08b5fb}#ds-wrapper #ds-reset .ds-control-group label{font-size:13px;color:#ccc;letter-spacing:1px;position:absolute;right:0;top:8px}#ds-wrapper #ds-reset tr{height:45px}#ds-wrapper #ds-reset button{cursor:pointer;color:#555;background-color:#e6e6e6;background-repeat:no-repeat;background-image:-webkit-gradient(linear, 0 0, 0 100%, from(#fff), color-stop(25%, #fff), to(#e6e6e6));background-image:-webkit-linear-gradient(#fff, #fff 25%, #e6e6e6);background-image:-moz-linear-gradient(top, #fff, #fff 25%, #e6e6e6);background-image:-ms-linear-gradient(#fff, #fff 25%, #e6e6e6);background-image:linear-gradient(#fff, #fff 25%, #e6e6e6);-webkit-transition:all .15s linear;-moz-transition:all .15s linear;transition:all .15s linear;border:1px solid #aaa;display:inline-block;font-size:15px;height:30px;width:100px;padding:0}#ds-wrapper #ds-reset button:hover{background-position:0 -15px;color:#333}#ds-wrapper #ds-reset button:active{top:0;-webkit-box-shadow:inset 0 2px 4px rgba(0,0,0,0.15),0 1px 2px rgba(0,0,0,0.05);box-shadow:inset 0 2px 4px rgba(0,0,0,0.15),0 1px 2px rgba(0,0,0,0.05)}#ds-wrapper #ds-reset h2{display:block;font-weight:normal;font-size:16px;margin:0 0 15px 0;color:#555}#ds-wrapper #ds-reset .ds-dialog-body{padding:30px 30px 25px;position:relative;overflow:hidden}#ds-wrapper #ds-reset .ds-icons-32{height:32px;margin-bottom:20px;overflow:hidden}#ds-wrapper #ds-reset .ds-icons-32 a{float:left;margin:0 5px 0 0}#ds-wrapper #ds-reset ul li{margin:10px 0}#ds-wrapper #ds-reset .ds-service-list{width:45%;float:left}#ds-wrapper #ds-reset .ds-service-list .ds-more-services{display:none}#ds-wrapper #ds-reset .ds-icon-ok{background-position:0 -203px;width:12px;height:12px}#ds-wrapper #ds-reset .ds-quote{margin:10px 0;padding:6px 10px;background:#f8f8f8;line-height:1.5em;font-size:12px;overflow-y:auto;max-height:180px}#ds-wrapper #ds-reset .ds-textarea-wrapper{border:1px solid #ccc;padding:0 20px 0 0;position:relative;margin:12px 0}#ds-wrapper #ds-reset .ds-textarea-wrapper textarea{width:100%;height:54px;margin:0;resize:none;padding:6px 10px;overflow:auto}#ds-wrapper #ds-reset .ds-textarea-wrapper .ds-hidden-text{top:0;left:10px;right:10px;position:absolute;visibility:hidden;word-wrap:break-word}#ds-wrapper #ds-reset .ds-textarea-wrapper textarea,#ds-wrapper #ds-reset .ds-textarea-wrapper .ds-hidden-text{font-size:13px;line-height:1.5em}#ds-wrapper #ds-reset .ds-actions{position:relative;height:30px}#ds-wrapper #ds-reset .ds-actions button{display:block;position:absolute;top:0;right:0}#ds-wrapper #ds-reset .ds-dialog-close{position:absolute;bottom:13px;right:11px;display:block;width:13px;height:13px;overflow:hidden;background:transparent url("//static.duoshuo.com/images/sprites.png") 0 -163px no-repeat;_background-image:url("//static.duoshuo.com/images/sprites.gif")}#ds-wrapper #ds-reset .ds-dialog-close:hover{background-position:0 -176px}#ds-wrapper #ds-reset .ds-logo{display:inline-block;width:64px;height:21px;margin-right:10px;background:url("//static.duoshuo.com/images/logo.png") 0 0 no-repeat}#ds-wrapper #ds-reset .ds-dialog-footer{clear:both;border-top:1px dotted #ccc;padding:10px 15px 6px}#ds-wrapper #ds-reset .ds-dialog-footer span{color:#999;position:relative;top:-6px}#ds-wrapper #ds-reset .ds-connect{display:none}#ds-wrapper #ds-reset .ds-unread-list li{position:relative;margin:0;padding:8px 0;border-top:1px solid #eee;line-height:1.5em;color:#777}#ds-wrapper #ds-reset .ds-unread-list li a{color:#d32}#ds-wrapper #ds-reset .ds-unread-list li a:hover{color:#e45c4e}#ds-wrapper #ds-reset .ds-unread-list li a[rel~="author"]{color:#777}#ds-wrapper #ds-reset .ds-unread-list li .ds-delete{display:none;position:absolute;right:0;bottom:10px;text-indent:-9999px;width:14px;height:14px;overflow:hidden;background:transparent url("//static.duoshuo.com/images/delete.gif") no-repeat scroll 0 -14px}#ds-wrapper #ds-reset .ds-unread-list li:hover .ds-delete{display:block}#ds-wrapper #ds-reset.ds-touch .ds-unread-list li .ds-delete{display:block}#ds-wrapper.ds-no-transition #ds-reset button{background:url("//static.duoshuo.com/images/bg_sprites.png") repeat-x !important}#ds-wrapper.ds-no-transition #ds-reset button:hover{background-position:0 -30px !important}#ds-wrapper.ds-no-transition #ds-reset.ds-dialog{background-image:url("//static.duoshuo.com/images/black.png")}#ds-wrapper.ds-ie6 #ds-reset{margin-top:0}#ds-wrapper.ds-ie6 #ds-reset .ds-dialog-footer span{top:-3px}#ds-notify{position:fixed;*position:absolute;z-index:9999;max-width:144px;_width:130px;display:block;float:none;padding:8px 12px;background-color:#fff;-webkit-border-radius:5px;border-radius:5px;box-shadow:0 1px 1px rgba(0,0,0,0.25);border:1px solid #aaa}#ds-notify #ds-reset{line-height:14px}#ds-notify #ds-reset a.ds-logo{width:18px;height:14px;background:transparent url("//static.duoshuo.com/images/sprites.png") 0 -220px no-repeat;_background-image:url("//static.duoshuo.com/images/sprites.gif");position:absolute;display:block;top:8px;left:12px}#ds-notify #ds-reset span.ds-unread-count{font-weight:bold;color:#e32}#ds-notify #ds-reset ul.ds-notify-unread{line-height:150%;display:inline-block;margin:0 0 0 22px;padding:0}#ds-notify #ds-reset ul.ds-notify-unread li a{color:#d32;text-decoration:none}#ds-recent-comments li.ds-comment{list-style-type:none;position:relative !important;margin:0 !important;padding:6px 0 !important;_zoom:1;border-top:1px solid #dcdcdc;word-wrap:break-word;font-size:13px}#ds-recent-comments li.ds-comment a{display:inline}#ds-recent-comments li.ds-comment div{padding:0;margin:0}#ds-recent-comments li.ds-comment .ds-avatar{position:absolute !important;top:6px !important;left:0 !important}#ds-recent-comments li.ds-comment .ds-avatar a{display:block}#ds-recent-comments li.ds-comment .ds-meta{*margin-left:-15px;_margin-left:0}#ds-recent-comments li.ds-comment .ds-time{font-size:10px;color:#999;margin-left:5px}#ds-recent-comments li.ds-comment .ds-thread-title{margin:0 0 4px 0;line-height:13px;font-size:12px;color:#777}#ds-recent-comments li.ds-comment .ds-thread-title a{font-size:12px}#ds-recent-comments li.ds-comment .ds-excerpt{line-height:18px}#ds-recent-comments li.ds-comment.ds-show-avatars{padding-left:38px !important}#ds-recent-visitors .ds-avatar{display:inline;padding:0 !important;margin:4px !important}#ds-login .ds-icons-32 a{float:left;margin:0 5px 0 0}#ds-share #ds-reset.ds-share-aside-left ul,#ds-share #ds-reset.ds-share-aside-right ul,#ds-share #ds-reset.ds-share-inline ul{list-style:none;margin:0;padding:0;*zoom:1}#ds-share #ds-reset.ds-share-aside-left ul:after,#ds-share #ds-reset.ds-share-aside-right ul:after,#ds-share #ds-reset.ds-share-inline ul:after{content:".";display:block;height:0;clear:both;visibility:hidden}#ds-share #ds-reset.ds-share-aside-left ul li,#ds-share #ds-reset.ds-share-aside-right ul li,#ds-share #ds-reset.ds-share-inline ul li{list-style:none;float:left;font-size:14px;padding:7px 0}#ds-share #ds-reset.ds-share-inline{position:relative}#ds-share #ds-reset.ds-share-inline ul li{margin-left:8px}#ds-share #ds-reset.ds-share-inline ul li:first-child{margin-left:0}#ds-share #ds-reset.ds-share-aside-left,#ds-share #ds-reset.ds-share-aside-right{position:fixed;top:50%;z-index:1000;-webkit-transition:all .2s linear;-moz-transition:all .2s linear;transition:all .2s linear}#ds-share #ds-reset.ds-share-aside-left{left:0;-webkit-transform:translate(-100%, -50%);-ms-transform:translate(-100%, -50%);-o-transform:translate(-100%, -50%);transform:translate(-100%, -50%)}#ds-share #ds-reset.ds-share-aside-right{right:0;-webkit-transform:translate(100%, -50%);-ms-transform:translate(100%, -50%);-o-transform:translate(100%, -50%);transform:translate(100%, -50%)}#ds-share #ds-reset .ds-share-aside-toggle{width:28px;padding:23px 2px;background:#e94c4c;color:#fff;position:absolute;top:0;text-align:center;font-size:16px;font-weight:bolder;cursor:pointer}#ds-share #ds-reset.ds-share-aside-left .ds-share-aside-toggle{right:-32px;border-top-right-radius:3px;border-bottom-right-radius:3px}#ds-share #ds-reset.ds-share-aside-left .ds-share-icons{border-top-right-radius:0;border-top-left-radius:0;border-bottom-left-radius:0;border-left:none}#ds-share #ds-reset.ds-share-aside-right .ds-share-aside-toggle{left:-32px;border-top-left-radius:3px;border-bottom-left-radius:3px}#ds-share #ds-reset.ds-share-aside-right .ds-share-icons{border-top-left-radius:0;border-top-right-radius:0;border-bottom-right-radius:0;border-right:none}#ds-share #ds-reset.slide-to-left{-webkit-transform:translate(0, -50%);-ms-transform:translate(0, -50%);-o-transform:translate(0, -50%);transform:translate(0, -50%)}#ds-share #ds-reset.slide-to-right{-webkit-transform:translate(0, -50%);-ms-transform:translate(0, -50%);-o-transform:translate(0, -50%);transform:translate(0, -50%)}#ds-share #ds-reset .ds-share-icons-32 .ds-weibo,#ds-share #ds-reset .ds-share-icons-32 .ds-sohu,#ds-share #ds-reset .ds-share-icons-32 .ds-renren,#ds-share #ds-reset .ds-share-icons-32 .ds-netease,#ds-share #ds-reset .ds-share-icons-32 .ds-qqt,#ds-share #ds-reset .ds-share-icons-32 .ds-kaixin,#ds-share #ds-reset .ds-share-icons-32 .ds-douban,#ds-share #ds-reset .ds-share-icons-32 .ds-msn,#ds-share #ds-reset .ds-share-icons-32 .ds-qzone,#ds-share #ds-reset .ds-share-icons-32 .ds-duoshuo,#ds-share #ds-reset .ds-share-icons-32 .ds-360,#ds-share #ds-reset .ds-share-icons-32 .ds-alipay,#ds-share #ds-reset .ds-share-icons-32 .ds-qq,#ds-share #ds-reset .ds-share-icons-32 .ds-baidu,#ds-share #ds-reset .ds-share-icons-32 .ds-taobao,#ds-share #ds-reset .ds-share-icons-32 .ds-google,#ds-share #ds-reset .ds-share-icons-32 .ds-more,#ds-share #ds-reset .ds-share-icons-32 .ds-wechat,#ds-share #ds-reset .ds-share-icons-32 .ds-diandian,#ds-share #ds-reset .ds-share-icons-32 .ds-huaban,#ds-share #ds-reset .ds-share-icons-32 .ds-duitang,#ds-share #ds-reset .ds-share-icons-32 .ds-youdao,#ds-share #ds-reset .ds-share-icons-32 .ds-pengyou,#ds-share #ds-reset .ds-share-icons-32 .ds-facebook,#ds-share #ds-reset .ds-share-icons-32 .ds-twitter,#ds-share #ds-reset .ds-share-icons-32 .ds-linkedin,#ds-share #ds-reset .ds-share-icons-32 .ds-meilishuo,#ds-share #ds-reset .ds-share-icons-32 .ds-mogujie{height:32px;width:32px;text-decoration:none;color:#999;display:block;overflow:hidden;background-image:url("//static.duoshuo.com/images/service-icons-color-32.png");background-repeat:no-repeat;_background-image:url("//static.duoshuo.com/images/service-icons-color-32.gif")}#ds-share #ds-reset .ds-share-icons-32 .ds-weibo{background-position:0 0}#ds-share #ds-reset .ds-share-icons-32 .ds-sohu{background-position:0 -32px}#ds-share #ds-reset .ds-share-icons-32 .ds-renren{background-position:0 -64px}#ds-share #ds-reset .ds-share-icons-32 .ds-netease{background-position:0 -96px}#ds-share #ds-reset .ds-share-icons-32 .ds-qqt{background-position:0 -128px}#ds-share #ds-reset .ds-share-icons-32 .ds-kaixin{background-position:0 -160px}#ds-share #ds-reset .ds-share-icons-32 .ds-douban{background-position:0 -192px}#ds-share #ds-reset .ds-share-icons-32 .ds-msn{background-position:0 -224px}#ds-share #ds-reset .ds-share-icons-32 .ds-qzone{background-position:0 -256px}#ds-share #ds-reset .ds-share-icons-32 .ds-duoshuo{background-position:0 -288px}#ds-share #ds-reset .ds-share-icons-32 .ds-360{background-position:0 -320px}#ds-share #ds-reset .ds-share-icons-32 .ds-alipay{background-position:0 -352px}#ds-share #ds-reset .ds-share-icons-32 .ds-qq{background-position:0 -384px}#ds-share #ds-reset .ds-share-icons-32 .ds-baidu{background-position:0 -416px}#ds-share #ds-reset .ds-share-icons-32 .ds-taobao{background-position:0 -448px}#ds-share #ds-reset .ds-share-icons-32 .ds-google{background-position:0 -480px}#ds-share #ds-reset .ds-share-icons-32 .ds-more{background-position:0 -512px}#ds-share #ds-reset .ds-share-icons-32 .ds-wechat{background-position:0 -544px}#ds-share #ds-reset .ds-share-icons-32 .ds-diandian{background-position:0 -576px}#ds-share #ds-reset .ds-share-icons-32 .ds-huaban{background-position:0 -608px}#ds-share #ds-reset .ds-share-icons-32 .ds-duitang{background-position:0 -640px}#ds-share #ds-reset .ds-share-icons-32 .ds-youdao{background-position:0 -672px}#ds-share #ds-reset .ds-share-icons-32 .ds-pengyou{background-position:0 -704px}#ds-share #ds-reset .ds-share-icons-32 .ds-facebook{background-position:0 -736px}#ds-share #ds-reset .ds-share-icons-32 .ds-twitter{background-position:0 -768px}#ds-share #ds-reset .ds-share-icons-32 .ds-linkedin{background-position:0 -800px}#ds-share #ds-reset .ds-share-icons-32 .ds-meilishuo{background-position:0 -832px}#ds-share #ds-reset .ds-share-icons-32 .ds-mogujie{background-position:0 -864px}#ds-share #ds-reset .ds-share-icons-32 .flat{background-image:url("//static.duoshuo.com/images/service-icons-color-flat-32.png");_background-image:url("//static.duoshuo.com/images/service-icons-color-flat-32.gif")}#ds-share #ds-reset .ds-share-icons-16 .ds-weibo,#ds-share #ds-reset .ds-share-icons-16 .ds-sohu,#ds-share #ds-reset .ds-share-icons-16 .ds-renren,#ds-share #ds-reset .ds-share-icons-16 .ds-netease,#ds-share #ds-reset .ds-share-icons-16 .ds-qqt,#ds-share #ds-reset .ds-share-icons-16 .ds-kaixin,#ds-share #ds-reset .ds-share-icons-16 .ds-douban,#ds-share #ds-reset .ds-share-icons-16 .ds-msn,#ds-share #ds-reset .ds-share-icons-16 .ds-qzone,#ds-share #ds-reset .ds-share-icons-16 .ds-duoshuo,#ds-share #ds-reset .ds-share-icons-16 .ds-360,#ds-share #ds-reset .ds-share-icons-16 .ds-alipay,#ds-share #ds-reset .ds-share-icons-16 .ds-qq,#ds-share #ds-reset .ds-share-icons-16 .ds-baidu,#ds-share #ds-reset .ds-share-icons-16 .ds-taobao,#ds-share #ds-reset .ds-share-icons-16 .ds-google,#ds-share #ds-reset .ds-share-icons-16 .ds-more,#ds-share #ds-reset .ds-share-icons-16 .ds-wechat,#ds-share #ds-reset .ds-share-icons-16 .ds-diandian,#ds-share #ds-reset .ds-share-icons-16 .ds-huaban,#ds-share #ds-reset .ds-share-icons-16 .ds-duitang,#ds-share #ds-reset .ds-share-icons-16 .ds-youdao,#ds-share #ds-reset .ds-share-icons-16 .ds-pengyou,#ds-share #ds-reset .ds-share-icons-16 .ds-facebook,#ds-share #ds-reset .ds-share-icons-16 .ds-twitter,#ds-share #ds-reset .ds-share-icons-16 .ds-linkedin,#ds-share #ds-reset .ds-share-icons-16 .ds-meilishuo,#ds-share #ds-reset .ds-share-icons-16 .ds-mogujie{line-height:16px;padding-left:20px;text-decoration:none;color:#999;display:block;overflow:hidden;background-image:url("//static.duoshuo.com/images/service-icons-color.png?v=2");background-repeat:no-repeat;_background-image:url("//static.duoshuo.com/images/service-icons-color.gif?v=2")}#ds-share #ds-reset .ds-share-icons-16 .ds-weibo{background-position:0 0}#ds-share #ds-reset .ds-share-icons-16 .ds-sohu{background-position:0 -16px}#ds-share #ds-reset .ds-share-icons-16 .ds-renren{background-position:0 -32px}#ds-share #ds-reset .ds-share-icons-16 .ds-netease{background-position:0 -48px}#ds-share #ds-reset .ds-share-icons-16 .ds-qqt{background-position:0 -64px}#ds-share #ds-reset .ds-share-icons-16 .ds-kaixin{background-position:0 -80px}#ds-share #ds-reset .ds-share-icons-16 .ds-douban{background-position:0 -96px}#ds-share #ds-reset .ds-share-icons-16 .ds-msn{background-position:0 -112px}#ds-share #ds-reset .ds-share-icons-16 .ds-qzone{background-position:0 -128px}#ds-share #ds-reset .ds-share-icons-16 .ds-duoshuo{background-position:0 -144px}#ds-share #ds-reset .ds-share-icons-16 .ds-360{background-position:0 -160px}#ds-share #ds-reset .ds-share-icons-16 .ds-alipay{background-position:0 -176px}#ds-share #ds-reset .ds-share-icons-16 .ds-qq{background-position:0 -192px}#ds-share #ds-reset .ds-share-icons-16 .ds-baidu{background-position:0 -208px}#ds-share #ds-reset .ds-share-icons-16 .ds-taobao{background-position:0 -224px}#ds-share #ds-reset .ds-share-icons-16 .ds-google{background-position:0 -240px}#ds-share #ds-reset .ds-share-icons-16 .ds-more{background-position:0 -256px}#ds-share #ds-reset .ds-share-icons-16 .ds-wechat{background-position:0 -272px}#ds-share #ds-reset .ds-share-icons-16 .ds-diandian{background-position:0 -288px}#ds-share #ds-reset .ds-share-icons-16 .ds-huaban{background-position:0 -304px}#ds-share #ds-reset .ds-share-icons-16 .ds-duitang{background-position:0 -320px}#ds-share #ds-reset .ds-share-icons-16 .ds-youdao{background-position:0 -336px}#ds-share #ds-reset .ds-share-icons-16 .ds-pengyou{background-position:0 -352px}#ds-share #ds-reset .ds-share-icons-16 .ds-facebook{background-position:0 -368px}#ds-share #ds-reset .ds-share-icons-16 .ds-twitter{background-position:0 -384px}#ds-share #ds-reset .ds-share-icons-16 .ds-linkedin{background-position:0 -400px}#ds-share #ds-reset .ds-share-icons-16 .ds-meilishuo{background-position:0 -416px}#ds-share #ds-reset .ds-share-icons-16 .ds-mogujie{background-position:0 -432px}#ds-share #ds-reset .ds-share-icons-16 .flat{background-image:url("//static.duoshuo.com/images/service-icons-color-flat.png");_background-image:url("//static.duoshuo.com/images/service-icons-color-flat.gif")}#ds-share #ds-reset .ds-share-icons{border:1px solid #ccc;background:#fff;border-radius:3px}#ds-share #ds-reset .ds-share-icons .ds-share-icons-inner{width:208px;padding:10px}#ds-share #ds-reset .ds-share-icons .ds-share-icons-inner ul{margin:0;padding:0}#ds-share #ds-reset .ds-share-icons .ds-share-icons-inner ul li{padding-left:8px;margin-left:0;width:92px;font-size:12px}#ds-share #ds-reset .ds-share-icons .ds-share-icons-inner ul li:hover{border-radius:3px;background:#eee}#ds-share #ds-reset .ds-share-icons .ds-share-icons-inner ul li:nth-child(even){margin-left:8px}#ds-share #ds-reset .ds-share-icons .ds-share-icons-footer{text-align:right;line-height:30px;font-size:12px;color:#ccc;background-color:#eee;padding-right:10px}#ds-share #ds-reset .ds-share-icons-more{top:30px;left:0;position:absolute;z-index:1000}#ds-share.ds-no-transition #ds-reset.ds-share-aside-left{left:-229px;transform:none}#ds-share.ds-no-transition #ds-reset.ds-share-aside-right{right:-229px;transform:none}#ds-share .ds-share-aside-left,#ds-share .ds-share-aside-right{width:230px;_position:absolute;_bottom:auto;_top:expression(eval(document.documentElement.scrollTop+200))}#ds-share .ds-share-aside-left{_left:expression(eval(document.documentElement.scrollLeft-230))}#ds-share .ds-share-aside-right{_right:auto;_left:expression(eval(document.documentElement.scrollLeft+document.documentElement.clientWidth-this.offsetWidth)-(parseInt(this.currentStyle.marginLeft,10)||0)-(parseInt(this.currentStyle.marginRight,10)||0 - 230))}
```

具体样式请参照：[多说.css](https://github.com/luuman/Hexo/blob/master/themes/spfk/source/css/%E5%A4%9A%E8%AF%B4.css)

## 为Hexo博客添加目录


Hexo博客系统的核心支持生成目录（Table of Contents），但其默认的主题Landscape并不支持目录的显示。我们只需对Landscape的主题文件稍作修改并添加一点目录的CSS就可以在文章前面显示友好的目录了。

>修改Landscape主题的ejs文件

我们首先要编辑文章显示页面的模板，也就是themes/landscape/layout/_partial/article.ejs文件。为了将目录生成在正文之前，我们首先在这个文件中找到<%- post.content %>，并在这一行之前加入如下代码：

```
    引入文件_partial\article.ejs
    <% if (!index && post.toc != false && !is_page()){ %>
        <%- partial('_partial/toc') %>
    <% } %>


    <% if (!index && post.toc){ %>
    <%- partial('post/toc') %>
    <% } %>

<div id="toc" class="toc-article">
    <strong class="toc-title">文章目录</strong>
    <%- toc(post.content) %>
</div>
<style>
    .left-col .switch-btn {
        display: none;
    }
    .left-col .switch-area {
        display: none;
    }
</style>

<input type="button" id="tocButton" value="隐藏目录"  title="点击按钮隐藏或者显示文章目录">

<%- js('http://7.url.cn/edu/jslib/comb/require-2.1.6,jquery-1.9.1.min') %>
<script>
    var toc_button = document.getElementById("tocButton");
    var toc_div = document.getElementById("toc");
    toc_button.onclick=function() {
        if (toc_div.style.display == "none") {
            toc_div.style.display = "block";
            toc_button.value = "隐藏目录";
            document.getElementById("switch-btn").style.display = "none";
            document.getElementById("switch-area").style.display = "none";
        }
        else {
            toc_div.style.display = "none";
            toc_button.value = "显示目录";
            document.getElementById("switch-btn").style.display = "block";
            document.getElementById("switch-area").style.display = "block";
        }
    }

    if ($(".toc").length < 1) {
        $("#toc").css("display","none");
        $("#tocButton").css("display","none");
        $(".switch-btn").css("display","block");
        $(".switch-area").css("display","block");
    }
</script>

<% if (theme.toc_nowrap) { %>
    <style>
        .toc {
            white-space: nowrap;
            overflow-x: hidden;
        }
    </style>

    <script>
        $(document).ready(function() {
            $(".toc li a").mouseover(function() {
                var title = $(this).attr('href');
                $(this).attr("title", title);
            });
        })
    </script>
<% } %>
```

这段代码的含义清晰明了，if语句中有两个条件，!index是为了不在首页的文章摘要中生成目录，post.toc确保了只在显式地标记了toc: true的文章中生成目录。若这两个条件满足，则创建一个目录的<div>。

修改完这个文件之后，找一篇包含了多个子标题的文章，并在文章开头的front-matter中添加一句toc: true，在浏览器中访问这篇文章，应该可以看到文章的开头处已经有了带链接的目录。但是这样的目录实在太难看，我们还需要添加相应的CSS来将其指定为我们想要的样式。

>为目录编写CSS文件

要指定目录的样式，我们要修改的文件是themes/landscape/source/css/_partial/article.styl。在文件的最后，添加如下代码：

```
/*toc*/
#tocButton {
    position: fixed;
    border: none;
    left: 220px;
    top: 382px;
    background: none;
    font-size: .9em;
    font-weight: bold;
    color: lightgray;
    cursor: pointer
    font-family: inherit;
    outline: none; /*Remove button border when clicked.*/
    -webkit-appearance: none; /*Remove iOS button style*/
    &:hover {
        color: #88acdb;
    }
}
.toc-article {
  position: fixed;
  width: 230px;
  top: 378px;
  bottom: 1em;
  left: 0;
  margin-left: 0em;
  padding: 6px 10px 10px 50px;
  border-radius: 2.8%;
  overflow: auto;
}
#toc {
    float: right;
    font-size: .9em;
    line-height: 1.65em;
    z-index: 12;
    a {
        color: gray;
        &:visited {
            color: rgba(244, 131, 133, 1);
        } 
        &:hover {
            color: #88acdb;
            text-decoration: none;
        }
    }
    li:hover {
        background: none;
        li:hover {
            background: rgba(158, 188, 226, .21);
        }
    }
    .toc-title {
        font-weight: bold;
        color: gray;
    }
    .toc {
        padding: .7em;
        padding-right: 0;
        li {
            list-style-type: none;
        }
    }
    ol {
        margin-left: 0;
    }
    .toc-child {
        padding-left: 1.25em;
        margin: 4px 0; 
    }
    .toc-link:hover {
        background: rgba(158, 188, 226, .21);
    }
}

.copyright {
    width: 85%;
    max-width: 45em;
    margin: 0 auto;
    padding: .5em 1.8em;
    border: 1px solid lightgray;
    font-size: .93em;
    line-height: 1.6em;
    word-break: break-word;
    background: rgba(255, 255, 255, .4);
    span {
        margin-right: 1em;
        color: #B5B5B5;
        font-weight: bold;
    }
    a {
        color: gray;
        &:hover {
            font-weight: bold;
            color: #a3d2a3;
            text-decoration: underline;
        }
    }
    &:hover p .copy-path::after {
        content: "复制";
    }
    .post-url:hover {
        font-weight: normal;
    }
    .copy-path {
        margin-left: 1em;
        &:hover {
            color: gray;
            cursor: pointer;
        }
    }
}
```
第一段的toc-article指定了目录整个<div>的背景色、边框色、倒角半径、各种间距以及最大的宽度。注意这里最好指定目录的最大宽度，我将其设为了28%，也就是文章正文那个框的宽度的28%，也可以设为一个固定的长度，比如在笔记本电脑上16em就是个不错的宽度，但为了能适配各种不同尺寸的屏幕，最好还是设置为百分比。如果不指定最大宽度，遇到比较长的标题时，生成的目录会非常难看。这个最大宽度的设置是我在网上其他添加目录的方法中没有见到的。

第二段的toc-title指的就是“文章目录”那四个字，这四个字要比其他字大一些，将其字号设为其他字的120%。

第三段的#toc.toc指定了目录列表的一些细节，将font-size设为0.9em会让目录的字比文章的字稍小一些。最后的.toc-child指定了二级目录的缩进量。

再次生成页面，应该已经可以显示比较美观的目录了。

>收尾工作

通常情况下我们不需要为每一篇文章都添加目录，因为大部分文章的长度还是相对较短，或者结构简单而没有添加小标题。在我的博客上，需要添加目录的长文还是相对较少的。因为我选择了默认不生成目录，而手动为需要目录的文章添加显式地标记。

下面我就需要编辑每一篇需要添加目录的文章，在文章开头的front-matter中加入toc: true。



## 插入自定义页面

仿照Hexo官网，了解到单页面的添加方式。

>添加代码

```

D:\Hexo\Hexos\themes\spfk\layout\plugins.swig

<div id="content-wrap">
  <div class="wrapper">
    <div class="inner">
      <header id="plugin-list-header">
        <h1 id="plugin-list-title">{{ page.title }}</h1>
        <input type="search" id="plugin-search-input" placeholder="Search">
        <div id="plugin-list-count">{{ site.data[page.data].length }} items</div>
      </header>
      <ul id="plugin-list">
        {% for plugin in _.sortBy(site.data[page.data], 'name') %}
          {{ partial('_partial/' + page.partial, {plugin: plugin}) }}
        {% endfor %}
      </ul>
    </div>
  </div>
</div>
<script>window.SEARCH_INDEX = {{ lunr_index(site.data[page.data]) }}</script>

```

```
D:\Hexo\Hexos\themes\spfk\layout\_partial\plugin.swig

<li class="plugin on">
  <a href="{{ plugin.link }}" class="plugin-name" target="_blank">{{ plugin.name }}</a>
  <p class="plugin-desc">{{ plugin.description }}</p>
  <div class="plugin-tag-list">
    {% for tag in plugin.tags %}
      <a href="#{{ tag }}" class="plugin-tag">{{ tag }}</a>
    {% endfor %}
  </div>
</li>
```

```
D:\Hexo\Hexos\themes\spfk\layout\_partial\work.swig


<li class="plugin on">
  <div class="plugin-screenshot">
    <img src="{{ plugin.imgs }}" class="plugin-screenshot-img">
    {% if plugin.preview %}
    <a href="{{ plugin.preview }}" class="plugin-preview-link" target="_blank"><i class="fa fa-eye"></i></a>
    {% endif %}
  </div>
  <a href="{{ plugin.link }}" class="plugin-name" target="_blank">{{ plugin.name }}</a>
  <p class="plugin-desc">{{ plugin.description }}</p>
  <div class="plugin-tag-list">
    {% for tag in plugin.tags %}
      <a href="#{{ tag }}" class="plugin-tag">{{ tag }}</a>
    {% endfor %}
  </div>
</li>
```

```
引入样式文件
D:\Hexo\Hexos\themes\spfk\source\css\style.styl

@import "_partial/plugins"
```



>修改样式

```
D:\Hexo\Hexos\themes\spfk\layout\_partial\plugin.swig

#plugin-list-header
  clearfix()
  margin: 40px 0

#plugin-list-title
  font-family: font-title
  font-size: 36px
  font-weight: 300
  line-height: 1
  float: left

#plugin-list-count
  color: color-gray
  padding-top: 1em
  text-align: right
  @media mq-normal
    float: right
    line-height: 40px
    padding-top: 0
    padding-right: 15px

#plugin-search-input
  font-size: 16px
  font-family: inherit
  -webkit-appearance: none
  border: 1px solid color-border
  padding: 10px 10px
  width: 100%
  margin-top: 25px
  @media mq-normal
    float: right
    width: 50%
    margin-top: 0

#plugin-list
  margin: 40px -20px
  display: flex
  flex-flow: column
  @media mq-tablet
    flex-flow: row wrap

.plugin
  display: none
  padding: 20px
  @media mq-tablet
    flex: 0 0 50%
  @media mq-normal
    flex: 0 0 (100 / 3)%
  &.on
    display: block

.plugin-name
  font-family: font-title
  font-weight: bold
  color: color-link
  font-size: 20px
  text-decoration: none
  line-height: 1
  &:hover
    color: color-link-hover

.plugin-desc
  line-height: line-height
  margin: 1em 0

.plugin-tag-list
  clearfix()
  line-height: 1.3

.plugin-tag
  color: color-gray
  font-size: 0.9em
  text-decoration: none
  float: left
  margin-right: 10px
  &:hover
    color: color-link-hover
  &:before
    content: "#"

.plugin-screenshot
  margin-bottom: 15px
  position: relative
  padding-top: (10 / 16 * 100)% // 16:10 ratio
  height: 0
  overflow: hidden

.plugin-screenshot-img
  position: absolute
  top: 0
  left: 0
  width: 100%
  height: auto

.plugin-preview-link
  position: absolute
  top: 0
  left: 0
  width: 100%
  height: 100%
  background: rgba(0, 0, 0, 0.7)
  color: #fff
  text-align: center
  opacity: 0
  transition: 0.15s
  &:hover
    opacity: 1
    .fa
      opacity: 1
      transform: scale(1)
  .fa
    position: absolute
    top: 0
    left: 0
    bottom: 0
    right: 0
    margin: auto
    font-size: 50px
    width: @font-size
    height: @font-size
    opacity: 0
    transform: scale(6)
    transition: 0.2s
    transition-delay: 0.15s
```

## 自定义挂件

除了默认已提供的挂件外，你还可以自定义自己的小挂件，在hexo\themes\modernist\layout\_widget\下，新建自己的ejs文件，如myWidget.ejs，然后在配置文件hexo\themes\modernist\_config.yml中配置。
```
widgets:
  - myWidget
用上述方法可以添加新浪微博小挂件。

生成自己的微博组件。
添加hexo\themes\modernist\layout\_widget\weibo.ejs文件。
配置hexo\themes\modernist\_config.yml。
```


## Hexo语法高亮

查阅格式高亮代码，了解到本主题，通过特定的规律进行语法高亮！好像无法识别不同代码的语法高亮！

测试：

通过测试代码，查看后台代码逻辑，了解到可以识别Apache、C++、bash等，还有部分不可识别。那么这个主题用的是什么的语法高亮？


代码code，table-gutter-pre是代码前面的序号。class="highlight apache"
```
J:\Hexo\Hexo\themes\spfk\source\css\_partial\highlight.styl

// https://github.com/luuman/hexo-theme-spfk

code-bgc = #002B36
code-tag = #F92672
code-attr = #A6E22E
code-word = #FFFFFF
code-value = #E6DB74
code-number = #9E90FF
code-keyword = #66D9EF
code-comment = #75715E
code-argument = #FD971F

$code-block
  background: code-bgc
  margin: 10px 0
  padding: 10px 10px
  overflow: auto
  color: #4C4C4C
  line-height: font-size * line-height
```
参考：
highlight.js：
Demo https://highlightjs.org/static/demo/
Solarized Dark
Atelier Sulphurpool Dark
文档：http://highlightjs.readthedocs.org/en/latest/css-classes-reference.html
下载：https://highlightjs.org/download/


## 首页添加loading效果


themes\spfk\layout\index.ejs


```
<link rel="stylesheet" href="css/loading-style.css">
<div id="loader-wrapper">
    <div id="loader"></div>
</div>

<%- partial('_partial/archive', {pagination: 2, index: true}) %>
<!-- loading -->
<script>window.jQuery || document.write('<script src="js/jquery-1.9.1.min.js"><\/script>')</script>
<script type="text/javascript">
    $(window).load(function() {                              // makes sure the whole site is loaded
        $('#loader').fadeOut();                              // will first fade out the loading animation
            $('#loader-wrapper').delay(350).fadeOut('slow'); // will fade out the white DIV that covers the website.
        $('body').delay(350).css({'overflow-y':'visible'});
    })
</script>
```


## LoadingBar页面顶部加载进度条

J:\Hexo\Hexo\themes\spfk\layout\_partial\head.ejs

```
<!-- 加载特效 -->
<% if(theme.animate) { %>
 <script src="/js/pace.js"></script>
<link href="/css/pace/pace-theme-flash.css" rel="stylesheet" />
<% } %>
```


## highlight.js

```
J:\Hexo\Hexo\node_modules\hexo-renderer-marked\node_modules\hexo-util\lib\highlight.js:
   37    }
   38  
   39:   result += '<figure class="highlight' + (data.language ? ' ' + data.language : '') + '"' + 'data-lang="' + (data.language ? ' ' + data.language : '') + '">';
   40  
   41    if (caption) {

J:\Hexo\Hexo\node_modules\hexo-util\lib\highlight.js:
   33    }
   34  
   35:   result += '<figure class="highlight' + (data.language ? ' ' + data.language : '') + '">';
   36  
   37    if (caption){

6 matches across 2 files

  result += '<figure class="highlight' + (data.language ? ' ' + data.language : '') + '"' + 'data-lang="' + (data.language ? ' ' + data.language : '') + '">';
```


## Hexo插件

[Swiftype]("https://swiftype.com/")

[微搜索]("https://dashboard.tinysou.com/")

[不蒜子]("http://busuanzi.ibruce.info/")

[百度统计]("http://sitecenter.baidu.com/sc-web/")

[CNZZ数据]("http://www.cnzz.com/")

[Font Awesome]("http://fontawesome.io/")

[百度脑图]("http://naotu.baidu.com/edit.html")

[ICON图标]("http://www.easyicon.net/")

[Font Awesome]("http://fontawesome.dashgame.com/")

[FREE Icon Maker]("https://freeiconmaker.com/")

[Icon Font]("https://icomoon.io/")

[在线ico]("http://www.ico.la/")

[IcoMoon]("https://icomoon.io/")

[Disqus]("https://disqus.com/")

[多说评论]("http://luuman.duoshuo.com/admin/")

[QQ邮箱开放平台]("http://open.mail.qq.com/cgi-bin/dy_template?sid=8EZP69fLJrYnKjRi&t=index&sub=0&r=2a79875e99ef37e39c7438e990f66d32")

[七牛云存储]("https://portal.qiniu.com/")

[百度分享]("http://share.baidu.com/")

[AddThis]("http://www.addthis.com/")

[分享一个方法解决Facebook,Linkedin,Twitter,Google+等SNS平台太多更新不过来的问题。 - 米课问答]("http://ask.imiker.com/question/6970")

[GitCafe]("https://gitcafe.com/")

[Coding.Net]("https://coding.net/user")

[GitHub]("https://github.com/")
