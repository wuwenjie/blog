---
id: 589
title: padding实现图片等比例自适应
date: '2017-10-10T15:47:22+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=589'
permalink: /2017/10/padding-for-images-responsive
categories:
    - 'HTML&amp;CSS'
---

### 一、CSS百分比padding都是相对宽度计算的

在默认的水平文档流方向下，CSS `margin`和`padding`属性的垂直方向的百分比值都是相对于宽度计算的，这个和`top`, `bottom`等属性的百分比值不一样。

对于`padding`属性而言，任意方向的百分比`padding`都现对于宽度计算可以让我们轻松实现固定比例的块级容器，举个例子，假设现在有个`<div>`元素：

```
<pre class="wp-block-preformatted">div { padding: 50%; }
```

或者：

```
<pre class="wp-block-preformatted">div { padding: 100% 0 0; }
```

或者：

```
<pre class="wp-block-preformatted">div { padding-bottom: 100%; }
```

则这个`<div>`元素尺寸就是一个宽高1:1的正方形，无论其父容器宽度是多少，这个`<div>`元素总能保持比例不变。

这种能固定比例的特性什么作用呢？

对于绝大多数都布局，我们并不要求非要比例固定，但是有一种情况例外，那就是图片，因为图片原始尺寸它是固定的。在传统的固定宽度的布局下，我们会通过给图片设定具体的宽度和高度值，来保证我们的图片占据区域稳固；但是在移动端或者在响应式开发情况下，图片最终展现的宽度很可能是不确定的，例如手机端的一个通栏广告，iPhone7下宽度是375，iPhone7 Plus下是414，还有360等尺寸，此时需要的不是对图片进行固定尺寸设定，而是比例设定。

通常有如下一些实现：

1\. 固定一个高度，然后使用`background-size`属性控制，如下：

```
<pre class="wp-block-preformatted">.banner {
    height: 40px;
    background-size: cover;
}
```

实时效果如下：

可以看到随着宽度的变化，总会有部分图片区域（宽度或高度）无法显示，并不是完美的做法。

2\. 使用视区宽度单位`vw`，如下：

```
<pre class="wp-block-preformatted">.banner {
    height: 15.15vw;
    background-size: cover;
}
```

如果对兼容性要求不是很高，使用`vw`也是一个不错的做法，至少理解起来要更轻松一点。

但是，如果我们的图片不是通栏，而是需要离左右各`1rem`的距离，此时，我们的CSS代码就要啰嗦点了，想要保持完美比例，就使用借助CSS3 `calc()`计算：

```
<pre class="wp-block-preformatted">.banner {
    height: calc(0.1515 * (100vw - 2rem));
    background-size: cover;
}
```

如果，图片距离两侧的宽度是动态不确定的，则，此时`calc()`也捉襟见肘了，但，恰恰是普普通通其貌不扬的`padding`属性，其兼容性和适应性都一级棒。

3\. 使用百分比`padding`，如下：

```
<pre class="wp-block-preformatted">.banner {
    padding: 15.15% 0 0;
    background-size: cover;
}
```

此时无论图片的外部元素怎么变动，比例都是恒定不变的。

### 二、CSS百分比padding与宽度自适应图片布局

但是有时候我们的图片是不方便作为背景图呈现的，而是内联的`<img>`，百分比`padding`也是可以轻松应对的，求套路是比较固定的，图片元素外面需要一个固定比例的容器元素，例如下面的HTML结构：

```
<pre class="wp-block-preformatted"><div class="banner">
  <img src=""banner.jpg>
</div>
```

`.banner`元素同样负责控制比例，然后图片填充`.banner`元素即可，CSS代码如下：

```
<pre class="wp-block-preformatted">.banner {
    padding: 15.15% 0 0;
    position: relative;
}
.banner > img {
    position: absolute;
    width: 100%; height: 100%;
    left: 0; top: 0;
}
```

————-

其实，我之前一直低估百分比`padding`的实际应用价值，因为有`vw`单位的存在，毕竟理解`vw`看上去要更简单一些，所以，一直就没做相关技巧的介绍。但是，随着图片相关布局处理越来越多，我发现，百分比`padding`的实用价值要比想象的大，要比`vw`单位适用场景更多，兼容性更好（百分比特性IE6+支持，图片100%覆盖IE8+支持）。

对于复杂布局，如果图片的宽度是不固定的自适应的，我们通常会想到这么一个取巧的做法，就是只设定图片的宽度，例如：

```
<pre class="wp-block-preformatted">img { width: 100%; }
```

此时浏览器默认会保持图片比例显示，图片宽度大了，高度也跟着一起变大；图片宽度小了，高度也跟着一起变小。开发人员似乎无需关心图片真实比例是怎样的。

然而这种技巧有一个非常不好的体验问题，那就是随着页面加载的进行，图片占据的高度会有一个从`0`到计算高度的图片变化，视觉上会有明显的元素跳动，代码层面会有布局重计算。

所以对图片高宽进行同时约定还是有必要的，但是同时要保证宽度自适应，似乎有点难度。记住，如果遇到这种需求场景，没有比百分比`padding`布局更好的做法！

缩小浏览器宽度可以看到不同宽度下的布局效果，Gif效果截图如下：

此demo难点就是图片自适应同时保持比例，以及页面刷新的时候没有布局稳固不晃动，其核心HTML和CSS代码如下：

```
<pre class="wp-block-preformatted"><div class="works-item-t">
    <img src="./150x200.png">
</div>
```

```
<pre class="wp-block-preformatted">.works-item-t {
    padding-bottom: 133%;
    position: relative;
}
.works-item-t > img {
    position: absolute;
    width: 100%; height: 100%;
}
```

可以看到，当把垂直方向`padding`值只使用`padding-bottom`表示的时候，如果没有`text-align`属性干扰，`<img>`元素的`left:0;top:0`是可以省略的。

对于这种图片宽度100%容器，高度按比例的场景，`padding-bottom`的百分比值大小就是图片元素的高宽比，就这么简单。

但，有时候，图片宽度并不是100%容器的，例如，图片宽度50%容器宽度，图片高宽比`4:3`，此时，CSS垂直方向百分比就666了，如下：

```
<pre class="wp-block-preformatted">.img-box {
    padding: 0 50% 66.66% 0;
}
```