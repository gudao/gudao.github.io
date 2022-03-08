---
title: "杂想"
date: 2022-01-27
draft: true
keywords: ["商业本质","交易","信息不对称","信用不传递","创造价值"]
description: "商业的本质到底是什么？交易、价值"
tags: ["商业","战略"]
categories: ["商业"]
author: "古道" 
---

# 问题
* 是否能做好自己的定位
* 哪些条件是为了完成这个定位所必要的条件
* 你的核心是什么
* 哪些是不重要的
# 答案
* 完成目标用户的特定需求
* 

407059028
1000000007
pNt^10W21b_dx
10 20  35 23 54 33
10*35*54 /20*

# 认准了一件事要坚持做完
# 不着急不害怕不要脸

``` shell
THIS_NAME=node01
THIS_IP=127.0.0.1
CLUSTER=node01=http://${THIS_IP}:2380,node02=http://${THIS_IP}:2381,
TOKEN=haha
./etcd --data-dir=data.etcd1 --name ${THIS_NAME} \
	--initial-advertise-peer-urls http://${THIS_IP}:2380 --listen-peer-urls http://${THIS_IP}:2380 \
	--advertise-client-urls http://${THIS_IP}:2379 --listen-client-urls http://${THIS_IP}:2379 \
	--initial-cluster ${CLUSTER} \
	--initial-cluster-state new --initial-cluster-token ${TOKEN}
	
	./etcd  --name node02 \
		--data-dir=data.etcd2 \
		--initial-advertise-peer-urls http://127.0.0.1:2381 \
		--listen-peer-urls http://127.0.0.1:2381 \
		--advertise-client-urls http://127.0.0.1:2378 \
		--listen-client-urls http://127.0.0.1:2378 \
		--discovery https://discovery.etcd.io/088c30f8069dca507c126cbb7e5251dd
		
		--initial-cluster ${CLUSTER} \
		--initial-cluster-state new \
		--initial-cluster-token ${TOKEN}
		
	./etcd  --name node03 \
		--data-dir=data.etcd3 \
		--initial-advertise-peer-urls http://127.0.0.1:2382 \
		--listen-peer-urls http://127.0.0.1:2382 \
		--advertise-client-urls http://127.0.0.1:2377 \
		--listen-client-urls http://127.0.0.1:2377 \
		--discovery https://discovery.etcd.io/088c30f8069dca507c126cbb7e5251dd
```
新基建信息产业库微信小程序

pm2 list #查看所有应用
pm2 delete all #删除所有应用
pm2 bin/www start #以fork形式启动应用
pm2 start ecosystem.config.js #以配置文件形式启动
最终启动成功的参数为：pm2 start ecosystem.config.js --env pro
本次问题排查，多次尝试使用ecosystem.config.js形式启动，并更改其中的参数都未果。期间也曾使用 -env 参数尝试，
但因ecosystem中的配置为  "NODE_ENV": "production",故也把--env 设置为 production ，没能成功，就放弃了该方法。后来看到app.js 也有pro的设置没有猜测到更换--env参数，有点遗憾。
