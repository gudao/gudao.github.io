---
title: "Redis 集群教程(三)"
date: 2014-02-08
draft: false
tags: [Redis cluster tutorial]
categories: [Redis]
---

![](/Content/upload/Img20131226/s_02wcido5_0xx.png) 
创建并使用集群 

前面说了那么多的理论，其实不是我们关心的，下面正式开始说明怎么配置并使用Redis集群。要想创建一个集群首先要有多个运行在集群模式下的Redis实例才行，乍看好像是废话其实不是，集群模式的Redis实例是非传统模式的Redis，它需要配置为拥有特殊集群功能和可执行集群命令的实例。 

下面为一个最简单的集群配置： 

port 7000 #端口 

cluster-enabled yes #开启集群模式 

cluster-config-file nodes.conf #集群配置文件 

cluster-node-timeout 5000 #超时时间 

appendonly yes 

（code 1） 

其中需要注意的是cluster-config-file,改配置项指定一个名为nodes.conf的文件，不要试图手动修改该文件，它由集群在启动时自动生成并实时更新该文件。 

Redis集群正常工作至少需要3个主节点，所以建议你在初次安装时配置3个主节点和三个从节点。下面正式开始配置步骤： 

1、按照端口号建立6个与端口号同名的文件夹，命令如下： 

 mkdir cluster-test 

 cd cluster-test 

 mkdir 7000 7001 7002 7003 7004 7005 

 （code 2） 

复制code 1中的内容，创建一个名为redis.conf文件，复制到6个文件夹中并修改port为相应的端口，。 

2、下载源码(https://github.com/antirez/redis/archive/unstable.zip)，编译 

make PREFIX=/home/cluster-test/redis install，在redis目录下就会生成名为bin的目录，其中包含redis-benchmark、redis-check-aof、redis-check-dump、redis-cli、 redis-server5个可执行文件，复制这个5个文件分别至7000-7005文件夹中。 

3、在终端运行下面命令： 

cd 7000 

./redis-server ./redis.conf 

正如你看到的，在没有node.config文件的时候，系统会自动分配一个新的编号。 

[22451] 31 Dec 14:09:07.507 * Max number of open files set to 10032 

[22451] 31 Dec 14:09:07.508 * No cluster configuration found, I&#39;m e39dca9a6053b35013e34ff164839973e6cb93c3 

这个编号将用来标识这个redis实例，其他的节点也通过这个编号来识别这个Redis，而不是通过IP或者端口，IP和端口可能会变但是这个编号却永远不会变，所以我们称其为节点编号。 
 
- - -
 