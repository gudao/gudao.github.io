---
title: ".net Hadoop资源管理器发布"
date: 2013-01-11
draft: false
tags: [C#,Hadoop,Thrift,.net,资源管理器]
categories: [Hadoop]
---

![](/Content/upload/Img20130111/fpg53zh0.rd4.jpg) 
终于可以发布第一版本Hadoop的资源管理器了，之前写过c#访问Hadoop文件的文章，有不少朋友发邮件询问详细的连接代码。经过几天的努力把一个初步的版本发布了，下面来几张截图看看效果。

![](/Content/attached/image/20130111/zatvwnye_cmr.jpg) 



![](/Content/attached/image/20130111/qhpdvz4s_bsc.jpg) 

  本项目主要为了解决Windows平台下的访问Hadoop文件的问题。使用Hadoop文件系统的朋友应该都知道Hadoop是Java语言开发的，并且需要部署在Linux平台上，所以这就对于.Net平台的开发者带来诸多困难。本项目以C#语言通过thrift方式连接Hadoop的HDFS提供Hadoop文件的读、写、删除、重命名、列表等功能，同时提供一个WinForm形式的资源管理器，可以方面Windows平台下的用户集中管理文件（Hadoop本身提供了一个Web形式的文件浏览接口，但是不能满足现有文件管理需求）。 项目现有提供的功能包括：  1、列表。可以以资源管理器方式列表文件及文件信息（如：文件格式、大小等）  2、文件下载。可以将HDFS中的文件下载到本地，可以单个或批量，按照列表下载。  3、文件上传。可以将本地的文件上传至HDFS，同样支持单个、批量。  4、文件修改。可以直接重命名、复制、粘贴、剪切HDFS中的文件。  5、删除文件恢复。可以自定义文件删除恢复机制，在指定的时间段内恢复文件。

源码放在了github上，有兴趣的可以去[这里](https://github.com/gudao/NetHadoop)（https://github.com/gudao/NetHadoop）交流。
 
- - -
 