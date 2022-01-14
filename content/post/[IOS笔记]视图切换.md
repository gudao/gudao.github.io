---
title: "[IOS笔记]视图切换"
date: 2013-02-18
draft: false
tags: [视图切换,IOS]
categories: [iOS]
---

在IOS应用中多视图必不可少，视图切换的效果自然也很重要，下面就贴上视图切换的代码：

先设置以下内容：



    [UIView beginAnimations:@&#34;view filip&#34; context:nil];//切换标题 

    [UIView setAnimationDuration:1.25];//切换时长 

    [UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];//切换路径 



再设置切换的效果：

[UIView setAnimationTransition:UIViewAnimationTransitionFlipFromLeft forView:self.view cache:YES]; 

最后执行效果： 

 

    [UIView commitAnimations]; 




 
- - -
 