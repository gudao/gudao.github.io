---
title: "android手机模拟器安装"
date: 2012-06-11
draft: false
tags: [Android SDK,Android模拟器]
categories: [科技]
---

1.下载Android SDK,地址在http://developer.android.com/sdk/index.html

2.下载并安装JDK，地址在这里http://www.oracle.com/technetwork/java/javase/downloads/jdk-7u4-downloads-1591156.html

3.解压Android SDK，配置环境变量添加解压包路径，我的为E:\tool\android-sdk-windows\platform-tools

  （注意：网上其他的都为旧版本的SDK，它的路径为android-sdk-windows\tools最新版本，将adb移到了platform-tools文件夹中）

4.启动SDK Manager.exe，下载需要的API版本

5.启动AVD Manager.exe，这个过程比较慢，如果界面上显示几个字母，说明已经成功，别关，继续待等待就行。

6.如果你的不能上网，请继续往下看。

7.下面直说命令

    win+r --打开运行

    cmd  --命令行

    adb shell --Android 命令

    getprop --查看所有属性设置

    setprop net.dns1 192.168.1.1--修改DNS地址，如果IP也需要设置，请就改 net.gprs.local-ip

    完成

8.如果还想安装应用程序，请adb install *.apk文件

    

参考地址：http://blog.sina.com.cn/s/blog_5f30b9410100p3ld.html
 
- - -
 