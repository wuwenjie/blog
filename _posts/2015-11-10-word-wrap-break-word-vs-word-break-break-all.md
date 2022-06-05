---
id: 491
title: 'word-wrap: break-word和word-break: break-all区别'
date: '2015-11-10T21:37:45+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=491'
permalink: /2015/11/word-wrap-break-word-vs-word-break-break-all
categories:
    - 'HTML&amp;CSS'
---

相信大家对断句换行都有一点了解，也许也有部分人连这个属性是什么都不知道。当然了，如果是IT这个专业相关的大佬们可以不用看了，这篇文章主要针对于自学前端和转行的朋友们。

> 案例

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">p{
    width: 200px;
    height: 200px;
    background: #ccc;
}
```

<figure class="wp-block-image">![](//upload-images.jianshu.io/upload_images/8919399-b06e064b2b1e3da4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/398/format/webp)</figure>尴尬的效果图

 不知道有没有人遇到过这种情况，如果遇到了，是否看起来很蛋疼？答案是肯定的，那么我们肯定希望他换行呀~，所以我们的`word-wrap: break-word`的存在就有意义了！效果如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">p{
    width: 200px;
    height: 200px;
    background: #ccc;
    word-wrap: break-word;
}
```

<figure class="wp-block-image">![](//upload-images.jianshu.io/upload_images/8919399-d243959c33ba5d77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/227/format/webp)</figure>word-wrap: break-word效果图

 可以看到，终于让那个单词换行了，但是我们还发现这个问题，好多行后面都有一大片空白被浪费了，看起来觉得很不爽，如下图所示:

<figure class="wp-block-image">![](//upload-images.jianshu.io/upload_images/8919399-9d8f9bfbc193c74e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/270/format/webp)</figure>尴尬的效果图

那我们怎么搞到这种强迫症患者绝对不能容忍的问题呢？接下来我们的`word-break: break-all`的存在就有意义了，效果如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">p{
    width: 200px;
    height: 200px;
    background: #ccc;
    word-break: break-all;
}
```

<figure class="wp-block-image">![](//upload-images.jianshu.io/upload_images/8919399-09768169ccaca09c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/224/format/webp)</figure>完美效果图

 好了，这样子看起来舒服多了，但是对于一个单词就是要一行看完，就不喜欢一个单词换两行的朋友，就可以不用`word-break: break-all`了，毕竟这个属性，我也基本不用，一般用`word-wrap: break-word`就差不多了