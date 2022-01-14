---
title: "jw player 中文文件名问题"
date: 2012-09-17
draft: false
tags: [jw palyer中文编码,encodeURIComponent]
categories: [服务器]
---

可恶的jwplayer不能用中文做为file参数，发送到服务器端的参数总是编码有问题，今天终于找到问题解决方法了。

用JS的<code>encodeURIComponent</code>方法编码两次即可，比如：<code>&#39;file&#39;: encodeURIComponent(encodeURIComponent(&#39;哈哈我是中文.flv&#39;)),</code> 

但是要注意中文文件名一定也要是utf-8的编码。

.net的默认应该就是Utf-8,java需要用urlencode转一下utf-8。

至此其实不算结束，我后端用的是asp.net写的读取flv文件，用上面的方法还是可以的。

但是用windows 版的nginx做flv服务器（注：linux版的nginx无此问题）还是找不到中文文件，如果你是这方面的高手请明示，多谢！
 
- - -
 