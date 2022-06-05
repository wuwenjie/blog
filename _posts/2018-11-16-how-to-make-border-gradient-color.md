---
id: 643
title: CSS实线边框渐变以及虚线边框渐变
date: '2018-11-16T22:41:54+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=643'
permalink: /2018/11/how-to-make-border-gradient-color
categories:
    - 'HTML&amp;CSS'
---

# 1. 实线边框渐变

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

## 2. 虚线边框渐变

##### CSS代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.box {
    width: 200px;
    height: 150px;
    border: 2px dashed #cd0000;
    box-sizing: border-box;
}
@supports (-webkit-mask: none) or (mask: none) {
.box {
    border: none;
    background: linear-gradient(to bottom, #34538b, #cd0000) no-repeat;
    -webkit-mask-image: linear-gradient(to right, #000 6px, transparent 6px), linear-gradient(to bottom, #000 6px, transparent 6px),  linear-gradient(to right, #000 6px, transparent 6px), linear-gradient(to bottom, #000 6px, transparent 6px);
    -webkit-mask-repeat: repeat-x, repeat-y, repeat-x, repeat-y;
    -webkit-mask-position: 0 0, 0 0, 0 100%, 100% 0;
    -webkit-mask-size: 8px 2px, 2px 8px, 8px 2px, 2px 8px;
    /* 合并写法 */
    mask: linear-gradient(to right, #000 6px, transparent 6px) repeat-x,
    linear-gradient(to bottom, #000 6px, transparent 6px) repeat-y,
    linear-gradient(to right, #000 6px, transparent 6px) repeat-x 0 100%,
    linear-gradient(to bottom, #000 6px, transparent 6px) repeat-y 100% 0;
    mask-size: 8px 2px, 2px 8px, 8px 2px, 2px 8px;
}    
}
```

##### HTML代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div class="box"></div>
```