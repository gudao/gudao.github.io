---
title: "Nginx模块修改(ngx_http_flv_module)"
date: 2012-08-31
draft: false
tags: [nginx,flv模块,flv加密解密]
categories: [Nginx]
---

![](/Content/upload/Img20120831/aw5i1dna.01h.gif) 
本文实现内容：增加flv模块参数，跳过片头内容。flv模块默认只有start一个参数，添加一个配置参数iskip来指定是否跳过片头。

如果你对nginx的模块开发还不了解，请移步[Nginx模块开发入门](http://www.codinglabs.org/html/intro-of-nginx-module-development.html)张洋同学的博客介绍的很是详细，下面只简单引用我认为比较重要的部分，以便解释本次修改的过程。要修改nginx模块，了解模块的工作原理是必备的，结合下图可以看出模块的主要逻辑是在Handler中处理的。![](/Content/attached/image/20120831/n2jl1boy_qrc.png) 

于是我们直接找到flv模块（位于 源码根目录/src/http/modules/ngx_http_flv_module.c文件）的ngx_http_flv_handler，下面修改正式开始

**一、添加自定义参数** 

找到已有参数start出现的位置




<pre>...

if (ngx_http_arg(r, (u_char *) &#34;start&#34;, 5, &amp;value) == NGX_OK) {
            start = ngx_atoof(value.data, value.len);
...

</pre>

代码的意思为获取参数start的值，照葫芦画瓢我们可以添加自定义的参数，再次上方我们加入以下代码




<pre> if (ngx_http_arg(r, (u_char *) &#34;iskip&#34;, 5, &amp;value) == NGX_OK) {

            iskip = ngx_atoof(value.data, value.len);

            if (iskip== NGX_ERROR) {
                iskip = 0;
            }
        }</pre>

凑巧的是我们加入的参数“iskip”也刚好是5个字符，所以这里ngx_http_arg的第三个参数也是5，如果参数的key的字符数为其他数字其自行更换。同时还要注意参数的顺序要一致。


**二、添加跳过片头逻辑** 

先看代码


<pre>.... 
 if (r-&gt;args.len) {

	if (ngx_http_arg(r, (u_char *) &#34;start&#34;, 5, &amp;value) == NGX_OK) {

            start = ngx_atoof(value.data, value.len);

            if (start == NGX_ERROR || start &gt;= len) {
                start = 0;
            }
//看这里
            if (start) {
                len = sizeof(ngx_flv_header) - 1 + len - start;
                i = 0;
            }
        }
	
    }
....
....
//还有这里
    if (i == 0) {
        b = ngx_pcalloc(r-&gt;pool, sizeof(ngx_buf_t));
        if (b == NULL) {
            return NGX_HTTP_INTERNAL_SERVER_ERROR;
        }

        b-&gt;pos = ngx_flv_header;
        b-&gt;last = ngx_flv_header + sizeof(ngx_flv_header) - 1;
        b-&gt;memory = 1;

        out[0].buf = b;
        out[0].next = &amp;out[1];
    }
...</pre>

通过以上代码可知只有start参数不为0时，即不从片头开始播放时，程序要先向客户端发送一段flv的header，所以我们不用考虑自己动手加header的情况了，直接修改文件读取起始位置和文件大小就可以了，所以就有了下面这段代码





<pre>          ...
          if (start) {
                len = sizeof(ngx_flv_header) - 1 + len - start;
                i = 0;
            }

	    if(iskip){
		start=start+4000;//指定跳过的字节数
		len=len-4000;
	    }
...</pre>

所有修改到此为止，这里是修改后的[ngx_http_flv_module.c](/Content/attached/file/20120831/c3ms5oyi_igt.rar)文件。

总结：此次修改flv模块可以实现简单flv文件加密，即先通过flv编辑软件在flv文件的头部加入一段自定内容，在通过nginx播放时，自动跳过自定义内容就可以解读正确的内容，而通过其他播放器直接解读flv文件是不能识别为正常的flv文件的。更加安全的扩展可以在不同位置加入自定义内容，并在解密时验证自定义内容，本文就不再做更多的描述，如果您对flv加密有更好的方法，欢迎讨论




 
- - -
 