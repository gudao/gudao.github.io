---
title: "[转]ubuntu10.04不能上网解决"
date: 2012-06-27
draft: false
tags: [ubuntu不能上网问题,eth0]
categories: [Ubuntu]
---

一、问题


    Atheros公司出品的ar8151网卡，硬件ID为1969: 1083在ubuntu下无法正常驱动？



二、方案



    1. 本网卡驱动已经集成在ubuntu10.04中了，但默认没有加载



    2. lspci -vv | grep Atheros (查看网卡型号)



       Ethernet controller: Atheros Communications Device 1083 (rev c0)



    3. sudo modprobe atl1c (加载网卡驱动)



    4. sudo chmod a+w /sys/bus/pci/drivers/atl1c/new_id (增加文件写权限) 



    5. sudo echo &#34;1969 1083&#34; &gt; /sys/bus/pci/drivers/atl1c/new_id (修改网卡驱动参数)



    6. sudo chmod a-x /sys/bus/pci/drivers/atl1c/new_id (恢复文件权限)
 
- - -
 