---
id: 258
title: 'text-align属性对position:absloute/fixed的元素无效'
date: '2012-03-16T19:05:01+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=258'
permalink: /2012/03/text-align-on-position-absloute-fixed-element
categories:
    - 'HTML&amp;CSS'
---

text-align属性对position:absloute/fixed的元素无效

实现元素的水平居中，有个很经典的方法就是：

```
<pre class="wp-block-preformatted">.center { margin-left: -"1/2个元素宽度"; left: 50%; position: absolute; }
```

但是，此方法需要父容器是body，或是是设置了`position:relative`属性的元素，属性关联一是耗代码，关键是维护易出叉子。

从本文的分析来看，实际上，我们可以直接使用`margin-left`属性，无需`left`属性以及父标签的`position:relative`申明就可以实现居中显示效果了（例如图片弹出，页面居中浏览效果）。

```
<pre class="wp-block-preformatted">.center { margin-left: - ("1/2个元素宽度" + 2); position: absolute; }
```

不过，直接的`margin`方法虽好，但是有两个较大的局限性：  
1\. 自身宽度需知，否则，得要借助脚本去获取才能实现居中；  
2\. 无法实现block水平元素的水平居中

因此，除了某些特殊的场合，`margin+position`这种组合的水平居中方式是没有什么用武之地的。

<figure class="wp-block-image">![新浪微博的返回顶部按钮截图 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201112/2011-12-27_111528.png)</figure>那哪些是特殊场合呢？例如，新浪微博或是其他很多网站常有的页面主体右侧的“返回顶部”按钮。

这个按钮是怎么实现右侧定位的呢？先看看新浪微博的实现方式，小bug查看，发现是我上面提到的经典的`left:50% + margin`方法，核心CSS如下：

```
<pre class="wp-block-preformatted">.W_gotop { 
    position: fixed;
    left: 50%;
    margin-left: 476px; /*这个显然经过了计算，且受限于容器宽度值*/
    bottom: 100px;
}
```

根据本文的内容，实际上，实现该效果，可以直接使用text-align属性，“返回顶部”直接就主体右侧显示了，无需水平方向定位！

您可以狠狠地点击这里：[text-align:right声明下的返回顶部定位demo](http://www.zhangxinxu.com/study/201112/text-align-right-position-fixed-top.html)

<figure class="wp-block-image">![demo页面返回顶部的定位效果Chrome截图 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201112/2011-12-27_112759.png)</figure>核心CSS代码如下：

```
<pre class="wp-block-preformatted">.sina_backtop_box { text-align: right; }
.sina_backtop { ...; position: fixed; bottom: 100px; }
```

HTML如下：

```
<pre class="wp-block-preformatted"><div class="sina_backtop_box">
    &nbsp;<a href="#" class="sina_backtop">返回顶部</a>
</div>
```

`&nbsp;`用来实现兼容性的效果，已经做过解释，不多说。

`text-align: right;`实现的好处是：返回顶部的定位与页面主体宽度无关了。在页面宽度自适应的布局中可以大放异彩！