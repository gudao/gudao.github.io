---
title: "教你如何计算iPhone的屏幕高度"
date: 2013-03-14
draft: false
tags: [iOS,控件高度,视图高度]
categories: [iOS]
---

![](/Content/attached/image/20130314/m40lcjlb_1bu.jpg) 


iPhone的各版本都是以点为计数单位的，所以给开发者带来了不少方便。但是你知道怎么计算你的应用的布局吗？

下面先列出几个常用的默认控件的大小，以便大家调整布局，先看张全局图你可能会豁然开朗。

![](/Content/attached/image/20130314/m40lcjlb_1bu.jpg) 

下面列出各控件的详细尺寸：



<table><tbody><tr valign="TOP"><td>
**控件名称** 
</td><td>
**尺寸(单位：点pts)** 
</td></tr><tr valign="TOP"><td>
Window(包括status bar  )
</td><td>
320*480
</td></tr><tr valign="TOP"><td>
Navigation Bar
</td><td>
高 44
</td></tr><tr valign="TOP"><td>
Nav Bar 、ToolBar中的图片    
</td><td>
20*20
</td></tr><tr valign="TOP"><td>
Tab Bar
</td><td>
高49
</td></tr><tr valign="TOP"><td>
Tab Bar Icon
</td><td>
30*30
</td></tr><tr valign="TOP"><td>
TextField
</td><td>
高31
</td></tr><tr valign="TOP"><td>
全键盘
</td><td>
高140
</td></tr><tr valign="TOP"><td>
非全键盘
</td><td>
高216
</td></tr><tr valign="TOP"><td>

</td><td>

</td></tr></tbody></table>



如果计算一个包含NavBar 又包含Tab Bar的视图的高度，可以自行计算：如480-20-44-49=367，

（全部-状态栏-顶部-底部）

你明白了吗？
 
- - -
 