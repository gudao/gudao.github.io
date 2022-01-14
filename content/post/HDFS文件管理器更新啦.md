---
title: "HDFS文件管理器更新啦"
date: 2013-08-26
draft: false
tags: [hadoop,hdfs,c#,文件管理器,thrift]
categories: [Hadoop]
---

![](/Content/upload/Img20130826/s_50vg3kob_jwg.png) 
这几天没事把HDFS文件管理器的功能重新整理了一下，更新的内容主要有：部署更简单，界面更方便！哈哈，有点自夸，先来张截图炫一下！![](/Content/attached/image/20130826/ew40d0zl_1bw.png) 

较第一版《[.net Hadoop资源管理器发布](http://www.front2end.cn/blog/.net-Hadoop-Explorer-release.html)》的是不是好了不少。

下面正式开始使用步骤：

    一、下载文件：

        1、hadoop的扩展包[hadoop.rar](/Content/attached/file/20130826/ozqqrpd3_x5q.rar)，其中包括hadoop-thrift.jar、libthrift-0.9.0.jar、thrift.sh。

        2、HDFS资源管理器的客户端[ReleaseV2.rar](/Content/attached/file/20130826/1qduyn2z_ois.rar)，其中NetHadoop.exe为主程序，NetHadoop.exe.config为配置文件。

    二、配置hadoop：

        1、将hadoop-thrift.jar、libthrift-0.9.0.jar放至hadoop程序的根目录（以下称hadoop目录）下的lib文件夹中，thrift.sh文件建议放在hadoop/bin下，并修改其文件属性为可执行。

        2、进入hadoop/bin文件夹运行thrift.sh，需要加端口号（服务将在此端口下运行）。如:

                 cd /home/ud/hadoop/bin

                  ./thrift.sh 8888

    三、配置资源管理器：

        1、解压ReleaseV2.rar，找到NetHadoop.exe.config，对应修改以下节点：

        ![](/Content/attached/image/20130826/fzjp4igp_orb.png) 

        其中 &lt;add key=&#34;HdfsThriftIP&#34; value=&#34;192.168.3.30&#34;/&gt;为启动服务的IP地址，&lt;add key=&#34;HdfsThriftPort&#34; value=&#34;8888&#34;/&gt;为上面提到的端口。        &lt;add key=&#34;HdfsRoot&#34; value=&#34;hdfs://192.168.3.30:9000/&#34;/&gt;为hdfs的根路径，默认可以为hdfs的&#34;/&#34;，如果想隐藏一些其他路径，可以指定特殊路径。 &lt;add key=&#34;HDFSREPLICATION&#34; value=&#34;2&#34;/&gt;为上传文件是强行实施的文件保存份数。

        2、启动NetHadoop.exe主程序。

    四、恭喜你！



    注意：该管理器默认使用了可恢复的删除策略，所以需要配置为可恢复才可以删除，如果你还不会请移步这里[Hadoop删除数据恢复配置。](http://www.front2end.cn/blog/Hadoop-deleted-data-recovery-configuration.html) 

    源代码随后会放在github上，感兴趣的欢迎到这里[https://github.com/gudao/NetHadoop](https://github.com/gudao/NetHadoop)下载。
 
- - -
 