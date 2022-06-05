---
id: 261
title: CSS3下的圆形遮罩效果实现与应用
date: '2019-08-16T19:10:57+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/260-revision-v1/'
permalink: '/?p=261'
---

虽说效果离不开CSS3，但，最最关键的核心属性是CSS `border`属性，偌大的半透明边框属性（demo中边框宽度512像素），边框颜色为`rgba`黑色半透明，然后`50%`圆角。

相关CSS代码如下：

```
<pre class="wp-block-preformatted">.test_cover {
    width: 100px;
    height: 100px;
    border: 512px solid rgba(0, 0, 0, .35);
    border-radius: 50%;
    /* 定位 */
    position: absolute;
    left: -325px;
    top: -465px;
}
```

很简单吧，然后父标签来个尺寸限制，溢出隐藏就完事大吉了。

<figure class="wp-block-image">![é&#129;®ç½©å¼&#149;å¯¼æ&#143;&#144;ç¤ºæ&#149;&#136;æ&#158;&#156;æ&#136;ªå&#155;¾ å¼ é&#145;«æ&#151;­-é&#145;«ç©ºé&#151;´-é&#145;«ç&#148;&#159;æ´»](http://image.zhangxinxu.com/image/blog/201112/2011-12-23_155146.png)</figure>