---
title: "hadoop hdfs问题总结"
date: 2013-06-18
draft: false
tags: [hadoop,hdfs,问题总结]
categories: [Hadoop]
---

前言：本文所提及的错误均是在ubuntu 12.04下部署的hadoop1.0.3。

1、问题：master执行任何命令，datanode提示WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!  

    原因：ssh证书验证错误

    解决方法：直接删除所有的IP（如果你的datanode较多不建议这么做，因为所有节点需求重新连接）

                    mv /root/.ssh/known_hosts /tmp

2、问题：网页提示WARNING : There are about  *  missing blocks. Please check the log or run fsck.  

    原因：部分数据库损坏或丢失 

    解决方法：./hadoop fsck -delete 

3、问题：balancer.sh不能运行，namenode一直处于安全模式

    解决方法：hadoop dfsadmin -safemode leave命令强制离开         dfsadmin -safemode value 参数value的说明：                 enter - 进入安全模式                 leave - 强制NameNode离开安全模式                 get -  返回安全模式是否开启的信息                 wait - 等待，一直到安全模式结束。 
 
- - -
 