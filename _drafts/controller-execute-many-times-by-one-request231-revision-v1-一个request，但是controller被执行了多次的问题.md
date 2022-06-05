---
id: 232
title: 一个request，但是controller被执行了多次的问题
date: '2019-08-16T18:01:02+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/231-revision-v1/'
permalink: '/?p=232'
---

这两天竟然被一个bug整疯了，因为项目调试的时候，通过 sql profiler 查看，竟然执行了多次的相同 查询语句， 然后发现原来 一次请求时候，竟然多次进去controller，多次访问路由。一直找不到原因，怎么会多次访问呢？后来又采用了 抓包 fiddler工具发现了有一个.png的请求进入了我那个路由，然而这个文件并不存在，所以进入了我的路由，然后才恍然大悟页面上面有这个

 &lt;img alt=”” src=”logo.png” class=”logo”&gt;

然后路由刚好匹配我的

 public ActionResult Index(string id = “”)  
 {  
 return View();  
 }

导致了一次访问这个页面，多次请求这个路由。当文件不存在的时候，会通过路由，因为我的图片路径写错了

<http://stackoverflow.com/questions/7333343/shouldnt-mvc-ignore-images-by-default>

<http://forums.asp.net/t/1536510.aspx/1?how+to+do+not+route+images+css+js+etc>

因为在开发的时候难免会复制一下美工写好的html，而且他们写的都是相对路径，为了避免调试出现问题，狠一点，开发的时候把所有扩展名的文件都过滤掉，不让经过路由。

在global文件中 忽略有扩展名的路由，如下：

 public static void RegisterRoutes(RouteCollection routes)  
 {  
 routes.IgnoreRoute(“{resource}.axd/{\*pathInfo}”);  
 routes.Ignore(“{\*allfiles}”, new { allfiles = @”.\*\\.(.\*)(/.\*)?” });