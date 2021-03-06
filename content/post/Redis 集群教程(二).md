---
title: "Redis 集群教程(二)"
date: 2013-12-27
draft: false
tags: [Redis cluster tutorial,consistency guarantees]
categories: [Redis]
---

![](/Content/upload/Img20131226/s_02wcido5_0xx.png) 
  一致性保证 

Redis 集群不能严格的保证数据的一致性，这就意味着在实际应用中它会漏写一些数据。异步复制可能是导致此结果的首要原因，下面我们举例说明集群在进行写操作时的三个步骤： 

1、客户端向节点B写入数据； 

2、节点B返回“成功”给客户端； 

3、节点B将此写入操作复制给它的从节点B1、B2和B3。 

值得我们注意的是节点B在将该操作复制给它的从节点B1、B2、B3之前就将“成功”返回给了客户端。这将导致一个令人望而却步的潜在问题，就是如果节点B1没有能够成功的复制该写入操作，而节点B又在这时崩溃了，更恰巧的是节点B1又被升级为主节点B（此机制在主从模式中介绍过），这样最终的结果就是该写入操作在当前的节点B（原来的节点B1）中不存在，此次写入操作就彻底的被丢掉了。 

与此非常类似的情况是传统数据库为了保持数据的一致性，通常都被配置为每秒将数据写入到硬盘一次，而不是在每次返回操作结果之前把数据写入到硬盘，不这样做的原因就是因为这将严重的影响数据库的性能。 

注意：Redis集群将来会支持用户显式地使用同步写机制。 

官方还给出了一个丢失写操作的例子，不好意思没看懂。 
 
- - -
 