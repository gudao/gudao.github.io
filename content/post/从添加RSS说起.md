---
title: "从添加RSS说起"
date: 2012-10-08
draft: false
tags: [rel,RSS]
categories: [服务器]
---

博客在添加新Logo的同时，新添加了RSS订阅功能，不知道细心的你有没有发现。功能刚完成就用google reader订阅了自己（哈哈，有点小自恋），在订阅地址栏直接输入域名www.front2end.cn，结果显示没有找到可订阅内容！当时就纳闷儿了，为什么别人的网站可以，我的就不可以？

昨天无意中发现一个网站的源代码中有段类似的内容：


<pre>&lt;link rel=&#34;<a class="attribute-value">alternate</a>&#34; type=&#34;<a class="attribute-value">application/atom+xml</a>&#34; title=&#34;<a class="attribute-value">Recent Entries</a>&#34; href=&#34;...<a href="view-source:http://www.mnot.net/blog/index.atom" class="attribute-value"></a>&#34;&gt; </pre>

看到此代码时才恍然大悟，原来是订阅器不知道我的RSS地址，于是在我的博客首页里添加如下内容：

<pre>&lt;link id=&#34;<a class="attribute-value">RSSLink</a>&#34; href=&#34;<a href="view-source:http://www.front2end.cn/Feed" class="attribute-value">/Feed</a>&#34; rel=&#34;<a class="attribute-value">alternate</a>&#34; type=&#34;<a class="attribute-value">application/rss+xml</a>&#34; title=&#34;<a class="attribute-value">RSS</a>&#34;&gt; </pre>

哈哈，这下就可以了，订阅时不用再输入订阅的具体地址了。

扩展内容：


## rel 属性


rel 属性描述了当前页面与href所指定文档的关系.


- rel属性通常出现在a,link标 签中
- 属性值
  - alternate -- 定义交替出现的链接
  - <code>appendix</code> -- 定义文档的附加信息
  - <code>bookmark</code> -- 书签
  - <code>chapter</code> -- 当前文档的章节
  - <code>contents</code> 
  - <code>copyright</code> -- 当前文档的版权
  - <code>glossary</code> -- 词汇
  - <code>help</code> -- 链接帮助信息
  - <code>index</code> -- 当前文档的索引
  - next -- 记录文档的下一页.(浏览器可以提前加载此页)
  - <code>nofollow</code> -- 不被用于计算PageRank
  - prev -- 记录文档的上一页.(定义浏览器的后退键)
  - <code>section</code> -- 作为文档的一部分
  - start -- 通知搜索引擎,文档的开始
  - stylesheet -- 定义一个外部加载的样式表
  - <code>subsection</code> -- 作为文档的一小部分


 
- - -
 