---
id: 953
title: '无法捕获到async await的错误'
date: '2020-02-04T23:58:56+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2020/02/952-revision-v1'
permalink: '/?p=953'
---

最近在做.net项目中遇到无法捕获到错误的问题，即使在全局的错误捕获中，也依然没有捕获到，直接造成系统奔溃，究其原因是用了async void 的方法，async void是要避免使用的，详情可以看MSDN这篇文章： <https://docs.microsoft.com/zh-CN/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming>

如下代码是错误的：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="csharp" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">public async void Foo()
{
    var x = await DoSomethingAsync();
}

public void DoFoo()
{
    try
    {
       Foo();
    }
    catch (Exception ex)
    {
        // 这里永远不会被捕获到
    }
}
```

根据MSDN文章以下代码才是最佳做法：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="csharp" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// 最重要的是需要捕获错误的方法，要避免async void，改成 async Task
public async Task Foo()
{
    var x = await DoSomethingAsync();
}

public async void DoFoo()
{
    try
    {
        await Foo();
    }
    catch (Exception ex)
    {
         // 这里可捕获到错误
    }
}
//或//
public void DoFoo()
{
    try
    {
        Foo().Wait();
    }
    catch (Exception ex)
   {
        // 这里可捕获到错误
    }
} 
```