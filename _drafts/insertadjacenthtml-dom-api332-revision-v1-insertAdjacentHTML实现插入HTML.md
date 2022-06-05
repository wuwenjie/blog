---
id: 333
title: insertAdjacentHTML实现插入HTML
date: '2019-08-16T23:11:21+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/332-revision-v1/'
permalink: '/?p=333'
---

语法如下：

```
<pre class="wp-block-preformatted">element.insertAdjacentHTML(position, html);
```

`position`是相对于`element`元素的位置，并且只能是以下的字符串之一：

`beforebegin`在 `element` 元素的前面。

`afterbegin`在 `element 元素的第一个子元素前面。`

`beforeend`在 `element 元素的最后一个子元素后面。`

`afterend`在 `element` 元素的后面。

`html`是字符串被解析成HTML或XML插入到DOM树中。