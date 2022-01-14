---
title: "简单几步让Nginx平滑升级（服务器运维必看）"
date: 2015-04-05
draft: false
tags: [nginx]
categories: [Nginx]
---

前一篇文章中的Markdwon其实是轻量级博客系统Ghost的默认编辑器，而今天介绍的Nginx也是Ghost部署需要的。我当时为了简单，安装的Nginx是预编译版的，今天要在Nginx上发布视频，结果把.conf里的MP4模块打开后重启Nginx没有起来，于是用`nginx -V`查看发现预编译版本竟然没有MP4模块。只好自己手动升级一下Nginx了，作为一名合格的运维人员，升级服务当然要保证服务的正常运行啊，那就是今天所说的平滑升级了，下面写一下Nginx的平滑升级步骤：

- 
安装编译库，`apt-get install gcc libpcre3 libpcre3-dev libssl-dev libgd2-xpm-dev`

- 
下载nginx源码,`wget http://nginx.org/download/nginx-1.6.2.tar.gz`，解压`tar -xzvf nginx-1.6.2.tar.gz`

- 
为了保证现有模块保留，先查看现有编译参数,`nginx -V`，然后复制现有参数加上新的MP4模块，我的编译参数如下: `

./configure --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-ipv6 --with-http_ssl_module --with-http_stub_status_module  --with-http_gzip_static_module --with-http_image_filter_module --with-http_spdy_module --with-http_sub_module --with-http_xslt_module  --with-http_mp4_module`    

- 
如果你的显示结果类似这样说明你成功了，

`nginx path prefix: &#34;/usr/share/nginx&#34;

nginx binary file: &#34;/usr/share/nginx/sbin/nginx&#34;

nginx configuration prefix: &#34;/etc/nginx&#34;

nginx configuration file: &#34;/etc/nginx/nginx.conf&#34;

nginx pid file: &#34;/run/nginx.pid&#34;

nginx error log file: &#34;/var/log/nginx/error.log&#34;

nginx http access log file: &#34;/var/log/nginx/access.log&#34;

nginx http client request body temporary files: &#34;/var/lib/nginx/body&#34;

nginx http proxy temporary files: &#34;/var/lib/nginx/proxy&#34;

nginx http fastcgi temporary files: &#34;/var/lib/nginx/fastcgi&#34;

nginx http uwsgi temporary files: &#34;/var/lib/nginx/uwsgi&#34;

nginx http scgi temporary files: &#34;/var/lib/nginx/scgi&#34;`

- 
编译`make`

- 
编译完成后，在当前目录的 objs 目录下就是我们需要的 nginx 可执行文件了，只需覆盖即可完成升级。在覆盖老版本的 nginx 之前，首先将其重命名以免无法覆盖`mv /usr/sbin/nginx /usr/sbin/nginx.old`

- 
将新编译好的 nginx 复制到 nginx 的目录中`cp objs/nginx /usr/sbin/nginx`

- 
最后重启 nginx 完成升级,`nginx -s reload`

- 
查看是否升级成功,`nginx -V`


 
- - -
 