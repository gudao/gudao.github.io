---
title: "如何理解认证和授权"
date: 2022-02-17
draft: flase
keywords: ["认证","授权","OAuth2.0","OpenId Connect","OIDC"]
tags: ["认证","授权"]
categories: ["认证授权"]
author: "古道" 
---
# 授权
## Client Credentials Grant
先从简单的说，授权的意思就是告诉拿东西的人，你想要这个东西，看他给不给你？给你就是授权成功，不给就是授权失败。
拿着东西的这个人就是『授权者』（Authorization Server）或者是授权服务，你就是客户端(Client)。
如果持有者天然的认识你，自然直接把东西给你了。给你的这个过程就是授权成功了。
## Resource Owner Password Credentials Grant
继续上面的例子，如果是来的时候是蒙面来的，找持有者要东西，他看不出来你是谁，自然东西就不能随便给你。结果持有者就说：『天王盖地虎』，你一听对上了：『宝塔镇河妖』。持有者一听能对上暗号自然也就把东西给你。
这里的暗号其实就是账号密码。Client请求时直接把用户名和密码发过去，Server 收到并验证成功了username 和 password 即可授权成功。
这个模式有2个前提：
- 账号、密码是两者提前约定好的；
- 账号、密码第三者不知道并且有足够的保护措施他人无法得到；
在互联网这么复杂的环境中其实第二点很难做到，这就限制了这种授权方式的应用。
常见的应用场景就是企业内部或者合作伙伴之间，比如自家研发的不同应用之间的授权可以直接使用这种方式，请求都是服务器和服务器之间的也就不担心账号和密码被访问者劫取了。

## Implicit Grant
这个名字虽然是针对下一个授权方式（）而言的，但是我们为了理解的方便还是继续深化Resource Owner Password Credentials Grant