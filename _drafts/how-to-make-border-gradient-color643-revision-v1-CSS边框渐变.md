---
id: 646
title: CSS边框渐变
date: '2019-08-18T22:48:46+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/643-revision-v1/'
permalink: '/?p=646'
---

# 边框渐变

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.border-block {
  border: 10px solid transparent;
  border-image: linear-gradient(to top, blue, red);
  border-image-slice: 10;
}
```

实现效果如下:

<figure class="wp-block-image">![](https://upload-images.jianshu.io/upload_images/2318155-3ca36505c881e20a.png)</figure>给border-image加linear-gradient不难理解，但是如果单纯使用border-image，会发现效果是这样的：

<figure class="wp-block-image">![](https://upload-images.jianshu.io/upload_images/2318155-9ea0841060e2155d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)</figure>所以关键作用是border-image-slice属性。

先看下border-image属性。

border-image是border-image-source, border-image-slice, border-image-width, border-image-outset, border-image-repeat的简写。

border-image-source 属性可以给定一个url作为边框图像，类似background-image: url的用法；

<figure class="wp-block-image">![](https://upload-images.jianshu.io/upload_images/2318155-a0861acc0f10876e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)</figure>border-image-slice是指将边框图片切成9份，四个角四个边，和一个中心区域。被切割的9个部分分布在边框的9个区域。

当盒子宽度和被切图像的宽度不相等时，四个顶角的变化具有一定的拉伸效果。border-image-slice属性默认值是100%，这个百分比是相对于边框图像的宽高来的，当左右切片宽度之和&gt;100%时，5号7号就显示空白，因此使用默认值无法看到被填满的边框图片。关于boder-image具体可以查找资料。