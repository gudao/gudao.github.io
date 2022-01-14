---
title: "(转)Hadoop集群安装详细步骤|Hadoop安装配置"
date: 2012-06-05
draft: false
tags: [Hadoop,安装,部署]
categories: [Hadoop]
---

![](/Content/upload/Img20120605/r3jcceg0.xhx.jpg) 


# Hadoop集群安装


首先我们统一一下定义，在这里所提到的Hadoop是指Hadoop Common，主要提供DFS（分布式文件存储）与Map/Reduce的核心功能。Hadoop在windows下还未经过很好的测试，所以笔者推荐大家在linux（cent os 5.X）下安装使用。准备安装Hadoop集群之前我们得先检验系统是否安装了如下的必备软件：ssh、rsync和Jdk1.6（因为Hadoop需要使用到Jdk中的编译工具，所以一般不直接使用Jre）。可以使用yum install rsync来安装rsync。一般来说ssh是默认安装到系统中的。Jdk1.6的安装方法这里就不多介绍了。确保以上准备工作完了之后我们就开始安装Hadoop软件，假设我们用三台机器做Hadoop集群，分别是：192.168.1.111、192.168.1.112和192.168.1.113（下文简称111，112和113），且都使用root用户。下面是在linux平台下安装Hadoop的过程：


<table><tbody><tr><td>[root@192.168.1.111] #这里将Hadoop安装到/opt/hadoop[root@192.168.1.111] mkdir /opt/hadoop[root@192.168.1.111] cd /opt/hadoop[root@192.168.1.111] #更改所属关系和权限（对于其它用户而言）[root@192.168.1.111] chown root ./; chmod 755 ./[root@192.168.1.111] # 下载项目包（去官网（[http://hadoop.apache.org/](http://hadoop.apache.org/)）下载Hadoop项目）[root@192.168.1.111] wget [http://www.apache.org/dist/hadoop/core/hadoop-0.20.2/hadoop-0.20.2.tar.gz](http://www.apache.org/dist/hadoop/core/hadoop-0.20.2/hadoop-0.20.2.tar.gz)[root@192.168.1.111] # 解压[root@192.168.1.111] tar xzvf hadoop-0.20.2.tar.gz[root@192.168.1.111] # 进入目录[root@192.168.1.111] cd hadoop-0.20.2[root@192.168.1.111] # 因为Hadoop子项目的需要，一般需要设置环境变量如下：[root@192.168.1.111] export HADOOP_HOME=`pwd` [root@192.168.1.111] # 也可以将环境变量设置到/etc/profile中 </td></tr></tbody></table>


在所有服务器的同一路径下都进行这几步，就完成了集群Hadoop软件的安装，是不是很简单？没错安装是很简单的，下面就是比较困难的工作了。


# 集群配置


根据Hadoop文档的描述“The Hadoop daemons are NameNode/DataNode and JobTracker/TaskTracker.”可以看出Hadoop核心守护程序就是由NameNode/DataNode 和JobTracker/TaskTracker这几个角色构成。Hadoop的DFS需要确立NameNode与DataNode角色，一般NameNode会部署到一台单独的服务器上而不与DataNode共同同一机器。另外Map/Reduce服务也需要确立JobTracker和TaskTracker的角色，一般JobTracker与NameNode共用一台机器作为master，而TaskTracker与DataNode同属于slave。至于NameNode/DataNode和JobTracker/TaskTracker的概念这里就不多讲了，需要了解的可以参看相关文档。在这里我们使用111作为NameNode与JobTracker，其它两台机器作为DataNode和TaskTracker，具体的配置如下：


## 环境的配置


在$HADOOP_HOME/conf/hadoop-env.sh中定义了Hadoop启动时需要的环境变量设置，其中我们至少需要配置JAVA_HOME（Jdk的路径）变量；另外我们一般还需要更改HADOOP_LOG_DIR（Hadoop的日志路径）这个变量，默认的设置是“export HADOOP_LOG_DIR=${HADOOP_HOME}/logs”，一般需要将其配置到一个磁盘空间比较大的目录下。


## Hadoop核心程序配置


Hadoop 包括一组默认配置文件（$HADOOP_HOME/src目录下的core/core-default.xml, hdfs/hdfs-default.xml 和mapred/mapred-default.xml），大家可以先好好看看并理解默认配置文件中的那些属性。虽然默认配置文件能让Hadoop核心程序顺利启动，但对于开发人员来说一般需要自己的来设置一些常规配置以满足开发和业务的需求，所以我们需要对默认配置文件的值进行覆盖，具体方法如下。$HADOOP_HOME/conf/core-site.xml是Hadoop的核心配置文件，对应并覆盖core-default.xml中的配置项。我们一般在这个文件中增加如下配置：



Core-site.xml代码  <a title="收藏这段代码">![收藏代码](http://yymmiinngg.iteye.com/images/icon_star.png)</a> 


- <configuration>   
-         <property>    
-                 <!-- 用于dfs命令模块中指定默认的文件系统协议 -->   
-                 <name>fs.default.name</name>    
-                 <value>hdfs://192.168.1.111:9000</value>    
-         </property>    
- </configuration>   

$HADOOP_HOME/conf/hdfs-site.xml是HDFS的配置文件，对应并覆盖hdfs-default.xml中的配置项。我们一般在这个文件中增加如下配置：



Hdfs-site.xml代码  <a title="收藏这段代码">![收藏代码](http://yymmiinngg.iteye.com/images/icon_star.png)</a> 


- <configuration>   
-         <property>   
-                 <!-- DFS中存储文件命名空间信息的目录 -->   
-                 <name>dfs.name.dir</name>   
-                 <value>/opt/hadoop/data/dfs.name.dir</value>   
-         </property>   
-         <property>   
-                 <!-- DFS中存储文件数据的目录 -->   
-                 <name>dfs.data.dir</name>    
-                 <value>/opt/hadoop/data/dfs.data.dir</value>   
-         </property>   
-         <property>   
-                 <!-- 是否对DFS中的文件进行权限控制(测试中一般用false)-->   
-                 <name>dfs.permissions</name>   
-                 <value>false</value>   
-        </property>   
- </configuration>   

$HADOOP_HOME/conf/mapred-site.xml是Map/Reduce的配置文件，对应并覆盖mapred-default.xml中的配置项。我们一般在这个文件中增加如下配置：



Mapred-site.xml代码  <a title="收藏这段代码">![收藏代码](http://yymmiinngg.iteye.com/images/icon_star.png)</a> 


- <configuration>   
-         <property>   
-                 <!-- 用来作JobTracker的节点的(一般与NameNode保持一致) -->   
-                 <name>mapred.job.tracker</name>   
-                 <value>192.168.1.111:9001</value>   
-         </property>   
-         <property>   
-                 <!-- map/reduce的系统目录（使用的HDFS的路径） -->   
-                 <name>mapred.system.dir</name>   
-                 <value>/system/mapred.system.dir</value>   
-         </property>   
-         <property>   
-                 <!-- map/reduce的临时目录（可使用“,”隔开，设置多重路径来分摊磁盘IO） -->   
-                 <name>mapred.local.dir</name>   
-                 <value>/opt/hadoop/data/mapred.local.dir</value>   
-         </property>   
- </configuration>   

 


## 主从配置


在$HADOOP_HOME/conf目录中存在masters和slaves这两个文件，用来做Hadoop的主从配置。上面已经提到了Hadoop主要由NameNode/DataNode 和JobTracker/TaskTracker构成，在主从配置里我们一般将NameNode和JobTracker列为主机，其它的共为从机，于是对于此处的配置应该是：        



Masters代码  <a title="收藏这段代码">![收藏代码](http://yymmiinngg.iteye.com/images/icon_star.png)</a> 


- 192.168.1.111   

 



Slaves代码  <a title="收藏这段代码">![收藏代码](http://yymmiinngg.iteye.com/images/icon_star.png)</a> 


- 192.168.1.112   
- 192.168.1.113   

如果你对以上介绍的配置项做了正确的配置，那么你的Hadoop集群只差启动和初体念了，当然，在$HADOOP_HOME/conf目录下还包括其它的一些配置文件，但那些都不是必须设置的，如果有兴趣你可以自己去了解了解。值得注意的是Hadoop集群的所有机器的配置应该保持一致，一般我们在配置完master后，使用scp将配置文件同步到集群的其它服务器上。 


# 启动


经过以上两个步骤，Hadoop的安装和配置已经OK了，那么下面我们就来启动Hadoop集群。启动前我们需要做一些准备，因为集群的启动是从NameNode开始的，于是DataNode的运行需要NameNode的远程调用，Hadoop使用ssh命令远程运行DataNode节点，这就是为什么Hadoop需要ssh的支持。我们可以想象一下，如果集群里有100台机器，那么我们就需要输入100遍主机的访问密码，但如果配置SSH使用无密码公钥认证的方式，就解决了此问题。简单的说，在111上需要生成一个密钥对，即一个私钥和一个公钥。将公钥拷贝到112和113上，如此一来，当111向112发起ssh连接的时候，112上就会生成一个随机数并用111的公钥对这个随机数进行加密，并且发送给111，111收到这个加密的数以后用私钥进行解密，并将解密后的数返回112，112确认解密的数无误后就允许111进行连接了。这就完成了一次公钥认证过程。公钥生成的步骤如下：


<table><tbody><tr><td>[root@192.168.1.111] ssh-keygen -t rsa[root@192.168.1.111] #以下是生成一个为id_rsa/id_rsa.pub的无密码的公/私钥对Generating public/private rsa key pair.Enter file in which to save the key (/root/.ssh/id_rsa):Enter passphrase (empty for no passphrase):Enter same passphrase again:[root@192.168.1.111] ls -l ~/.ssh[root@192.168.1.111] #这样我们会看到如下所示的结果集：-rw------- 1 root     root      1675 Jul  5 14:01 id_rsa-rw-r--r-- 1 root     root       394 Jul  5 14:01 id_rsa.pub </td></tr></tbody></table>


然后将id_rsa.pub的内容复制到每个机器（也包括本机）的~/.ssh/authorized_keys文件中。如果authorized_keys不存在，则使用touch ~/.ssh/authorized_keys生成一个；如果该文件已经存在，则追加内容进去就OK了，这里我们推荐使用如下命令：


<table><tbody><tr><td>[root@192.168.1.111] cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys[root@192.168.1.111] #因为ssh服务对文件的权限有着非常严格的限制（authorized_keys只能拥有指定用户的写权限）于是需要进行如下处理[root@192.168.1.111] chmod 644 ~/.ssh/authorized_keys </td></tr></tbody></table>


经过以上步骤，我们的无密码访问就配置好了，可以通过如下命令进行验证：


<table><tbody><tr><td>[root@192.168.1.111] ssh 192.168.1.112[root@192.168.1.112] exit[root@192.168.1.111] ssh 192.168.1.113[root@192.168.1.113] exit </td></tr></tbody></table>


如此一来，就可以正常启动Hadoop集群了。启动方式如下：


<table><tbody><tr><td>[root@192.168.1.111] cd $HADOOP_HOME/bin[root@192.168.1.111] #查看一下目录下的文件[root@192.168.1.111] ls -l-rwxr-xr-x 1 root root 9998 Feb 19 15:55 hadoop-rwxr-xr-x 1 root root 1966 Feb 19 15:55 hadoop-config.sh-rwxr-xr-x 1 root root 3690 Feb 19 15:55 hadoop-daemon.sh-rwxr-xr-x 1 root root 1227 Feb 19 15:55 hadoop-daemons.sh-rwxr-xr-x 1 root root 2710 Feb 19 15:55 rcc-rwxr-xr-x 1 root root 2043 Feb 19 15:55 slaves.sh-rwxr-xr-x 1 root root 1066 Feb 19 15:55 start-all.sh-rwxr-xr-x 1 root root  965 Feb 19 15:55 start-balancer.sh-rwxr-xr-x 1 root root 1645 Feb 19 15:55 start-dfs.sh-rwxr-xr-x 1 root root 1159 Feb 19 15:55 start-mapred.sh-rwxr-xr-x 1 root root 1019 Feb 19 15:55 stop-all.sh-rwxr-xr-x 1 root root 1016 Feb 19 15:55 stop-balancer.sh-rwxr-xr-x 1 root root 1146 Feb 19 15:55 stop-dfs.sh-rwxr-xr-x 1 root root 1068 Feb 19 15:55 stop-mapred.sh[root@192.168.1.111] #启动服务之前，我们需要做一件事情，非常重要，那就是格式化命名空间[root@192.168.1.111] ./hadoop namenode -format[root@192.168.1.111] #启动dfs和map/reduce服务[root@192.168.1.111] ./start-all.sh </td></tr></tbody></table>


我们用如下命令验证一下是否启动成功：


<table><tbody><tr><td>[root@192.168.1.111] $HADOOP_HOME/bin/hadoop dfs -ls </td></tr></tbody></table>


如果没有错误提示或者出现文件列表，那么恭喜你，Hadoop成功启动了，另外，我们可以通过访问http://192.168.1.111:50070来查看hdfs的状态，访问http://192.168.1.111:50030来查看map/reduce的状态。如果出现错误，或Hadoop集群未启动，可以查看$HADOOP_HOME/logs/下的日志文件。

 
- - -
 