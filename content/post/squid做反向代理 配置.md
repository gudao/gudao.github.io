---
title: "squid做反向代理 配置"
date: 2012-04-20
draft: false
tags: [Squid,反向代理,CDN]
categories: [科技]
---

![](/Content/upload/Img20120420/ylatpvt1.wa4.jpg) 
最近在研究Squid，看了半天《Squid权威指南》也没配置成功！转发一篇我看了后配置成功的文章！

本文在介绍 squid 反向代理的工作原理的基础上，指出反向代理技术在提高网站访问速度，增强网站可用性、安全性方面有很好的用途。作者在具体的实验环境下，利用 DNS 轮询和 Squid 反向代理技术，实现了网站的负载均衡，从而提高了网站的可用性和可靠性。

现在有许多大型的门户网站如 SINA 都采用 squid 反向代理技术来加速网站的访问速度，可将不同的 URL 请求分发到后台不同的 WEB 服务器上，同时互联网用户只能看到反向代理服务器的地址，加强了网站的访问安全。

<a name="1.反向代理的概念"></a>反向代理的概念 

反向代理服务器又称为 WEB 加速服务器，它位于 WEB 服务器的前端，充当 WEB 服

务器的内容缓存器。其系统结构如图 1
<a name="N10061"></a>**图 1. 系统结构**![系统结构](http://www.ibm.com/developerworks/cn/linux/l-cn-squid/images/image001.jpg) 
反向代理服务器是针对 WEB 服务器设置的，后台 WEB 服务器对互联网用户是透明的，用户只能看到反向代理服务器的地址，不清楚后台 WEB 服务器是如何组织架构的。当互联网用户请求 WEB 服务时，DNS 将请求的域名解析为反向代理服务器的 IP 地址，这样 URL 请求将被发送到反向代理服务器，由反向代理服务器负责处理用户的请求与应答、与后台 WEB 服务器交互。利用反向代理服务器减轻了后台 WEB 服务器的负载，提高了访问速度，同时避免了因用户直接与 WEB 服务器通信带来的安全隐患。

[回页首](http://www.ibm.com/developerworks/cn/linux/l-cn-squid/#ibm-pcon) 

<a name="2.Squid 反向代理的实现原理"></a>Squid 反向代理的实现原理 

目前有许多反向代理软件，比较有名的有 Nginx 和 Squid 。 Nginx 是由 Igor Sysoev 为俄罗斯访问量第二的 Rambler.ru 站点开发的，是一个高性能的 HTTP 和反向代理服务器，也是一个 IMAP/POP3/SMTP 代理服务器。

<code>Squid</code><code> </code>是由美国政府大力资助的一项研究计划，其目的为解决网络带宽不足的问题，支持<code>HTTP</code>，HTTPS，FTP 等多种协议，是现在 Unix 系统上使用、最多功能也最完整的一套软体。下面将重点介绍 Squid 反向代理的实现原理和在提高网站性能方面的应用。

<code>Squid</code>反向代理服务器位于本地 WEB 服务器和 Internet 之间 , 组织架构如图 2：
<a name="N1008E"></a>**图 2. 组织架构**![组织架构](http://www.ibm.com/developerworks/cn/linux/l-cn-squid/images/image002.jpg) 
客户端请求访问 WEB 服务时，DNS 将访问的域名解析为 Squid 反向代理服务器的 IP 地址，这样客户端的 URL 请求将被发送到反向代理服务器。如果 Squid 反向代理服务器中缓存了该请求的资源，则将该请求的资源直接返回给客户端，否则反向代理服务器将向后台的 WEB 服务器请求资源，然后将请求的应答返回给客户端，同时也将该应答缓存在本地，供下一个请求者使用。

Squid 反向代理一般只缓存可缓冲的数据（比如 html 网页和图片等），而一些 CGI 脚本程序或者 ASP、JSP 之类的动态程序默认不缓存。它根据从 WEB 服务器返回的 HTTP 头标记来缓冲静态页面。有四个最重要 HTTP 头标记：


- Last-Modified: 告诉反向代理页面什么时间被修改
- Expires: 告诉反向代理页面什么时间应该从缓冲区中删除
- Cache-Control: 告诉反向代理页面是否应该被缓冲
- Pragma: 用来包含实现特定的指令，最常用的是 Pragma:no-c<code>ache</code><code> </code> 

[回页首](http://www.ibm.com/developerworks/cn/linux/l-cn-squid/#ibm-pcon) 

<a name="3.利用 Squid 反向代理加速网站实例"></a>利用 Squid 反向代理加速网站实例 

本实例的域名是 wenjin.cache.<code>ibm.com.cn</code>，通过<code>DNS</code>的轮询技术，将客户端的请求分发给其中一台 Squid 反向代理服务器处理，如果这台 Squid 缓存了用户的请求资源，则将请求的资源直接返回给用户，否则这台 Squid 将没有缓存的请求根据配置的规则发送给邻居 Squid 和后台的 WEB 服务器处理，这样既减轻后台 WEB 服务器的负载，又提高整个网站的性能和安全性。该系统结构图 3 如下：
<a name="N100C7"></a>**图 3. 系统结构**![系统结构](http://www.ibm.com/developerworks/cn/linux/l-cn-squid/images/image003.jpg) 
<a name="N100D4"></a>配置的系统环境： 


- 一台 DNS 服务器：操作系统 Freebsd，软件 BIND 9.5，IP 192.168.76.222 ；
- 三台 Squid 服务器：操作系统 Linux AS 4，软件 Squid 3.0，相应的 IP 如下：


<table><tbody><tr><td>

<pre>Squid1：192.168.76.223 
 Squid2：192.168.76.224 
 Squid3：192.168.76.225</pre>

</td></tr></tbody></table>



- 三台 WEB 服务器：操作系统 Linux AS 4，应用软件 Tomcat 5.0+Mysql，相应的 IP 地址如下：


<table><tbody><tr><td>

<pre>webServer1：210.82.118.195 
 webServer2：192.168.76.226 
 webServer1：192.168.76.227</pre>

</td></tr></tbody></table>


<a name="N100F0"></a>应用软件的安装和配置 

**配置 DNS 服务器** 

软件利用 Freebsd 自带的 bind 9.5 。然后针对该系统配置 bind，首先修改 bind 的配置文件 /etc/namedb/named.conf，在文件中添加


<table><tbody><tr><td>

<pre>zone &#34;cache.ibm.com.cn&#34;{ 
        type master; 
        file &#34;master/ cache.ibm.com.cn &#34;; 
 };</pre>

</td></tr></tbody></table>


再在 /etc/namedb/master 目录下添加 cache.ibm.com.cn 文件，该文件的内容如下：


<table><tbody><tr><td>

<pre>$TTL    3600 
 @       IN      SOA     search. ibm.com.cn. root. ibm.com.cn.  ( 
                                20080807        ; Serial 
                                3600    ; Refresh 
                                900     ; Retry 
                                3600000 ; Expire 
                                3600 )  ; Minimum 
        IN      NS      search.ibm.com.cn. 
 1       IN      PTR     localhost.ibm.com.cn. 
 wenjin  IN      A       192.168.76.223 
 wenjin  IN      A       192.168.76.224 
 wenjin  IN      A       192.168.76.225</pre>

</td></tr></tbody></table>


这样当用户请求的时候，DNS 通过轮询机制将 wenjin.cache.ibm.com.cn 的域名解析为 192.168.76.223、192.168.76.224 和 192.168.76.225 其中之一。

配置完成后，运行 rndc star t 启动 bind 服务。可在 /etc/rc.conf 中设置 named_enable=&#34;YES&#34; 使得开机自启动。

用 ps – A |grep named 查看 bind 服务是否起来；

用 nslookup wenjin.cache.ibm.com.cn 测试 bind 服务是否正常运行。

**配置 Squid1 服务器** 

- 下载 squid-3.0.STABLE8.tar.gz 源码包，将其放在 /home 目录下
- 解压缩<code>tar – zxvf squid-3.0.STABLE8.tar.gz</code> 设置配置参数：<code>cd squid-3.0.STABLE10</code> 



<pre>./configure – prefix=/usr/local/squid</pre>


将 squid 安装在 /usr/local 目录下
- 编译安装：<code>make&amp;make install</code>安装完以后会在 /usr/local 目录下看见 squid 目录。
- 配置 squid 配置文件
编辑 squid.conf 文件，<code>vi /usr/local/squid/etc/squid.conf</code> 




<pre>cache_effective_user squid 
 cache_effective_group squid 
 ######### 设定 squid 的主机名 , 如无此项 squid 将无法启动
 visible_hostname squid1.nlc.gov.cn 
 ############# 配置 squid 为加速模式 ################# 
 http_port 80 accel vhost vport 
 icp_port 3130 
 ##### 配置 squid2、squid3 为其邻居，当 squid1 在其缓存中没有找到请求的资源时，
          通过 ICP 查询去其邻居中取得缓存
 cache_peer squid2.ibm.com.cn sibling 80 3130 
 cache_peer squid3.ibm.com.cn sibling 80 3130 
 ##### squid1 的三个父节点，originserver 参数指明是源服务器，
 round-robin  参数指明 squid 通过轮询方式将请求分发到其中一台父节点；
 squid 同时会对这些父节点的健康状态进行检查，如果父节点 down 了，
那么 squid 会从剩余的 origin 服务器中抓取数据
 cache_peer 210.82.118.195 parent 8080 0 no-query originserver round-robin \ 
                                              name=webServer1 
 cache_peer 192.168.76.226 parent 8080 0 no-query originserver round-robin \ 
                                              name=webServer2 
 cache_peer 192.168.76.227 parent 8080 0 no-query originserver round-robin \ 
                                            name=webServer3 
 #### 将 wenjin.cache.ibm.com.cn 域的请求通过 RR 轮询方式转发到三个父节点中的一个
 cache_peer_domain webServer1 webServer2 webServer3 wenjin.cache.ibm.com.cn 
 ##### 下面是一些访问控制、日志和缓存目录的设置
 acl localnet src 192.168.76.223 192.168.76.224 192.168.76.225 
 acl all src 0.0.0.0/0.0.0.0 
 http_access allow all 
 icp_access allow localnet 
 cache_log /usr/local/squid/var/logs/cache.log 
 access_log /usr/local/squid/var/logs/access.log squid 
 cache_dir ufs /usr/local/squid/var/cache/ 1000 16 256 
 ####### 对 squid 的一些优化 ############### 
 maximum_object_size 10240 KB  ### 能缓存的最大对象为 10M 
 maximum_object_size_in_memory 512 KB ### 内存中缓存的最大对象 512K 
 cache_mem 256 MB  ###squid 用于缓存的内存量</pre>



保存后 :wq 退出。

在 /etc/hosts 文件中添加




<pre>192.168.76.223  squid1.ibm.com.cn 
 192.168.76.224  squid2.ibm.com.cn 
 192.168.76.225  squid3.ibm.com.cn</pre>



保存后 : wq 退出。

检查 squid 配置文件正确与否：<code>/usr/local/squid/bin/squid – k parse</code> 

生成缓存目录<code>/usr/local/squid/bin/squid – z</code> 

启动<code>squid：/usr/local/squid/bin/squid</code> 

**配置 squid2 和 squid3 服务器** 

squid2 和 squid3 服务器的配置方法和配置参数和 squid1 一样，配置完成后，分别启动这两个服务器上的 squid 服务。

在 squid 的日志文件 cache.log 中，出现如下日志信息则说明三台 squid 之间成功配置为 sibling，且配置了三个父代理。




<pre>2008/11/17 10:08:47| Configuring Sibling squid1.ibm.com.cn/80/3130 
 2008/11/17 10:08:47| Configuring Sibling squid3.ibm.com.cn/80/3130 
 2008/11/17 10:08:47| Configuring Parent 210.82.118.195/8080/0 
 2008/11/17 10:08:47| Configuring Parent 192.168.76.226/8080/0 
 2008/11/17 10:08:47| Configuring Parent 192.168.76.227/8080/0 
 2008/11/17 10:08:47| Ready to serve requests.</pre>



**测试** 

测试之前，保证 DNS 服务、三台 squid 服务和三台 web 服务都正常起来。在客户端输入[http://wenjin.cache.ibm.com.cn](http://wenjin.cache.nlc.gov.cn/)，则正确的显示该网页。服务器端的响应对客户端是透明的，客户端不知道请求是由哪台 WEB 服务器处理的；而且其中某台 Squid 服务器或 WEB 服务器发生故障，也不影响服务的正常运行。

[回页首](http://www.ibm.com/developerworks/cn/linux/l-cn-squid/#ibm-pcon) 

<a name="总结"></a>总结 

Squid 是一个开源的软件，利用它的反向代理技术可以提高网站系统的访问速度。本文在真实的网络环境下，利用三台 squid 反向代理服务器加速了网站的性能，同时结合 DNS 轮询技术实现了网站的负载均衡。经过一段时间的测试和试运行，该网站的访问速度和可用性方面都有很大的提高，从未出现过网站服务中断情况。

原文地址：http://www.ibm.com/developerworks/cn/linux/l-cn-squid/

祝你配置成功！
 
- - -
 