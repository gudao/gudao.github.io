---
title: "[nginx小技巧]413 Request Entity Too Large"
date: 2012-10-15
draft: false
tags: [nginx小技巧,nginx,413]
categories: [Nginx]
---

某些网站用了Ext.js，但是用Nginx代理后容易出现413错误（ Request Entity Too Large）。可能是因为上传或请求的内容太大解决方法：

修改 client_max_body_size 

如：




<pre>http {
...
client_max_body_size 8m;
...
}</pre>

设置客户可以上传的大小为8MB即可。

 
- - -
 