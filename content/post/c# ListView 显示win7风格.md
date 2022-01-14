---
title: "c# ListView 显示win7风格"
date: 2012-06-15
draft: false
tags: [ListView,win7风格]
categories: [.Net]
---

如果你用winform 开发应用，而且没有使用第三方的美化控件还想让应用漂亮一点， 那就使用下面的方法吧

先声明一下这个：





[DllImport(&#34;uxtheme.dll&#34;, CharSet = CharSet.Unicode, EntryPoint = &#34;SetWindowTheme&#34;)] 
 private static extern Int32 SetWindowTheme(IntPtr hwnd, string subAppName, string subIdList); 



然后在form启动时调用：  


SetWindowTheme(listView1.Handle, &#34;Explorer&#34;, null); 

OK，看看效果吧！ 


- 


 
- - -
 