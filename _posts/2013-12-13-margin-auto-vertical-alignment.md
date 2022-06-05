---
id: 356
title: 'margin:auto实现水平垂直居中'
date: '2013-12-13T00:12:46+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=356'
permalink: /2013/12/margin-auto-vertical-alignment
categories:
    - 'HTML&amp;CSS'
---

如果要问如何CSS实现绝对定位元素的居中效果，很多人心里已经有答案了。

兼容性不错的主流用法是：

```
<pre class="wp-block-preformatted">.element {
    width: 600px; height: 400px;
    position: absolute; left: 50%; top: 50%;
    margin-top: -200px;    /* 高度的一半 */
    margin-left: -300px;    /* 宽度的一半 */
}
```

但，这种方法有一个很明显的不足，就是需要提前知道元素的尺寸。否则`margin`负值的调整无法精确。此时，往往要借助JS获得。

CSS3的兴起，使得有了更好的解决方法，就是使用`transform`代替`margin`. `transform`中`translate`偏移的百分比值是相对于自身大小的，于是，我们可以：

```
<pre class="wp-block-preformatted">.element {
    width: 600px; height: 400px;
    position: absolute; left: 50%; top: 50%;
    transform: translate(-50%, -50%);    /* 50%为自身尺寸的一半 */
}
```

于是乎，无论绝对定位元素的尺寸是多少，其都是水平垂直居中显示的。

然，问题很明显，兼容性不好。<del>IE10+以及其他现代浏览器才支持</del>, IE9(-ms-), IE10+以及其他现代浏览器才支持。中国盛行的IE8浏览器被忽略是有些不适宜的（手机web开发可忽略）。

实际上，绝对定位元素的居中实现还有另外一种方法，可以说是权衡了上面的尺寸自适应以及兼容性的一个方案，其实现的核心是`margin:auto`.

### 三、margin:auto实现绝对定位元素的居中

首先，我们来看下CSS代码：

```
<pre class="wp-block-preformatted">.element {
    width: 600px; height: 400px;
    position: absolute; left: 0; top: 0; right: 0; bottom: 0;
    margin: auto;    /* 有了这个就自动居中了 */
}
```

代码两个关键点：

1. 上下左右均`0`位置定位；
2. `margin: auto`

于是，就居中了。上面代码的`width: 600px` `height: 400px`仅是示意，你修改为其他尺寸，或者不设置尺寸（需要是图片这种自身包含尺寸的元素），都是居中显示的。很有意思的~~

点击demo页面中间的按钮，让原本`static`的框框`absolute`化，可以发现其是水平垂直居中的。

不知诸位新技能get否？

对了，该方法IE8+以及其他浏览器都是OK的。

当一个绝对定位元素，其对立定位方向属性同时有具体定位数值的时候，流体特性就发生了，例如：

```
<pre class="wp-block-preformatted"><div class="box"></div>
```

```
<pre class="wp-block-preformatted">.box {
  position: absolute;
  left: 0; right: 0;
}
```

如果只有`left`属性或者只有`right`属性，则由于包裹性此时`.box`宽度是`0`。但是，在本例中，因为`left/right`同时存在，因此宽度就不是`0`，而是自适应于`.box`包含块的`<em>padding box</em>`宽度，也就是随着包含块`<em>padding box</em>`的宽度变化，`.box`的宽度也会跟着一起变。

具有流体特性绝对定位元素的`margin:auto`的填充规则和普通流体元素一模一样：

1. 如果一侧定值，一侧`auto`，`auto`为剩余空间大小；
2. 如果两侧均是`auto`, 则平分剩余空间；

：

例如，下面的CSS代码：

```
<pre class="wp-block-preformatted">.father {
    width: 300px; height:150px;
    position: relative;
}
.son { 
    position: absolute; 
    top: 0; right: 0; bottom: 0; left: 0;
}
```

此时`.son`这个元素的尺寸表现为“格式化宽度和格式化高度”，和`<div>`的“正常流宽度”一样，同属于外部尺寸，也就是尺寸自动填充父级元素的可用尺寸的，然后，此时我们给`.son`设置尺寸，例如：

```
<pre class="wp-block-preformatted">.son { 
    position: absolute; 
    top: 0; right: 0; bottom: 0; left: 0;
    width: 200px; height: 100px;
}
```

此时宽高被限制，原本应该填充的空间就被多余了出来，这多余的空间就是`margin:auto`计算的空间，因此，如果这时候，我们再设置一个`margin:auto`，那么：

```
<pre class="wp-block-preformatted">.son { 
    position: absolute; 
    top: 0; right: 0; bottom: 0; left: 0;
    width: 200px; height: 100px;
    margin: auto;
}
```

我们这个`.son`元素就水平和垂直方向同时居中了。因为，`auto`正好把上下左右剩余空间全部等分了，自然就居中啦！