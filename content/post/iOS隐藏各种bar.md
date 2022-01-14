---
title: "iOS隐藏各种bar"
date: 2013-04-17
draft: false
tags: [navigationController,tabBarController,setStatusBarHidden]
categories: [iOS]
---

隐藏顶部状态栏



[[UIApplication sharedApplication] setStatusBarHidden:YES withAnimation:UIStatusBarAnimationNone];
隐藏Nav




self.navigationController.navigationBar.translucent =YES;



[self.navigationController setNavigationBarHidden:YES animated:NO];


隐藏TabBar


在初始时使用self.hidesBottomBarWhenPushed=YES;



    self = [super init];

    if (self) {

        self.hidesBottomBarWhenPushed=YES;

    }

    return self;


 
- - -
 