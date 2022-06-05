---
id: 233
title: 在AppCode中的razor调用HtmlHelper方法和UrlHelper方法
date: '2011-05-19T18:02:40+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=233'
permalink: /2011/05/using-htmlhelper-urlhelper-in-appcode-razor
categories:
    - 'net&amp;c#'
---

可以写一个帮助类，如下

using System.Web.WebPages;  
using System.Web.Mvc;

 public class Helper  
 {

public static UrlHelper GetUrlHelper()  
 {  
 return ((WebViewPage)WebPageContext.Current.Page).Url;  
 }  
  
public static HtmlHelper GetHtmlHelper()  
{  
 return ((WebViewPage)WebPageContext.Current.Page).Html;  
}

}

<http://stackoverflow.com/questions/4710853/using-mvc-htmlhelper-extensions-from-razor-declarative-views>