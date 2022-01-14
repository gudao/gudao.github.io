---
title: "nginx Too many open files错误解决办法"
date: 2013-06-03
draft: false
tags: [nginx,Too many open files]
categories: [Nginx]
---

应用环境：centos 5.9、 nginx 1.4.1

测试应用并发时，nginx报Too many open files错误。

解决方法：

1、修改/etc/security/limits.conf

    添加




<pre>* soft nofile 10240 
* hard nofile 10240</pre>

    注销登录后即可生效。（注意数字最好别太大，我尝试过655350，重启后机器反应特别慢）


2、修改nginx配置文件

    添加worker_rlimit_nofile 10240;


 
- - -
 