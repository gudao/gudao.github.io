---
title: "[nginx小技巧]httpheader准确显示命中状态"
date: 2012-10-10
draft: false
tags: [nginx小技巧,nginx]
categories: [Nginx]
---

![](/Content/upload/Img20120831/aw5i1dna.01h.gif) 
nginx缓没缓存请求是我们关心的问题，把缓存状态显示在header里是个不错的选择。

方法如下：

    使用$upstream_cache_status 变量 

    示例：add_header ADKS-Cache     &#34;$upstream_cache_status  from  www.front2end.com&#34;; 
   变量值：

- MISS
- EXPIRED - expired, request was passed to backend
- UPDATING - expired, stale response was used due to proxy/fastcgi_cache_use_stale updating
- STALE - expired, stale response was used due to proxy/fastcgi_cache_use_stale
- HIT

效果：

 相应头信息

    

<table><tbody role="list"><tr role="listitem"><td role="presentation">ADKS-Cache </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>HIT  from www.front2end.com</code> </td></tr><tr role="listitem"><td role="presentation">Cache-Control </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>max-age=3600</code> </td></tr><tr role="listitem"><td role="presentation">Connection </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>keep-alive</code> </td></tr><tr role="listitem"><td role="presentation">Date </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>Wed, 10 Oct 2012 01:41:57 GMT</code> </td></tr><tr role="listitem"><td role="presentation">Etag </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>&#34;0463e6df954cd1:0&#34;</code> </td></tr><tr role="listitem"><td role="presentation">Expires </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>Wed, 10 Oct 2012 02:41:57 GMT</code> </td></tr><tr role="listitem"><td role="presentation">Last-Modified </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>Thu, 28 Jun 2012 06:44:12 GMT</code> </td></tr><tr role="listitem"><td role="presentation">Server </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>nginx/1.2.4</code> </td></tr><tr role="listitem"><td role="presentation">X-Powered-By </td><td valign="top"></td><td valign="top"></td><td valign="top"></td><td role="list"><code>ASP.NET</code> </td></tr></tbody></table>


 
- - -
 