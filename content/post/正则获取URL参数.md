---
title: "正则获取URL参数"
date: 2012-10-26
draft: false
tags: [js,正则表达式,获取url参数]
categories: [建站技巧]
---

<code>function</code> <code>getQueryStringRegExp(name)</code> 

<code>{</code> 

<code>    </code><code>var</code> <code>reg = </code><code>new</code> <code>RegExp(</code><code>&#34;(^|\\?|&amp;)&#34;</code><code>+ name +</code><code>&#34;=([^&amp;]*)(\\s|&amp;|$)&#34;</code><code>, </code><code>&#34;i&#34;</code><code>);  </code> 

<code>    </code><code>if</code> <code>(reg.test(location.href)) </code><code>return</code> <code>unescape(RegExp.$2.replace(/\+/g, </code><code>&#34; &#34;</code><code>)); </code><code>return</code> <code>&#34;&#34;</code><code>;</code> 

<code>};</code> 
 
- - -
 