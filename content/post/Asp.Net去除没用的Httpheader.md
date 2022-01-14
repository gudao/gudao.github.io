---
title: "Asp.Net去除没用的Httpheader"
date: 2012-03-07
draft: false
tags: [Http响应标头]
categories: [.Net]
---

使用IIS和Asp.net的Web服务器,通常在默认情况下，HTTP响应头会包含3个Web服务器的自身识别头:

1:X-Powered-By

2:X-AspNet-Version

3:X-AspNetMvc-Version

去除方法分别为：

1：Internet 信息服务(IIS)管理器-》Http相应标头-》删除X-Powered-By即可，或者改为自己喜欢的内容

2：只需要在Web.Config的&lt;system.web&gt;节点下添加如下内容: &lt;httpRuntime enableVersionHeader=&#34;false&#34; /&gt; 

3：在Global.asax的<code>Application_Start</code>事件中将<code>MvcHandler类的<code>DisableMvcResponseHeader属性设置为True</code></code>  

MvcHandler.DisableMvcResponseHeader = true;

移除服务器自身识别响应头会有如下好处：

    这降低了服务器和浏览器之间所需传输的数据量

    使黑客攻击服务器变得更加困难，从而使服务器更加强壮


 
- - -
 