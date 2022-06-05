---
id: 220
title: 't4-editor使用方法 Visual T4'
date: '2019-08-16T17:31:24+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/219-revision-v1/'
permalink: '/?p=220'
---

<http://visualstudiogallery.msdn.microsoft.com/40a887aa-f3be-40ec-a85d-37044b239591>

可以这里下载，也可以在 vs的 工具——扩展管理器 里面搜索 安装即可。

引入一些常用的命名空间并使用的方法如下：

&lt;#@ template debug=”false” hostspecific=”false” language=”C#” #&gt;  
&lt;#@ output extension=”.txt” #&gt;  
&lt;#@ assembly name=”System.Core” #&gt;  
&lt;#@ assembly name=”System.Xml” #&gt;  
&lt;#@ assembly name=”System.Xml.Linq” #&gt;  
&lt;#@ import namespace=”System” #&gt;  
&lt;#@ import namespace=”System.Text” #&gt;  
&lt;#@ import namespace=”System.IO” #&gt;  
&lt;#@ import namespace=”System.Linq” #&gt;  
&lt;#@ import namespace=”System.Xml.Linq” #&gt;  
&lt;#  
 var a = “sdfsdf”.Select(it =&gt; it);  
 foreach (var b in a)  
 {  
\#&gt;  
 &lt;#= b #&gt;   
&lt;#  
}  
\#&gt;

载入自定义dll，如

&lt;#@ Assembly Name=”E:\\CodeTest\\TestT4\\My\\bin\\Debug\\My.dll” #&gt;

&lt;#@ Import Namespace=”My” #&gt;

**一个模板生成多个文件，范例**（详见：http://www.olegsych.com/2008/03/how-to-generate-multiple-outputs-from-single-t4-template/）

&lt;#@ template debug=”False” hostspecific=”True” language=”C#” #&gt;  
&lt;#@ assembly name=”System.Core” #&gt;  
&lt;#@ assembly name=”System.Xml” #&gt;  
&lt;#@ assembly name=”System.Xml.Linq” #&gt;  
&lt;#@ assembly name=”System.Web” #&gt;  
&lt;#@ Assembly Name=”System.Web.Mvc” #&gt;  
&lt;#@ import namespace=”System” #&gt;  
&lt;#@ import namespace=”System.Text” #&gt;  
&lt;#@ import namespace=”System.IO” #&gt;  
&lt;#@ import namespace=”System.Linq” #&gt;  
&lt;#@ import namespace=”System.Xml.Linq” #&gt;  
&lt;#  
 HelloWorld(12);  
 SaveOutput(“test1.txt”);  
 HelloWorld(13);  
 SaveOutput(“test2.txt”);  
 HelloWorld(14);  
 SaveOutput(“test3.txt”);  
 #&gt;  
  
  
&lt;#+  
  
 void HelloWorld(int i)  
 {  
 Write(“Hello World” + i);  
 #&gt;  
 This is file &lt;#= i #&gt;  
&lt;#+  
 }  
\#&gt;  
  
&lt;#+  
 void SaveOutput(string outputFileName)  
 {  
 string templateDirectory = Path.GetDirectoryName(Host.TemplateFile);  
 string outputFilePath = Path.Combine(templateDirectory, outputFileName);  
 File.WriteAllText(outputFilePath, this.GenerationEnvironment.ToString());  
 this.GenerationEnvironment.Remove(0, this.GenerationEnvironment.Length);  
 }  
\#&gt;  
<http://www.olegsych.com/2008/03/how-to-generate-multiple-outputs-from-single-t4-template/>

 中文MSDN这里：<http://msdn.microsoft.com/zh-cn/library/dd820620>

Tutorials:[*Creating your first code generator*](http://www.olegsych.com/2008/09/t4-tutorial-creatating-your-first-code-generator/)[*Troubleshooting code generation errors*](http://www.olegsych.com/2008/09/t4-tutorial-troubleshooting-code-generation-errors/)[*Debugging code generation files*](http://www.olegsych.com/2008/09/t4-tutorial-debugging-code-generation-files/)[*Creating reusable code generation templates*](http://www.olegsych.com/2008/09/t4-tutorial-creating-reusable-code-generation-templates/)[*Creating complex code generators*](http://www.olegsych.com/2008/09/t4-tutorial-creating-complex-code-generators/)[*Reusing code generators on multiple projects*](http://www.olegsych.com/2008/10/t4-tutorial-reusing-code-generators-on-multiple-projects/)

Here’s [some of the links from Oleg’s blog](http://www.olegsych.com/2007/12/text-template-transformation-toolkit/).[*How to create a simple T4 template*](http://www.olegsych.com/2007/12/how-to-create-a-simple-t4-template/)[*How to use T4 to generate .config files*](http://www.olegsych.com/2007/12/how-to-use-t4-to-generate-config-files/)[*How to use T4 to generate Decorator classes*](http://www.olegsych.com/2007/12/how-to-use-t4-to-generate-decorator/)[*How to use T4 to generate CRUD stored procedures*](http://www.olegsych.com/2008/01/how-to-use-t4-to-generate-crud-stored-procedures/)[*How to use T4 to generate strongly-typed navigation class in ASP.NET*](http://www.olegsych.com/2008/02/how-to-use-t4-to-generate-strongly-typed-navigation-class-in-aspnet/) *(by Kirill Chilingarashvili)* [*How to use T4 to generate strongly-typed AzMan wrapper*](http://www.olegsych.com/2008/03/how-to-use-t4-to-generate-strongly-typed-azman-wrapper/)[*How to generate multiple outputs from single T4 template*](http://www.olegsych.com/2008/03/how-to-generate-multiple-outputs-from-single-t4-template/)[*T4 template for generating ADO.NET Entity Framework Stored Procedures*](http://www.olegsych.com/2008/05/t4-template-for-generating-adonet-entity-framework-stored-procedures/) *(by David DeWinter)* [*T4 script for generating ADO.NET Entity Framework Views* ](http://blogs.msdn.com/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx)*(by ADO.NET team)* [*T4 template for generating LINQ to SQL Data Context*](http://damieng.com/blog/2008/07/23/linq-to-sql-t4-template-reloaded) *(by Damien Guard)* [*T4 template for generating WiX source files*](http://blog.newagesolution.net/2008/06/how-to-use-msbuild-and-wix-to-msi.html) *(by New Age Solutions)* [*T4 template for generating SQL view from C# enumeration*](http://www.olegsych.com/2008/07/t4-template-for-generating-sql-view-from-csharp-enumeration/)[*MSBuild task for transforming T4 templates*](http://geekswithblogs.net/EltonStoneman/archive/2008/07/25/an-msbuild-task-to-%20-t4-templates.aspx) *(by Elton Stoneman)* [*T4 template for generating state machines in C#*](http://aabs.wordpress.com/2008/06/26/state-machines-in-c-30-using-t4-templates/) *(by Andrew Matthews)*

Oleg also has a CodePlex project called [T4 Toolbox](http://www.codeplex.com/t4toolbox) that is a library of T4 templates that get added to File | New Item.

Also, check out [Damien Guard’s T4 templates that are a wholesale replacement of code that LINQ to SQL generates](http://damieng.com/blog/2008/09/14/linq-to-sql-template-for-visual-studio-2008).

===其他的学习，可以到这个hanselman的博客。

<http://www.hanselman.com/blog/T4TextTemplateTransformationToolkitCodeGenerationBestKeptVisualStudioSecret.aspx>