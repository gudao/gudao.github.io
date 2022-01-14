---
title: "[转]CentOS Rsync服务端与Windows cwRsync客户端实现数据同步"
date: 2012-09-07
draft: false
tags: [rsync,数据同步]
categories: [服务器]
---

![](/Content/upload/Img20120907/qv2qdap0.dze.jpg) 
**原文地址：[http://www.osyunwei.com/archives/3769.html](http://www.osyunwei.com/archives/3769.html)** 

按照下文配置需要注意两点

1.客户端认证失败    rsync.pass文件的权限必须为其他用户不可访问。查看rsync.log下面会有如下提示：    secrets file must not be other-accessible (see strict modes option)2.配置文件问题    rsyncd.conf文件中最好别有注释

 

**说明：** 
1、Rsync服务端系统：CentOS 5.5IP地址：192.168.21.160数据存放目录：/data/osyunwei2、cwRsync客户端系统：Windows Server 2003IP地址：192.168.21.130同步的目录：D:\osyunwei**实现目的：**cwRsync客户端每天凌晨3:00钟自动同步Rsync服务端/data/osyunwei目录中的数据到D:\osyunwei目录**一、Rsync服务端配置**1、关闭SELINUXvi /etc/selinux/config #编辑防火墙配置文件#SELINUX=enforcing #注释掉#SELINUXTYPE=targeted #注释掉SELINUX=disabled #增加:wq #保存，关闭shutdown -r now #重启系统2、开启防火墙tcp 873端口（Rsync默认端口）vi /etc/sysconfig/iptables #编辑防火墙配置文件-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 873 -j ACCEPT:wq! #保存/etc/init.d/iptables restart #最后重启防火墙使配置生效3、安装Rsync服务端软件yum install rsync xinetd   #安装vi /etc/xinetd.d/rsync  #编辑配置文件，设置开机启动rsyncdisable = no #修改为/etc/init.d/xinetd start  #启动（CentOS中是以xinetd 来管理Rsync服务的）4、创建rsyncd.conf配置文件vi /etc/rsyncd.conf  #创建配置文件，添加以下代码log file = /var/log/rsyncd.log   #日志文件位置，启动rsync后自动产生这个文件，无需提前创建。[osyunwei]    #自定义名称path = /data/osyunwei   #Rsync服务端数据目录路径comment = osyunwei    #模块名称与[osyunwei]自定义名称相同uid = root  #设置rsync运行权限为rootgid = root  #设置rsync运行权限为rootport=873    #默认端口read only = no   #设置为no，cwRsync客户端可上传文件,yes只读write only = no  #设置为no，cwRsync客户端可下载文件,yes不能下载auth users = osyunweibakuser   #执行数据同步的用户名，可以设置多个，用英文状态下逗号隔开secrets file = /etc/rsync.pass   #用户认证配置文件，里面保存用户名称和密码，后面会创建这个文件hosts allow = 192.168.21.130  #允许进行数据同步的客户端IP地址，可以设置多个，用英文状态下逗号隔开hosts deny = 192.168.21.254   #禁止数据同步的客户端IP地址，可以设置多个，用英文状态下逗号隔开list = yes  #显示Rsync服务端资源列表:wq! #保存5、创建用户认证文件vi  /etc/rsync.pass     #配置文件，添加以下内容osyunweibakuser:123456  #格式，用户名:密码，可以设置多个，每行一个用户名:密码:wq! #保存6、设置文件权限chmod 600 /etc/rsyncd.conf  #设置文件所有者读取、写入权限chmod 600 /etc/rsync.pass   #设置文件所有者读取、写入权限7、启动rsync/etc/init.d/xinetd start   #启动service xinetd stop   #停止service xinetd restart #重新启动**二、cwRsync客户端设置**1、下载cwRsync客户端软件下载地址：[http://jaist.dl.sourceforge.net/project/sereds/cwRsync/4.0.3/cwRsync_4.0.3_Installer.zip](http://jaist.dl.sourceforge.net/project/sereds/cwRsync/4.0.3/cwRsync_4.0.3_Installer.zip)2、安装cwRsync客户端   解压cwRsync_4.0.3_Installer.zip 双击打开cwRsync_4.0.3_Installer.exe   Next 下一步   IAgree 同意安装   Next   默认安装路径 C:\Program Files\cwRsync   Install 安装   Close 安装完成，关闭3、测试是否与Rsync服务端通信成功开始-运行-cmd输入cd C:\Program Files\cwRsync\bin   回车再输入telnet 192.168.21.160 873   回车出现telnet界面，说明与Rsync服务端通信成功备注 C:\Program Files\cwRsync\bin  是指cwRsync程序安装路径4、cwRsync客户端同步Rsync服务端的数据开始-运行-cmd，输入cd C:\Program Files\cwRsync\bin 回车再输入rsync --port=873 -vzrtopg   --progress --delete osyunweibakuser@192.168.21.160::osyunwei /cygdrive/d/osyunwei输入密码：123456 回车开始同步数据，等待执行完成，可以打开D:\osyunwei 与Rsync服务端/data/osyunwei目录中的数据对比一下，查看是否相同说明：d/osyunwei 代表D:\osyunweiosyunwei 代表/etc/rsyncd.conf文件中自定义的模块名称comment = osyunwei192.168.21.160  #Rsync服务端IP地址osyunweibakuser    #执行数据同步的用户--port=873  #端口-vzrtopg --progress #显示同步过程详细信息--delete  #从cwRsync客户端D:\osyunwei目录中删除与Rsync服务端/data/osyunwei目录中不相同的数据，即保证两边的数据完全一致**三、在cwRsync客户端的任务计划中添加批处理脚本文件，每天凌晨3:00钟自动同步Rsync服务端/data/osyunwei目录中的数据到D:\osyunwei目录**1、打开C:\Program Files\cwRsync\bin目录新建passwd.txt输入123456保存继续在C:\Program Files\cwRsync\bin目录新建osyunwei.bat输入@echo offecho.echo 开始同步数据，请稍等...echo.cd C:\Program Files\cwRsync\binrsync -vzrtopg --port=873 --progress --delete osyunweibakuser@192.168.21.160::osyunwei /cygdrive/d/osyunwei &lt; passwd.txtecho.echo 数据同步完成echo.最后保存退出2、添加批处理脚本到Windows任务计划开始-设置-控制面板-任务计划打开添加任务计划，下一步浏览，选择打开C:\Program Files\cwRsync\bin目录里面的osyunwei.bat执行这个任务，选择每天，下一步起始时间：3:00运行这个任务：每天，下一步输入Windows系统管理员的登录密码，下一步完成扩展说明：如果要调整同步的时间，打开任务计划里面的osyunwei切换到日程安排来选项设置，还可以打开高级来设置每隔几分钟运行一次osyunwei.bat这个脚本**至此，CentOS Rsync服务端与Windows cwRsync客户端实现数据同步完成** 
- - -
 