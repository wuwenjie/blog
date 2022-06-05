---
id: 182
title: '利用HTML Agility Pack + ScrapySharp进行Html解析 抓取数据'
date: '2011-11-11T19:29:28+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=182'
permalink: /2011/11/html-agility-pack-scrapysharp-crawl-pages
categories:
    - 'net&amp;c#'
---

自从 Web 应用程序自 1993 年 W3C 设立以来就开始发展，而且 HTML 也历经了数个版本的演化（1.0 – 2.0 – 3.0 – 3.2 – 4.0 – 4.01），现在也已经成为Web网页或应用程序的最基础，想要学习如何设计 Web 网页或开发 Web 应用程序，这已经是绝对必须要学的东西了，就算是方便的控件（例如 ASP.NET），但 HTML 仍然有学习它的必要性，因此如果不会 HTML，就等于没学过 Web 网页一般。

拜 HTML 与 Web 浏览器蓬勃发展之赐，各式各样的应用都在网络上迅速发展，举凡电子商务、企业门户、在线下单、企业间协同应用等，乃至于社交、个性化、Web 2.0 等商务与组织运用等能力，而在信息爆炸的时代，很多信息整合的应用也随之出炉，而这些信息整合的应用程序都会连接到不同的网站下载其信息，并且在重重的 HTML 中剖析出想要的数据（例如每股价格、涨跌幅、成交量等）。

但是 HTML 本身并不是一个结构严谨的语言，它允许卷标（tag）可以在不 close 的情况下继续使用。这也是因为浏览器设计的高容错性（Fault Tolerance）所致，如此一来，想要依照规则来剖析 HTML 文件几乎变得不可能，而且对方的网站的 HTML 结构也可能会随时变化，在这种情况下，剖析 HTML 变得非常辛苦，虽然 W3C 有另外推展 XHTML（遵守 XML 严谨格式的 HTML），但使用它来设计网页的案例仍为少数，大多数的网站仍然是使用 HTML。因此我们会需要一个工具，能够有方法快速的解析 HTML 以取出我们需要的数据。

大家都知道，HTML 本身其实只是一个 HTML 标记的字符串而已，因此一般说到要解析 HTML，第一个会想到的大概就是字符串比对（string comparison），自己针对 HTML 的结构写一个 pattern，然后由函式去做逐一的比对，例如：

\[C#\]

1\. **string** pattern = “&lt;td id=’stockPrice’&gt;”;

2\. html.IndexOf(pattern);

不过传统的字符串比对效能太差，也没有一个规则性，因而才发展出正则表达式（Regular Expression）技术，例如下列这样的语法：

\[Regular Expression\]

1\. &lt;/?\\w+((\\s+\\w+(\\s\*=\\s\*(?:”.\*?”|’.\*?’|\[^'”&gt;\\s\]+))?)+\\s\*|\\s\*)/?&gt;

但 Regular Expression 的学习曲线很高，若要使用它来解析 HTML，并且再加以定制化（Customization）的话，对于一般开发人员来说，实在没有什么亲和力。

HTML 还有一个特色，就是它是具层性（Hierarchy）的，因此浏览器在解译它的时候都会以文件树（document tree）的方式，再用递归（recursive）的方法来处理它，但 Regular Expression 没有支持层级性的剖析，而最接近阶层剖析又好用的工具，莫过于 XML Parser 了，它的 DOM 以及 XPath 的特性，都可以让解析 XML 的工作变得轻松，然而 XML Parser 无法读取一般的 HTML（XHTML 可以），因为一般的 HTML 是结构松散的类型，XML Parser 会在读入时检查语法结构是否完整（也就是 Well-known 的结构），若读入的是结构松散的内容的话会掷出例外讯息，因此无法直接使用 XML Parser 来辅助。

[HTML Agility Pack](http://htmlagilitypack.codeplex.com/) 是由法国的一位软件架构师 Simon Mourier 所开发，并且由 DarthObiwan 以及 Jessynoo 辅助开发出来的一个软件工具，它可以让剖析松散格式 HTML 的工作就像剖析 XML 一样简单，它也有类似于 System.Xml 命名空间中的 XML DOM 的许多类别，除了可以使用阶层的方式存取 HTML 以外，它也支持使用 XPath 的方式来搜寻 HTML，这会较以往使用文字比对或是 Regular Expression 的比对方式来得更明确。

若要使用 HTML Agility Pack 组件，可先上 [Codeplex](http://htmlagilitypack.codeplex.com/) 的 HTML Agility Pack 网站下载二进制文件（同时也提供源代码、说明文件以及 HAP Explorer 工具程序可下载），并解压缩后，在项目加入对 HtmlAgilityPack.dll 的引用。

Html Agility Pack 源码中的类大概有28个左右，其实不算一个很复杂的类库，但它的功能确不弱，为解析DOM已经提供了足够强大的功能支持，可以跟jQuery操作DOM媲美：）Html Agility Pack最常用的基础类其实不多，对解析DOM来说，就只有HtmlDocument和HtmlNode这两个常用的类，还有一个 HtmlNodeCollection集合类。

HTML Agility Pack的操作起来还是很麻烦，下面我们要介绍的这个组件是ScrapySharp，他在2个方面针对Html Agility Pack进行了包装，使得解析Html页面不再痛苦，幸福指数直线上升到90分哈。

ScapySharp有了一个真实的浏览器包装类（处理Reference，Cookie等），另外一个就是使用类似于jQuery一样的Css选择器和Linq语法。让我们使用起来非常的爽。它的代码放在 <https://bitbucket.org/rflechner/scrapysharp>。也可以通过Nuget添加

<figure class="wp-block-image">[![image](https://images.cnblogs.com/cnblogs_com/shanyou/201205/20120527224050477.png)](http://images.cnblogs.com/cnblogs_com/shanyou/201205/201205272240456578.png)</figure>下面我们来看一段解析博客园的博客文章的代码：

using System;   
using System.Collections.Generic;   
using System.Linq;   
using System.Text;   
using HtmlAgilityPack;   
using ScrapySharp.Extensions;   
using ScrapySharp.Network;

namespace HTMLAgilityDemo   
{   
 class Program   
 {   
 static void Main(string\[\] args)   
 {   
 var uri = new Uri(“[http://www.cnblogs.com/shanyou/archive/2012/05/20/2509435.html”);](http://www.cnblogs.com/shanyou/archive/2012/05/20/2509435.html%22);)   
 var browser1 = new ScrapingBrowser();   
 var html1 = browser1.DownloadString(uri);   
 var htmlDocument = new HtmlDocument();   
 htmlDocument.LoadHtml(html1);   
 var html = htmlDocument.DocumentNode;

 var title = html.CssSelect(“title”);   
 foreach (var htmlNode in title)   
 {   
 Console.WriteLine(htmlNode.InnerHtml);   
 }   
 var divs = html.CssSelect(“div.postBody”);

 foreach (var htmlNode in divs)   
 {   
 Console.WriteLine(htmlNode.InnerHtml);   
 }

 divs = html.CssSelect(“#cnblogs\_post\_body”);   
 foreach (var htmlNode in divs)   
 {   
 Console.WriteLine(htmlNode.InnerHtml);   
 }   
 }   
 }   
}

Basic examples of CssSelect usages:

var divs = html.CssSelect(“div”); //all div elements

var nodes = html.CssSelect(“div.content”); //all div elements with css class ‘content’

var nodes = html.CssSelect(“div.widget.monthlist”); //all div elements with the both css class

var nodes = html.CssSelect(“#postPaging”); //all HTML elements with the id postPaging

var nodes = html.CssSelect(“div#postPaging.testClass”); // all HTML elements with the id postPaging and css class testClass

var nodes = html.CssSelect(“div.content &gt; p.para”); //p elements who are direct children of div elements with css class ‘content’

var nodes = html.CssSelect(“input\[type=text\].login”); // textbox with css class login

We can also select ancestors of elements:

var nodes = html.CssSelect(“p.para”).CssSelectAncestors(“div.content &gt; div.widget”);