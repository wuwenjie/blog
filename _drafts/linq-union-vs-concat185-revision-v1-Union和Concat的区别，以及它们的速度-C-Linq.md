---
id: 186
title: 'Union和Concat的区别，以及它们的速度 (C# Linq)'
date: '2019-08-15T22:22:20+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/185-revision-v1/'
permalink: '/?p=186'
---

Union 会去重复后合并。而Contact不去重直接合并。 所以Contact当然比较快了。所以如果你不用去重的时候，尽量用Contact做合并吧。

在SQL里面：Union uses ‘UNION’ while Concat uses ‘UNION ALL’.

Union removes duplicates.Concat does not.

So, they produce different results if the sources either contain any items in common, or have any internal duplicates.

If you can guarantee there are no duplicates, or if there are few and you don’t care about having them in your output, Concat will be faster since there’s no need to test each value against what has already been yielded.

However, if there are many duplicates and you don’t need them, the extra processing in Union to remove the dupes may be offset by the savings in your code that consumes the results.