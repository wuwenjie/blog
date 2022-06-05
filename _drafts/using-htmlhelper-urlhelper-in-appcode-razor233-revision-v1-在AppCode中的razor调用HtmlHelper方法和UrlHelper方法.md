---
id: 234
title: 在AppCode中的razor调用HtmlHelper方法和UrlHelper方法
date: '2019-08-16T18:03:33+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/233-revision-v1/'
permalink: '/?p=234'
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