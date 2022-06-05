---
id: 881
title: 'vnode.context[binding.expression] = false'
date: '2019-11-09T15:39:45+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/11/879-revision-v1'
permalink: '/?p=881'
---

 vnode.context\[binding.expression\] = false

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""> function trigger(el, type) {
    // copy from vue.js line 8578
    var e = document.createEvent("HTMLEvents");
    e.initEvent(type, true, true);
    el.dispatchEvent(e);
  }
```

:key component