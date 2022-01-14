---
title: "windows下编译nginx"
date: 2012-09-13
draft: false
tags: [nginx,flv模块,windows编译nginx]
categories: [Nginx]
---

![](/Content/upload/Img20120831/aw5i1dna.01h.gif) 
[前面一篇文章](http://www.front2end.cn/Post/Detail/43)说了怎么修改nginx的flv模块，但是没有说修改完怎么用。如果你是Linux用户自然重新编译nginx就OK了，但偏偏我又是一个Windows的痴恋狂，没办法我还想要Windows版的nginx，好吧下面我就把Windows下编译Nginx的过程跟大家分享一下。

一、安装Windows下的编译环境cygwin

    说起这个玩意真的，我真的不看好它的安装过程，如果你感兴趣请Google它的Fucking manual（我还是提供一个[地址](http://hi.baidu.com/www100/item/b79723f239cf9449932af29f)吧，有些朋友就是喜欢一条龙服务），如果你想偷懒那就下载Cygwin-Easy-2007.03.21.iso，这个东东，我用的比较老是2007的，但是可以用就行了。

二、下载nginx源码

    如果你看过上一篇文章，那你一定有这个了。把它放在你喜欢的目录下。

三、使用cygwin，进入源码文件夹

  默认的cygwin不能粘贴命令（表示鄙视），如何修改请看[这里](http://www.gnbase.com/thread-1185-1.html)。

 进入源码文件夹：cd c:/nginx

 四、配置并编译

    


<pre>    ./configure \
    --prefix=. \
    --sbin-path=nginx \
    --conf-path=conf/nginx.conf \
    --pid-path=logs/nginx.pid \
    --error-log-path=logs/error.log \
    --http-log-path=logs/access.log \
    --http-client-body-temp-path=client/ \
    --http-proxy-temp-path=proxy/ \
    --http-fastcgi-temp-path=fastcgi/ \
    --with-cc-opt=&#34;-D FD_SETSIZE=4096&#34; \
    --with-debug \
    --with-mail_ssl_module \
    --with-http_realip_module \
    --with-http_stub_status_module \
    --with-http_ssl_module \
    --with-http_flv_module \

make &amp;&amp; make install</pre>

   如果你不懂上面的配置意思，同样输入./configure -h 读那Fucking manual，不过我要指出的是prefix的选项，如果我们指定–prefix=/cygdrive/d/nginx，那么最后脱离Cygwin环境运行，会发现root无法指向其它磁盘上的目录，所以这里使用了相对路径，而且指定了可执行文件nginx在根目录下，而不是默认的sbin/nginx。


五、编译成功

    如果你编译成功了，先恭喜一下，不过在Cygwin下运行正常，但如果要脱离Cygwin，需要从cygwin/bin下拷贝cygwin1.dll,cyggcc_s-1.dll,cygz.dll,cygcrypto-0.9.8.dll,cygssl-0.9.8.dll,cygpcre-0.dll,cygcrypt-0.dll这几个dll到nginx目录下。如果不知道这些都是干什么的，你就先一个都不复制，启动一下，报错少那个dll你就复制那个文件。



过程说明完毕，如果有问题欢迎回复或邮件。



 
 
- - -
 