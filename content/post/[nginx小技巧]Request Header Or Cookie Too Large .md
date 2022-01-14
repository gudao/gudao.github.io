---
title: "[nginx小技巧]Request Header Or Cookie Too Large "
date: 2012-10-12
draft: false
tags: [nginx小技巧,nginx]
categories: [Nginx]
---

方法：

在http中添加

        client_header_buffer_size 32k;        large_client_header_buffers 4 32k;


 
- - -
 