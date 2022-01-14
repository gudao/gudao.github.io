---
title: "Hadoop删除数据恢复配置"
date: 2012-11-29
draft: false
tags: [Hadoop数据恢复]
categories: [Hadoop]
---

数据删除恢复绝对是有用的东东，但是Hadoop的默认配置为不使用回收站，配置方法为在**conf/core-site.xml**中添加如下配置：




<pre>&lt;property&gt;  
  &lt;name&gt;fs.trash.interval&lt;/name&gt;  
  &lt;value&gt;1440&lt;/value&gt;  
  &lt;description&gt;Number of minutes between trash checkpoints.  
  If zero, the trash feature is disabled.  
  &lt;/description&gt;  
&lt;/property&gt; </pre>

其中value的单位是分钟，在这个时间段内文件会保存，过期响应的数据则真正删除。



【2013-1-23补充】

以上说的可以删除恢复，只是针对命令行的删除即命令hadoop fs -rm 或-rmr可以，针对API里的FileSystem.delete是不起作用的，所以说如果你是调用的API删除，请注意这里的配置不起作用。如果想通过API删除也可以恢复，请move到.Tarsh文件夹，暂时是这个方法。


 
- - -
 