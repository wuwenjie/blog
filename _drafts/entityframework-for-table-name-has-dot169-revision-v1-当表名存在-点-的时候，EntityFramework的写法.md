---
id: 170
title: '当表名存在 点 的时候，EntityFramework的写法'
date: '2019-08-15T18:48:18+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/169-revision-v1/'
permalink: '/?p=170'
---

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="csharp" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
            modelBuilder.Entity<TestDB_Category>()
                .ToTable("TestDB.Category","dbo");
}
```