---
title: "[转]hadoop管理NameNode AND SecondaryNameNode"
date: 2013-09-05
draft: false
tags: [hadoop,SecondaryNameNode,namenode]
categories: [Hadoop]
---

【转自】：http://a280606790.iteye.com/blog/870123

光从字面上来理解，很容易让一些初学者先入为主的认为：SecondaryNameNode（snn）就是NameNode（nn）的热备进程。其实不是。snn是HDFS架构中的一个组成部分，但是经常由于名字而被人误解它真正的用途，其实它真正的用途，是用来保存namenode中对HDFS metadata的信息的备份，并减少namenode重启的时间。对于hadoop进程中 ，要配置好并正确的使用 snn，还是需要做一些工作的。hadoop的默认配置中让 snn进程默认运行在了 namenode 的那台机器上，但是这样的话，如果这台机器出错，宕机，对恢复HDFS文件系统是很大的灾难，更好的方式是：将snn的进程配置在另外一台机器 上运行。 

在hadoop中，namenode负责对HDFS的metadata的持久化存储，并且处理来自客户端的对HDFS的各种操作的交互反馈。为了保证交互速度，HDFS文件系统的metadata是被load到namenode机器的内存中的，并且会将内存中的这些数据保存到磁盘进行持久化存储。为了保证这个持久化过程不会成为HDFS操作的瓶颈，hadoop采取的方式是：没有对任何一次的当前文件系统的snapshot进行持久化，对HDFS最近一段时间的操作list会被保存到namenode中的一个叫Editlog的文件中去。当重启namenode时，除了 load fsImage意外，还会对这个EditLog文件中 记录的HDFS操作进行replay，以恢复HDFS重启之前的最终状态。 

而SecondaryNameNode，会周期性的将EditLog中记录的对HDFS的操作合并到一个checkpoint中，然后清空EditLog。所以namenode的重启就会Load最新的一个checkpoint，并replay EditLog中 记录的hdfs操作，由于EditLog中记录的是从 上一次checkpoint以后到现在的操作列表，所以就会比较小。如果没有snn的这个周期性的合并过程，那么当每次重启namenode的时候，就会花费很长的时间。而这样周期性的合并就能减少重启的时间。同时也能保证HDFS系统的完整性。 

这就是SecondaryNameNode所做的事情。所以snn并不能分担namenode上对HDFS交互性操作的压力。尽管如此，当namenode机器宕机或者namenode进程出问题时，namenode的daemon进程可以通过人工的方式从snn上拷贝一份metadata来恢复HDFS文件系统。 


### 至于为什么要将SNN进程运行在一台非NameNode的 机器上，这主要出于两点考虑： 


- 可扩展性： 创建一个新的HDFS的snapshot需要将namenode中load到内存的metadata信息全部拷贝一遍，这样的操作需要的内存就需要 和namenode占用的内存一样，由于分配给namenode进程的内存其实是对HDFS文件系统的限制，如果分布式文件系统非常的大，那么namenode那台机器的内存就可能会被namenode进程全部占据。 
- 容错性： 当snn创建一个checkpoint的时候，它会将checkpoint拷贝成metadata的几个拷贝。将这个操作运行到另外一台机器，还可以提供分布式文件系统的容错性。  

### 配置将SecondaryNameNode运行在另外一台机器上 


HDFS的一次运行实例是通过在namenode机器上的<tt>$HADOOP_HOME/bin/start-dfs.sh（</tt> 或者<tt>start-all.sh</tt> <tt>） 脚本来启动的。这个脚本会在运行该脚本的机器上启动 namenode进程，而slaves机器上都会启动DataNode进程，slave机器的列表保存在</tt> <tt>conf/slaves文件中，一行一台机器。并且会在另外一台机器上启动一个snn进程，这台机器由</tt> <tt>conf/masters文件指定。所以，这里需要严格注意， </tt> <tt>conf/masters 文件中指定的机器，并不是说jobtracker或者namenode进程要 运行在这台机器上，因为这些进程是运行在 launch bin/start-dfs.sh或者 bin/start-mapred.sh(start-all.sh)的机器上的。所以，masters这个文件名是非常的令人混淆的，应该叫做secondaries会比较合适。然后，通过以下步骤：</tt>  

- 将所有想要运行secondarynamenode进程的机器写到masters文件中，一行一台。 
- 修改在masters文件中配置了的机器上的conf/hadoop-site.xml文件，加上如下选项：  


Java代码  <a title="收藏这段代码">![收藏代码](http://a280606790.iteye.com/images/icon_star.png)</a>


- <span style="font-size: small;"><property>  
- <name>dfs.http.address</name>  
- <value>namenode.hadoop-host.com:50070</value>  
- </property>  
-   </span style="font-size: small;">

  

      core-site.xml：这里有2个参数可配置，但一般来说我们不做修改。fs.checkpoint.period表示多长时间记录一次hdfs的镜像。默认是1小时。fs.checkpoint.size表示一次记录多大的size，默认64M。  

      



Java代码  <a title="收藏这段代码">![收藏代码](http://a280606790.iteye.com/images/icon_star.png)</a>


- <span style="font-size: small;"><property>  
-   <name>fs.checkpoint.period</name>  
-   <value>3600</value>  
-   <description>The number of seconds between two periodic checkpoints.  
-   </description>  
- </property>  
-   
- <property>  
-   <name>fs.checkpoint.size</name>  
-   <value>67108864</value>  
-   <description>The size of the current edit log (in bytes) that triggers  
-        a periodic checkpoint even if the fs.checkpoint.period hasn&#39;t expired.  
-   </description>  
- </property>  
-   </span style="font-size: small;">

  

3、配置检查。配置完成之后，我们需要检查一下是否成功。我们可以通过查看运行secondarynamenode的机器上文件目录来确定是否成功配置。首先输入jps查看是否存在secondarynamenode进程。如果存在，在查看对应的目录下是否有备份记录。如下图：  

![](http://hi.csdn.net/attachment/201011/4/0_1288857738S888.gif) 

该目录一般存在于hadoop.tmp.dir/dfs/namesecondary/下面。 

四、恢复 

1、配置完成了，如何恢复。首先我们kill掉namenode进程，然后将hadoop.tmp.dir目录下的数据删除掉。制造master挂掉情况。 

2、在配置参数dfs.name.dir指定的位置建立一个空文件夹； 把检查点目录的位置赋值给配置参数fs.checkpoint.dir； 启动NameNode，并加上-importCheckpoint。（这句话抄袭的是hadoop-0.20.2/hadoop-0.20.2/docs/cn/hdfs_user_guide.html#Secondary+NameNode，看看文档，有说明） 

3、启动namenode的时候采用hadoop namenode –importCheckpoint 

五、总结 

1、secondarynamenode可以配置多个，master文件里面多写几个就可以。 

2、千万记得如果要恢复数据是需要手动拷贝到namenode机器上的。不是自动的（参看上面写的恢复操作）。 

3、镜像备份的周期时间是可以修改的，如果不想一个小时备份一次，可以改的时间短点。core-site.xml中的fs.checkpoint.period值 
 
- - -
 