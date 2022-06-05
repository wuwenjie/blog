---
id: 222
title: 写一个Foreach帮助类，在razor中使用
date: '2019-08-16T17:34:19+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/221-revision-v1/'
permalink: '/?p=222'
---

esterday, during my [ASP.NET MVC 3 talk at Mix 11](http://channel9.msdn.com/events/MIX/MIX11/FRM03), I wrote a useful helper method demonstrating an advanced feature of Razor, [Razor Templated Delegates](https://haacked.com/archive/2011/02/27/templated-razor-delegates.aspx).

There are many situations where I want to quickly iterate through a bunch of items in a view, and I prefer using the `foreach` statement. But sometimes, I need to also know the current index. So I wrote an extension method to `IEnumerable<T>` that accepts Razor syntax as an argument and calls that template for each item in the enumeration.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">public static class HaackHelpers {
  public static HelperResult Each<TItem>(
      this IEnumerable<TItem> items, 
      Func<IndexedItem<TItem>, 
      HelperResult> template) {
    return new HelperResult(writer => {
      int index = 0;

      foreach (var item in items) {
        var result = template(new IndexedItem<TItem>(index++, item));
        result.WriteTo(writer);
      }
    });
  }
}
```

This method calls the template for each item in the enumeration, but instead of passing in the item itself, we wrap it in a new class,`IndexedItem<T>`.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">public class IndexedItem<TModel> {
  public IndexedItem(int index, TModel item) {
    Index = index;
    Item = item;
  }

  public int Index { get; private set; }
  public TModel Item { get; private set; }
}
```

And here’s an example of its usage within a view. Notice that we pass in Razor markup as an argument to the method which gets called for each item. We have access to the direct item and the current index.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
@model IEnumerable<Question>

<ol>
@Model.Each(@<li>Item @item.Index of @(Model.Count() - 1): @item.Item.Title</li>)
</ol>
```

If you want to try it out, I put the code in a package in my personal NuGet feed for my code samples. Just connect NuGet to<http://nuget.haacked.com/nuget/> and `Install-Package RazorForEach`. The package installs this code as source files in *App\_Code*.

转 <https://haacked.com/archive/2011/04/14/a-better-razor-foreach-loop.aspx/>