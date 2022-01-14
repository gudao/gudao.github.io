---
title: "自定义httpModule在IIS6和IIS7下的配置区别"
date: 2012-05-03
draft: false
tags: [httpModule,IIS6,IIS7]
categories: [.Net]
---

微软真的很气人，不同IIS配置文件要不同配置，部署一个程序时间都花在查找区别上了。

IIS6下需要配置在&lt;system.web&gt;节点下：如

        &lt;httpModules&gt;            &lt;add name=&#34;自定义名称&#34; type=&#34;命名空间,程序集名称&#34; /&gt;        &lt;/httpModules&gt;

IIS7下则需要配置在&lt;system.webServer&gt;节点下：如

        &lt;modules runAllManagedModulesForAllRequests=&#34;true&#34;&gt;            &lt;add name=&#34;自定义名称&#34; type=&#34;命名空间,程序集名称&#34; /&gt;        &lt;/modules&gt;


 
- - -
 