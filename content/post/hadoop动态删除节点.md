---
title: "hadoop动态删除节点"
date: 2013-11-20
draft: false
tags: [hadoop,delete datanode]
categories: [Hadoop]
---

在Hadoop使用过程中难免有个别服务器不给力，老是出现掉盘或宕机等这种情况，这时你就要考虑把这种服务器彻底的拿掉。我们这里说的就是动态删除节点，步骤是这样的：

    1.hdfs-site.xml下添加    &lt;property&gt;          &lt;name&gt;dfs.hosts.exclude&lt;/name&gt;          &lt;value&gt;/usr/hadoop/src/conf/excludes&lt;/value&gt;    &lt;/property&gt;    2.conf目录下创建excludes文件    添加ip地址    3.执行./bin/hadoop dfsadmin  -refreshNodes    4.完毕后去除ip
 
- - -
 