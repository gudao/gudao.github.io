---
title: "Asp.Net 从Windows到Linux"
date: 2012-11-23
draft: false
tags: [mono,jexus,.net跨平台]
categories: [服务器]
---

昨天公司一个特殊客户购买产品必须部署在Linux上，导致我们的产品很被动。遇到这个问题却令我很兴奋，因为我整天自诩游走在Windows和Linux之间。闲话少扯。

 asp.net mvc3 在Linux下运行必须必备条件有2个：1、运行环境；2、Web服务器。没问题在Linux这两个条件都可以满足。Mono为.Net提供了很好的运行环境，具体效率是否比Windows下的.Net framework高还是低有待考证（据说Mono遵循微软的协议比微软自己还要严格）。Web服务器可以根据你的情况自由发挥，可选方案有apache，nginx,xsp,<code>jexus。个人不喜欢apache，nginx可能还要其他的CGI，xsp是单线程的，最终只能用Jexus了。下面说下具体安装步骤：</code> 

<code>1、安装Mono，要编译安装的话请Google，直接安装命令，sudo apt-get install mono-devel，无需任何配置安装完就OK，不得不感叹Mono很牛逼。</code> 

<code>2、</code>下载jexus，我用的是当前最新正式版5.1.4，官方地址是http://www.linuxdot.net/，你可以自由选择。

3、解压、配置jexus。再次还要感慨一下Jexus，它也很牛逼，有全中文的配置说明，我保证你认真读一遍根本不用看其他人的配置说明。

4、如果你是MVC项目，请把MVC需要的dll一并放在bin目录下，这些dll是：

System.Web.Mvc.dll

System.Web.Razor.dll

System.Web.WebPages.Deployment.dll

System.Web.WebPages.dll

System.Web.WebPages.Razor.dll
（如果你Windows可以运行MVC3，那么这些文件就在你的机器上，实在找不到就邮件我吧。）




最后我还要感慨一下Jexus的牛逼之处，小小的Web服务器功能甚是强大，竟然还包括Url重写，还支持反向代理负载均衡等。Jexus哥看好你哟！
 
- - -
 