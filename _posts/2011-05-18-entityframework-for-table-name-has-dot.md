---
id: 169
title: '当表名存在 点 的时候，EntityFramework的写法'
date: '2011-05-18T18:48:18+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=169'
permalink: /2011/05/entityframework-for-table-name-has-dot
categories:
    - 'net&amp;c#'
---

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="csharp" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
            modelBuilder.Entity<TestDB_Category>()
                .ToTable("TestDB.Category","dbo");
}
```