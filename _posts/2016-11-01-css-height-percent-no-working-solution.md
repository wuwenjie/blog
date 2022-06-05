---
id: 540
title: 'CSS height:100%无效以及替代方案'
date: '2016-11-01T23:55:28+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=540'
permalink: /2016/11/css-height-percent-no-working-solution
categories:
    - 'HTML&amp;CSS'
---

**可以考虑用 vh 或者padding-bottom来替代。。。** <https://javascript.shop/height%e7%99%be%e5%88%86%e6%af%94%e4%bb%a5%e5%8f%8a%e9%ab%98%e5%ba%a6%e8%87%aa%e9%80%82%e5%ba%94%e9%97%ae%e9%a2%98/>

有时我们会困惑为什么设置div的height:100%;没有效果，如下所示：

<figure class="wp-block-image">![](https://ask.qcloudimg.com/http-save/yehe-4737877/pl8u0au3zu.png?imageView2/2/w/1620)</figure>要解决这个问题，先的知道设置height:100%的原理，当你让一个元素的高度设置为百分比高度时，是相对于父元素的高度根据百分比来计算高度。 所以当父元素没有高度时，height:100%也就没有高度值，所以我们来设置body高度。

<figure class="wp-block-image">![](https://ask.qcloudimg.com/http-save/yehe-4737877/c1apcwnt9b.png?imageView2/2/w/1620)</figure>可见还是没有效果，原因跟上一个一样，其父元素也没有高度，所以我们来设置html的高度。

<figure class="wp-block-image">![](https://ask.qcloudimg.com/http-save/yehe-4737877/17o4eltl4b.png?imageView2/2/w/1620)</figure>可见产生了我们想要的效果。

```
<pre class="wp-block-preformatted">
<html lang="en">
<head>
   <meta charset="UTF-8">
   <title>测试height100%</title>
</head>
<style>
   *{
      padding: 0;
      margin: 0;
   }
   html{
      height: 100%;
   }
   body{
      height: 100%;
   }
   .wqh{
      height: 100%;
      background-color: royalblue;
   }
</style>
<body>
<div class="wqh"></div>
</body>
</html>
```

### 进阶

html 的父元素可以理解成window，浏览器会将html填充完一个浏览器窗口。

设置html的height为具体的像素值，当其值大于浏览器窗口时，浏览器出现滚动条，当其值小于浏览器窗口时，同样会将html填充完整个浏览器窗口（可以设置背景测试）。

<figure class="wp-block-image">![](https://ask.qcloudimg.com/http-save/yehe-4737877/elih9rv38f.png?imageView2/2/w/1620)</figure><figure class="wp-block-image">![](https://ask.qcloudimg.com/http-save/yehe-4737877/ii76cytsq2.png?imageView2/2/w/1620)</figure>设置html的height为百分比时，当其值大于100%出现滚动条，当小于100%时同样会将html填充完整个浏览器窗口（可以设置背景测试）。

但是子html的子元素的高度设置成百分比时，会按照html设置的高度值计算比例。（如下所示，html高度为1000px；所以body的高度为500px）。

<figure class="wp-block-image">![](https://ask.qcloudimg.com/http-save/yehe-4737877/eax8sj8huj.png?imageView2/2/w/1620)</figure>对于body的设置的高度小于浏览器窗口时，同样会填充满整个浏览器窗口（可以设置背景测试），但是其子元素的高度设置成百分比时，会按照body设置的高度值来计算比例。

<figure class="wp-block-image">![](https://ask.qcloudimg.com/http-save/yehe-4737877/qdxc9rbfqr.png?imageView2/2/w/1620)</figure></body></html>