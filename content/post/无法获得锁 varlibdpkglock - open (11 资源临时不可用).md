---
title: "无法获得锁 /var/lib/dpkg/lock - open (11: 资源临时不可用)"
date: 2013-07-29
draft: false
tags: [apt-get install,lock]
categories: [服务器]
---

![](/Content/upload/Img20130729/s_kdhunic3_ix2.png) 
在ubuntu中，当在使用apt-get 安装软件过程中出现死机或强制退出时，下次再使用apt-get install命令时，会出现以下错误：

“E: 无法获得锁 /var/lib/dpkg/lock - open (11: 资源暂时不可用)E: 无法锁定管理目录(/var/lib/dpkg/)，是否有其他进程正占用它？”

解决办法如下：

1、如果是强制退出的，就在终端中找到刚才的进程将其结束。

    终端输入top或者 ps  -aux ，列出进程。找到含有apt‘-get的进程，直接sudo kill PID。2、如果找不到正在使用的进程，就强制解锁。命令如下：    sudo rm /var/cache/apt/archives/lock    sudo rm /var/lib/dpkg/lock



顺便扯个闲事，ubuntu出手机了，感兴趣可以关注一下官网哦。![](/Content/attached/image/20130729/mpk0iqqs_dsy.png) 
 
- - -
 