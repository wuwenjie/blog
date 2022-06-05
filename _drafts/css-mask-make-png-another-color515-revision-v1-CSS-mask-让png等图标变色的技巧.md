---
id: 517
title: 'CSS mask 让png等图标变色的技巧'
date: '2019-08-17T23:03:16+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/515-revision-v1/'
permalink: '/?p=517'
---

如下HTML和CSS代码就好了：

```
<pre class="wp-block-preformatted"><span class="colorful"></span>
```

```
<pre class="wp-block-preformatted">.colorful {
    display: inline-block;
    width: 32px; height: 32px;
    background-color: #f4615c;
    -webkit-mask: url(./test.png) no-repeat;
    mask: url(./test.png) no-repeat;
    -webkit-mask-size: 100% 100%;
    mask-size: 100% 100%;
}
```

CSS3 mask默认是基于透明度实现遮罩效果的。也就是实色区域显示，透明区域隐藏。因此，我们只需要把目标图标颜色#f4615c作为背景色，然后原始图标（无论什么颜色都可以）作为遮罩图片，效果就出来了。