---
id: 327
title: 在Email中的HTML规范
date: '2013-05-12T22:56:36+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=327'
permalink: /2013/05/html-in-email
categories:
    - 'HTML&amp;CSS'
---

**1. Doctype**

目前，兼容性最好的Doctype是XHTML 1.0 Strict，事实上Gmail和Hotmail会删掉你的Doctype，换上这个Doctype。

> &lt;!DOCTYPE html PUBLIC “-//W3C//DTD XHTML 1.0 Transitional//EN” “http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd”&gt;
> 
> &lt;html xmlns=”http://www.w3.org/1999/xhtml”&gt;
> 
>  &lt;head&gt;
> 
>  &lt;meta http-equiv=”Content-Type” content=”text/html; charset=UTF-8″ /&gt;
> 
>  &lt;title&gt;HTML Email编写指南&lt;/title&gt;
> 
>  &lt;meta name=”viewport” content=”width=device-width, initial-scale=1.0″/&gt;
> 
>  &lt;/head&gt;
> 
> &lt;/html&gt;

使用这个Doctype，也就意味着，不能使用HTML5的语法。

**2. 布局**

网页的布局（layout）必须使用表格（table）。首先，放置一个最外层的大表格，用来设置背景。

> &lt;body style=”margin: 0; padding: 0;”&gt;
> 
>  &lt;table border=”1″ cellpadding=”0″ cellspacing=”0″ width=”100%”&gt;
> 
>  &lt;tr&gt;   
>  &lt;td&gt; Hello! &lt;/td&gt;  
>  &lt;/tr&gt;
> 
>  &lt;/table&gt;
> 
> &lt;/body&gt;

表格的 border 属性等于1, 是为了方便开发。正式发布的时候，再把这个属性设为0。

在内层，放置第二个表格。用来展示内容。第二个table的宽度定为600像素，防止超过客户端的显示宽度。

> &lt;table align=”center” border=”1″ cellpadding=”0″ cellspacing=”0″ width=”600″ style=”border-collapse: collapse;”&gt;
> 
>  &lt;tr&gt;  
>  &lt;td&gt; Row 1 &lt;/td&gt;  
>  &lt;/tr&gt;
> 
>  &lt;tr&gt;  
>  &lt;td&gt; Row 2 &lt;/td&gt;  
>  &lt;/tr&gt;
> 
>  &lt;tr&gt;  
>  &lt;td&gt; Row 3 &lt;/td&gt;  
>  &lt;/tr&gt;
> 
> &lt;/table&gt;

邮件内容有几个部分，就设置几行（row）。

**3. 图片**

图片是唯一可以引用的外部资源。其他的外部资源，比如样式表文件、字体文件、视频文件等，一概不能引用。

有些客户端会给图片链接加上边框，要去除边框。

> img {outline:none; text-decoration:none; -ms-interpolation-mode: bicubic;}
> 
>  a img {border:none;}
> 
>  &lt;img border=”0″ style=”display:block;”&gt;

需要注意的是，不少客户端默认不显示图片（比如Gmail），所以要确保即使没有图片，主要内容也能被阅读。

**4. 行内样式**

所有的CSS规则，最好都采用行内样式。因为放置在网页头部的样式，很可能会被客户端删除。客户端对CSS规则的支持情况，请看[这里](http://www.campaignmonitor.com/css/)。

另外，不要采用CSS的简写形式，有些客户端不支持。比如，不要写成下面这样：

> style=”font: 8px/14px Arial, sans-serif;”

如果想表达

> &lt;p style=”margin: 1em 0;”&gt;

要写成下面这样：

> &lt;p style=”margin-top: 1em; margin-bottom: 1em; margin-left: 0; margin-right: 0;”&gt;

**5. W3C校验和测试工具**

要保证最终的代码，能够通过[W3C](http://validator.w3.org/)的校验，因为某些客户端会把不合格属性剥离。还要使用测试工具（[1](http://mailchimp.com/features/inbox-inspector/), [2](https://litmus.com/), [3](http://www.emailonacid.com/)），查看在不同客户端的显示结果。

发送HTML Email的时候，不要忘记MIME类型不能使用

> Content-Type: text/plain;

而要使用

> Content-Type: Multipart/Alternative;

发送工具可以考虑使用 [MailChimp](http://mailchimp.com/) 和 [Campaign Monitor](http://www.campaignmonitor.com/) 。

**6. 模板**

使用别人已经做好的模板，是一个不错的选择（[这里](http://mailchimp.com/features/email-templates/)和[这里](http://www.campaignmonitor.com/templates/all/)），网上还可以搜到[更多](http://www.google.com.hk/search?q=email+template)。

自己开发的话，可以参考[HTML Email Boilerplate](http://htmlemailboilerplate.com/)和[Emailology](http://www.emailology.org/#1)。