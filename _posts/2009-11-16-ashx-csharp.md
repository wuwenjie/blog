---
id: 197
title: ashx输出文字，执行JavaScript，输出图片等
date: '2009-11-16T12:41:08+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=197'
permalink: /2009/11/ashx-csharp
categories:
    - 'net&amp;c#'
---

一提到Ashx文件，我们就会想到http handler以及图片加载（在之前我们一般使用ASPX或者Webservice去做），一般做法如下：

Handler.ashx：  
&lt;%@ WebHandler Language=”C#” Class=”Handler” %&gt;  
using System;  
using System.IO;  
using System.Web;  
public class Handler : IHttpHandler {

public bool IsReusable {  
 get {  
 return true;  
 }  
}  
public void ProcessRequest (HttpContext context) {  
 context.Response.ContentType = “image/jpeg”;  
 context.Response.Cache.SetCacheability(HttpCacheability.Public);  
 context.Response.BufferOutput = false;  
 PhotoSize size;  
 switch (context.Request.QueryString\[“Size”\]) {  
 case “S”:  
 size = PhotoSize.Small;  
 break;  
 case “M”:  
 size = PhotoSize.Medium;  
 break;  
 case “L”:  
 size = PhotoSize.Large;  
 break;  
 default:  
 size = PhotoSize.Original;  
 break;  
 }   
 Int32 id = -1;  
 Stream stream = null;  
 if (context.Request.QueryString\[“PhotoID”\] != null &amp;&amp; context.Request.QueryString\[“PhotoID”\] != “”) {  
 id = Convert.ToInt32(context.Request.QueryString\[“PhotoID”\]);  
 stream = PhotoManager.GetPhoto(id, size);  
 } else {  
 id = Convert.ToInt32(context.Request.QueryString\[“AlbumID”\]);  
 stream = PhotoManager.GetFirstPhoto(id, size);  
 }  
 if (stream == null) stream = PhotoManager.GetPhoto(size);  
 const int buffersize = 1024 \* 16;  
 byte\[\] buffer = new byte\[buffersize\];  
 int count = stream.Read(buffer, 0, buffersize);  
 while (count &gt; 0) {  
 context.Response.OutputStream.Write(buffer, 0, count);  
 count = stream.Read(buffer, 0, buffersize);  
 }  
}  
}

\*.aspx:  
&lt;img src=”myHttpHander.ashx?id=123″ width=”20″ height=”20″ /&gt;

**还可以嵌入文字：  
Handler.ashx：  
&lt;%@ WebHandler Language=”C#” Class=”TestHandler” %&gt;  
using System;  
using System.Web;  
public class TestHandler : IHttpHandler {  
   
 public void ProcessRequest (HttpContext context) {  
 context.Response.ContentType = “text/plain”;  
 context.Response.Write(“document.write(\\”Hello World\\”);”);  
 }**

 **public bool IsReusable {  
 get {  
 return false;  
 }  
 }  
}  
\*.aspx:  
&lt;script type=”text/javascript” src=”TestHandler.ashx” /&gt;**

**我们变通以下，发现其实除了可以输出图片以外，还可以输出文字：  
Handler.ashx：  
&lt;%@ WebHandler Language=”C#” Class=”Handler” %&gt;  
using System;  
using System.Web;  
public class Handler : IHttpHandler {  
   
 public void ProcessRequest (HttpContext context) {  
 context.Response.ContentType = “text/plain”;  
 context.Response.Write(“alert(‘hi’)”);  
 }  
  
 public bool IsReusable {  
 get {  
 return false;  
 }  
 }  
}**

**\*.aspx:  
弹出alert  
&lt;script src=”Handler.ashx”&gt;&lt;/script&gt;  
也可以把.ashx当成css文件  
&lt;link href=”css/Handler.ashx” rel=”stylesheet” type=”text/css”&gt;  
xml文件  
orderDoc.load(“Handler.ashx”);**

当你希望从ashx或HttpHandler里访问你的Session时,你必须实现IReadOnlySessionState接口.

代码:

using System;  
using System.Web;  
using System.Web.SessionState;

public class DownloadHandler : IHttpHandler, IReadOnlySessionState  
{  
 public bool IsReusable { get { return true; } }  
   
 public void ProcessRequest(HttpContext ctx)  
 {  
 ctx.Response.Write(ctx.Session\[“fred”\]);  
 }  
}

其实，学习的思路不应该这样，以上除了图片外，我们都用偏了，为什么用偏了呢，因为软件以简单、实用为主，我们只是把以上纯粹看成可一项技术而没有把它放到软件的地位去考虑：）  
具体的用途，大家可以参考Rewirte.dll (这个dll，可以使服务器支持伪静态的)！  
反编译，看看人家做的：）