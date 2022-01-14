---
title: "怎样使用Windows认证连接SQL Server"
date: 2012-03-12
draft: false
tags: [sql server]
categories: [.Net]
---

![](/Content/upload/Img20120312/csmcd0m0.bhb.jpg) 
连接方式有两种，如果你是在web.config文件中&lt;**connectionStrings**&gt;节点中就必须包括**Trusted_Connection=Yes**或者是**Integrated Security=SSPI**，下面是两个示例：

&lt;add name=&#34;MyDbConn1&#34; connectionString=&#34;Server=MyServer;Database=MyDb;Trusted_Connection=Yes;&#34;/&gt;

&lt;add name=&#34;MyDbConn2&#34; connectionString=&#34;Initial Catalog=MyDb;Data Source=MyServer;Integrated Security=SSPI;&#34;/&gt;

顺便也把帐号登录记一下吧，直接看示例：

&lt;add name=&#34;MyDbConn3&#34; connectionString=&#34;server=MyServer;database=MyDb;uid=aa;pwd=bb“ /&gt;
 
- - -
 