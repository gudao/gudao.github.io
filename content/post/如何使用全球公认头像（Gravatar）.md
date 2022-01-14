---
title: "如何使用全球公认头像（Gravatar）"
date: 2013-07-02
draft: false
tags: [Gravatar,全球公认头像]
categories: [建站技巧]
---

![](/Content/upload/Img20130702/s_roaxzn4t_53r.png) 
**一、先说，Gravatar是什么？** 

    截一个[FROYO&#39;s Blog](http://www.izhuyue.com)的评论图来说明一下![](/Content/attached/image/20130702/ajvwrwj3_ebo.png) 

看到上面每个用户的头像你是不是觉得用户在评论时已经在该网站注册了用户并且上传了头像？非也！假如读者为了在你的博客说句话，就要注册一个用户并且上传一个自己的头像，估计你的博客这辈子也不会有评论（除非你很牛或者这个读者很变态）。[Gravatar](http://cn.gravatar.com/)帮我们解决了这个问题，您只需一次绑定一个邮箱就可以“四处留情”了（评论时输入绑定的邮箱，自定义头像自动显示出来）！

**二、再说，怎么拥有自己的个性头像？** 

    拥有全球公认的头像只需去[http://cn.gravatar.com/](http://cn.gravatar.com/)点击右上角的Sign In登录或注册一个用户（需要翻墙），登陆进去，点击“add a new image”；然后选择上传图片，一般都是从电脑中上传。上传并裁剪，效果可以在右侧看到。头像上传后，选G级。等待审核，可能需要几分钟短暂审核一下，一般选择了G，而你的图片没什么特别的，很快就通过。一般遇上慢的情况也就10分钟左右。想试试是否成功可以在我这里评论一下看看效果。

**三、最后说，我的博客怎么支持？** 

    想要在自己的博客中支持[Gravatar](http://cn.gravatar.com/)，只需要将用户评论时留下的邮箱地址Hash一下拼接成下面的格式即可。

    


<pre>http://www.gravatar.com/avatar/HASH</pre>

其中HASH代表你hash邮箱后得到的字符串，如果你用的是.net就可以这么写





<pre>FormsAuthentication.HashPasswordForStoringInConfigFile(Email, &#34;MD5&#34;).ToLower();</pre>

注意一定要是小写的字符串。


不用怕用户的邮箱没有绑定头像，如果没有绑定[Gravatar](http://cn.gravatar.com/)自动返回一个默认的头像，如果你想自定义默认的头像，只需在后边加一个参数d=默认图片地址，格式如下：




<pre>http://www.gravatar.com/avatar/00000000000000000000000000000000?d=http%3A%2F%2Fexample.com%2Fimages%2Favatar.jpg</pre>

其中参数d的值必须URL-encode编码。


同上s参数可以指定图片的大小

如：http://www.gravatar.com/avatar/00000000000000000000000000000000?s=400，返回400*400的图片。




 
- - -
 