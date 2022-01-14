---
title: "Hadoop集群安装笔记"
date: 2012-06-05
draft: false
tags: [Hadoop]
categories: [Hadoop]
---

![](/Content/upload/Img20120605/nlpnzrjq.ohh.jpg) 
安装过程遇到不少问题。具体安装步骤请参照上一篇转载。

安装完系统后，开启ssh    1、查看是否有/etc/ssh/sshd_config文件        cat /etc/ssh/sshd_config    2、如果有则：        /etc/init.d/ssh 　restart       没有这个文件则：        sudo apt-get install 　ssh    3.ssh-keygen -t rsa        回车        执行命令    sudo passwd root 设置root用户密码重新登录root帐号创建文件夹    /usr/hadoop/bak下载jdk修改jdk权限可以执行./jdk-6u32-linux-i586.bin复制以下内容： gedit /etc/environmentPATH=&#34;/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/hadoop/bak/jdk1.6.0_32/bin&#34;CLASSPATH=&#34;.:/usr/hadoop/bak/jdk1.6.0_32/lib&#34;JAVA_HOME=&#34;/usr/hadoop/bak/jdk1.6.0_32&#34;sudo update-alternatives --install /usr/bin/java java /usr/hadoop/bak/jdk1.6.0_32/bin/java 300sudo update-alternatives --install /usr/bin/javac javac /usr/hadoop/bak/jdk1.6.0_32/bin/javac 300复制conf文件添加ssh无密码登录    将id_rsa.pub的内容复制到每个机器（也包括本机）的/root/.ssh/authorized_keys文件中。    如果authorized_keys不存在，则使用touch     /root/.ssh/authorized_keys生成一个；如果该文件已经存在    复制authorized_keys    cat /root/.ssh/id_rsa.pub &gt;&gt; /root/.ssh/authorized_keys    确认可以连接成功，    ssh IP        如果不能且显示Agent admitted failure to sign using the key.    则运行    ssh-add /root/.ssh/id_rsa    如果显示Identity added: /root/.ssh/id_rsa (/root/.ssh/id_rsa)则为成功，如果显示    Could not open a connection to your authentication agent.则为错误，先运行    ssh-agent bash    再运行ssh-add /root/.ssh/id_rsa动态添加节点    1.把新机器的增加到conf/slaves文件中（datanode或者tasktracker crash则可跳过）    2.在新机器上进入hadoop安装目录          $bin/hadoop-daemon.sh start datanode          $bin/hadoop-daemon.sh start tasktracker    3.在namenode上          $bin/hadoop balancer
 
- - -
 