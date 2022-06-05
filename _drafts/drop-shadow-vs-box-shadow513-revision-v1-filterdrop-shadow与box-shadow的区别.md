---
id: 514
title: 'filter:drop-shadow与box-shadow的区别'
date: '2019-08-17T22:56:45+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/513-revision-v1/'
permalink: '/?p=514'
---

使用标准的CSS3实现某元素的投影效果，有两个套路，第一个就是使用常见的`box-shadow`, 第二个就是使用CSS3的`filter`阴影滤镜`drop-shadow`，那这两个阴影实现有什么具体的差异呢？

### 一、兼容性不一

CSS3 `box-shadow`从IE9浏览器开始就支持了，[兼容性](http://caniuse.com/css-boxshadow/embed)如下截图：

<figure class="wp-block-image">![CSS3 box-shadow兼容性](https://image.zhangxinxu.com/image/blog/201801/2018-01-10_004223.png)</figure>而`filter`中的`drop-shadow`IE13才开始支持，移动端Android4.4才开始支持，细想一下，其实离在移动端愉快使用就差一口气，明天的今天，大家说不定就在载歌载舞了。

[兼容性](http://caniuse.com/css-filters/embed)如下图：

<figure class="wp-block-image">![drop-shadow滤镜兼容性](https://image.zhangxinxu.com/image/blog/201801/2018-01-10_004331.png)</figure>### 二、同样的参数值，表现效果有差异

`filter`中的`drop-shadow`语法如下：

```
<pre class="wp-block-preformatted">filter: drop-shadow(x偏移, y偏移, 模糊大小, 色值);
```

例如：

```
<pre class="wp-block-preformatted">filter:drop-shadow(5px 5px 10px black)
```

表示右下5像素偏移，10像素模糊的黑色阴影。眼见为实，看下面的图片示意（实时效果，请使用Chrome或手机浏览器查看）：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>但是，如果使用同样参数值的`box-shadow`，例如：

```
<pre class="wp-block-preformatted">box-shadow: 5px 5px 10px black;
```

会发现，`box-shadow`的阴影距离更小，色值要更深：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>### 三、drop-shadow没有内阴影效果

`box-shadow`支持`inset`内阴影，如：

```
<pre class="wp-block-preformatted">box-shadow: inset 5px 5px 10px black;
```

但是，`drop-shadow`却没有。

### 四、drop-shadow不能阴影叠加

`box-shadow`有个超屌的特性，就是阴影可以任意累加，因此，理论上我们可以使用`box-shadow`生成任意的图片

但是`filter`中的`drop-shadow`就只能抱歉了，我就是一锤子买卖。没钱也这么任性！

说到现在，体现的尽是`drop-shadow`的不好，兼容性不够，内阴影不支持，多阴影也不支持；感觉就像是小蚯蚓，失恋了，工作也没了，存在的意义好像就成了白富美的谈资。

真的是这样吗？显然非也！所谓存在既有道理。

`drop-shadow`有一个很厉害的特性，也就这一个特性，让其以后有足够的机会大放异彩！那就是，`drop-shadow`才是真正意义上的投影，而`box-shadow`只是盒阴影而已。

什么意思呢？

### 五、阴影 vs 盒阴影

实践出真知，下面我们用CSS `border`写一个虚线框，例如：

```
<pre class="wp-block-preformatted">border: 10px dashed #beceeb;
```

结果长相如下：

然后，我们分别应用`box-shadow`和`drop-shadow`滤镜：

```
<pre class="wp-block-preformatted">border: 10px dashed #beceeb; box-shadow: 5px 5px 10px black;
```

```
<pre class="wp-block-preformatted">border: 10px dashed #beceeb; filter: drop-shadow(5px 5px 10px black);
```

结果：

怎么样？是不是本性暴露了！

`box-shadow`顾名思意“盒阴影”，只是盒子的阴影；你想啊，这盒子中间明明是透明的，结果，阴影的时候，居然光线没有穿透；但是`drop-shadow`就符合真实世界的投影，非透明的颜色，我就有投影；透明部分，光线穿过，没投影，而什么盒子不盒子的，跟我没有任何关系。

`drop-shadow`不仅可以穿透代码构建的元素的透明部分，PNG图片的透明部分也是可以穿透的，如下图：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/blog/201801/touming-mm1.png)</figure>于是，曾经困扰我们的一些老大难的问题就有了很好的解决思路了！

### 六、drop-shadow的实际应用

<figure class="wp-block-image">![没有阴影的三角](https://image.zhangxinxu.com/image/blog/201605/2016-05-18_004250.png)</figure>我们实现带有箭头指向的浮层面板的时候，考虑到兼容性，三角基本上都是使用`border`绘制的，没法`box-shadow`，但是，矩形部分设计师希望是有阴影的，于是，就会出现下图所示的情况：

箭头没有阴影，蒙混过关。

后来，又捣腾了一个办法，就使用矩形进行45deg旋转，两个`box-shadow`合体，但是，会存在阴影重叠的一部分，说穿了，还是效果不完美。

现在，有了`drop-shadow`，阴影就真的变成了阴影了。