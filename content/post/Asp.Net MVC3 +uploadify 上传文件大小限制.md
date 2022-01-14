---
title: "Asp.Net MVC3 +uploadify 上传文件大小限制"
date: 2012-04-26
draft: false
tags: [uploadify,上传文件大小限制]
categories: [.Net]
---

真他妈一下午就光在弄这个了，uploadify总是显示Http Error用

&#39;onError&#39;: function (event,queueId,fileObj,errorObj) {                alert(errorObj.info);            }

捕获了一下，显示404错误。

最后找到原因是IIS7把文件大小截获了。

解决方法： 

 

1、打开IIS管理器，找到Default Web Site。先进行停止。 

2、在IIS中双击“请求筛选”打开。 

3、点击右边的“编辑功能设置”，打开“编辑请求筛选设置”对话框。 

     其中的允许的最大容量长度，默认是”30000000“，30M，将其修改为你所需要的大小即可。 

4、启动IIS. 

或者修改web.config &lt;system.webServer&gt;节点 

&lt;security&gt;            &lt;requestFiltering&gt;&lt;!--500MB--&gt;                &lt;requestLimits maxAllowedContentLength=&#34;500000000&#34; /&gt;            &lt;/requestFiltering&gt;&lt;/security&gt; 

顺便说一下uploadify的限制大小需要配置sizeLimit，如： 

 &#39;sizeLimit&#39;: 500 * 1024 * 1024,   补充：后台接受文件如果包含参数请用&#39;scriptData&#39;: ｛｝ 


 
- - -
 