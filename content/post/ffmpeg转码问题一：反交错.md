---
title: "ffmpeg转码问题一：反交错"
date: 2012-08-24
draft: false
tags: [转码,反交错,ffmpeg]
categories: [流媒体]
---

ffmpeg 用x264视频编码，视频画面会出现锯齿（或者叫拉丝），下面两张图对比一下效果：

![](/Content/attached/image/20120824/v3lad5fe_nyc.jpg)（图：未设置反交错）![](/Content/attached/image/20120824/cqkotbjk_dvt.jpg) 

（图：设置反交错）

导致此效果的原因是x264本身不能很好的进行反交错处理，尤其是在要缩放原始视频的时候（比如说用1080i压制720p）。

下面说明如何设置ffmpeg参数来避免画面出现锯齿：

方法一：设置ffmpeg参数 -deinterlace即可，如：




<pre>ffmpeg -i test.mpg -vcodec libx264 -s 1024x768 -b:v 700k -r 25 -deinterlace -acodec libmp3lame -ar 22050 -f flv -y test.flv</pre>

方法二：使用ffmpeg滤镜 -vf yadif，如：





<pre>ffmpeg -i test.mpg -vcodec libx264  -s 1280x768 -b:v 700k -r 25 -vf yadif -acodec libmp3lame -ar 22050 -f flv -y test.flv</pre>

我的测试结果是，两种效果差别不大，但是第一种方法速度要快于第二种方法。此结论只为本人测试，请感兴趣的同学亲自测试！

 
- - -
 