---
id: 347
title: CSS3渐变与设计稿（photoshop）中的对应
date: '2019-08-16T23:55:58+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/346-revision-v1/'
permalink: '/?p=347'
---

### 一、问题没有想得那么简单

提问，使用CSS3 gradient渐变，在一个`400*300`的`div`层上实现一个`(100px, 100px)`到`(200px, 200px)`由红到黄的斜向线性渐变，该如何实现？

//zxx: 这里的讨论CSS3渐变都是基于新式规范写法，且忽略私有前缀

我们可能知道水平渐变的实现，类似这样：

```
<pre class="wp-block-preformatted">{background-image:linear-gradient(left, red 100px, yellow 200px);}
```

效果可能近似这样：

很自然的，那从`(100px, 100px)`到`(200px, 200px)`应该就是从左上角开始，应该是这样子：

```
<pre class="wp-block-preformatted">{background-image:linear-gradient(left top, red 100px, yellow 200px);}
```

效果可能近似这样：

哇哦，帅气，恩，应该就是我们想要的效果了！——

这显然是不可能的，如果真这么简单，我也不会拿出来说了~

<figure class="wp-block-image">![photoshop作图](http://image.zhangxinxu.com/image/blog/201309/2013-09-05_231823.png)</figure><figure class="wp-block-image">![作图的效果 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201309/2013-09-05_231904.png)</figure>我们打开Photoshop等绘图软件，画一个符合上面要求的渐变，看看效果是：

与上面的CSS实现对比一下：

红色区域大小明显差很多，怎么回事？

我只能告诉你，事情远没有你想的那么简单！

### 二、动用懒惰的慢思维，从头开始

我们遇到问题，如果第一反应是求助别人，get的是表层的东西；如果自己深入分析，get的往往是实在的东西。第一种人看似好学，实际是个懒惰的人，勤快地使用轻松、耗费精力较少的快思维，这种人适合做销售、公关，并不适合做技术；但并不表示他赚的票子会少。

题外话点到为止。深吸一口气，来，吸…………好，现在我们重新审视CSS3 gradient线性渐变的标准写法（因webkit不支持，这里省略了`to`）：

```
<pre class="wp-block-preformatted">background-image: linear-gradient(  [ <angle> | <side-or-corner> ,]? <color-stop> [, <color-stop>]+ );
```

上面这种CSS语法我们经常见到，可能有人看不懂具体的意思，其实上面的些符号含义与正则表达式有很多一致之处：

- `[]`在正则中表示一个字符类，这里，你可以理解为一个小单元。
- `|`表示候选。也就是“或者”的意思，要么前面的，要么就后面的。
- `?`为量词，表示`0`个或`1`个，言外之意就是，你可以不指定方向，直接渐变色走起。例如：background:linear-gradient(red, yellow);就是从上往下的红黄条纹效果。
- `+`也是量词，表示`1`个或者更多个。因此，终止颜色是不可缺少的。例如：`linear-gradient(red)`是酱油命，白板。
- `<>`中的是关键字，主要是让开发人员知道这里应该放些什么内容。

<figure class="wp-block-image">![渐变角度与呈现](http://image.zhangxinxu.com/image/blog/201309/45deg-a-or-b.png)</figure>**线性渐变关键字**  
**1. angle**  
`angle`表示渐变的角度，然而，这个角度变化千万不能想当然理解，举个例子：  
如果`angle`是`45deg`, 还是由红到黄的渐变，下面那个图是正确的表现：

是`A`呢还是`B`呢还是`C`呢还是`D`呢？

这个要比女友拿着四件衣服让你说哪个好看要简单吧。

5秒钟倒计时，5, 4, 3, 2, 1, ……

好了，答案是：**C**

亲爱的朋友，回答正确了木有？

我保证，很多人都回答错了（包括我自己），为什么会犯错？原因很简单，“熟悉感效应”。

> 想让人们相信谬误有个可靠的方法，那就是不断重复，因为人们很难对熟悉感和真相加以区别。——丹尼尔·卡尼曼

<figure class="wp-block-image">![旋转45度的效果 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201309/45deg-rotate.gif)</figure>我们，譬如我，非常多次地接触CSS3 `transform`中的旋转，`rotate(45deg)`效果就是元素默认态顺时针旋转`45°`；于是，这种熟悉感会让我们觉得渐变的旋转也应该如此。默认渐变从上到下，那么旋转`45°`应该是`D`啊（参见下gif示意），怎么会是`C`呢？

photoshop与CSS3走得越来越近了，我们可以从photoshop中找到答案。

<figure class="wp-block-image">![45度渐变在photoshop中的效果](http://image.zhangxinxu.com/image/blog/201309/2013-09-06_190049.png)</figure><figure class="wp-block-image">![渐变方向与角度的关系 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201309/2013-09-06_190718.png)</figure>从上图那个圈中的圆环可以看出，渐变的角度与旋转的那个角度完全不是一回事。线性渐变的这个角度为圆心为起点的发散方向。大图示意就是：

**2. side-or-corner**  
`side-or-corner`中文意思就是“边或角”，可选值有：

```
<pre class="wp-block-preformatted">[left | right] || [top | bottom]
```

表示，你可以有如下的写法或组合：  
`left`, `right`, `top`, `bottom`, `left top`, `left bottom`, `right top`, `right bottom`. 分别表示，从左往右，从右往左，从上往下，从下往上，从左上往右下，从……（都懂的，不全写了）

其中的`left top`(从左上往右下)正好我们一开始的例子使用了，现在看看，稍微想想，就知道我们使用错了！

显然，从`(100,100)`到`(300,300)`是个45度倍数角；而`left top`的角度是直奔右下角的，而容器是`400*300`，显然，不是45度倍数角。根据我们上面对`angle`的认识，角度应该是`-45°`，`-45°`为圆心网右下方向`45`度的一条线，正好符合从`(100,100)`到`(300,300)`的方向！

**3. color-stop**  
渐变关键颜色结点，语法为：

```
<pre class="wp-block-preformatted"><color> [ <percentage> | <length> ]
```

中文解释就是，颜色值+空格+百分比或长度值。例如`red 100px`. 记住，这里的颜色值只能一个，因此， `red 100px 100px`是完全错误滴！

OK，现在我们定义重新梳理了一遍，现在实现一开始的渐变效果应该OK了吧，试试呗~

如下CSS：

```
<pre class="wp-block-preformatted">{background-image:linear-gradient(-45deg, red 100px, yellow 200px);}
```

如下效果：

<figure class="wp-block-image">![作图的效果 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201309/2013-09-05_231904.png)</figure>肉眼看上去好像那么回事，我们来对比下photoshop中的正确实现：

<figure class="wp-block-image">![](http://mat1.gtimg.com/www/mb/images/face/10.gif)</figure>额~ 貌似还是不对啊，而且差得更远了，怎么回事？？？

我只能告诉你，事情远没有你想的那么简单！

### 三、深入理解线性渐变的角度坐标

上面的代码我们稍微修改下，加上`-webkit`前缀以及`-moz`前缀看看：

```
<pre class="wp-block-preformatted">{background-image:-webkit-linear-gradient(-45deg, red 100px, yellow 200px);}
```

如下效果（非webkit内核截图）：

哎呀，貌似角度对了嘛！咋回事。

这是Chrome浏览器下的一个奇葩问题，最近，Chrome浏览器已经去掉了CSS3渐变的私有前缀，但是，其中的解析也有了写变化：

```
<pre class="wp-block-preformatted">background-image:-webkit-linear-gradient(-45deg, red, yellow)
```

与

```
<pre class="wp-block-preformatted">background-image:linear-gradient(-45deg, red, yellow)
```

在Chrome浏览器下的渐变方向居然是相反的！`45deg`是正常的。

Firefox浏览器下也是如此，有前缀和没有前缀方向相反！咋回事？

原因很简单，CSS3目前还是草案阶段！

从浏览器去掉前缀前后的变化可以推测，之前，W3C的渐变坐标是与photoshop中一致的，但是，后来，由于某些原因，修改了。

至于什么原因，根据我草草的查找，可能与下面几个关键字之一有联系：`animation/transition`动画、`write-mode`书写方向、`flex box`模型、以及`radial-gradient`渐变等。

目前的[规范](http://dev.w3.org/csswg/css-images-3/#linear-gradients)是这么说的：

> **using angles**  
> For the purpose of this argument, ‘0deg’ points upward, and positive angles represent clockwise rotation, so ‘90deg’ point toward the right.

也就是：

> **使用angles**  
> 参数释义如下，‘0deg’指向上面，同时正角度顺时针旋转，因此‘90deg’指向右边。

<figure class="wp-block-image">![css3线性渐变现在的坐标系](http://image.zhangxinxu.com/image/blog/201309/gradient-rotate.png)</figure>我们画一下就是：

可见，目前，规范的渐变坐标系与photoshop是有差异的。

同时，也告诫我们，私有前缀可不能乱用哦！

面向未来，显然我们都要跟着规范走，于是有CSS：

```
<pre class="wp-block-preformatted">{background-image:linear-gradient(135deg, red 100px, yellow 200px);}
```

效果为：

<figure class="wp-block-image">![作图的效果 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201309/2013-09-05_231904.png)</figure>与PS图比一下：

我去~怎么还是有出入啊？——红色区域大小明显不一样嘛！

我只能告诉你，事情远没有你想的那么简单！

### 四、深入理解角度坐标与位置关系

对于斜向线性渐变，点到点的渐变可不是直接把点的横坐标放上去就可以的。因为当渐变倾斜的时候，渐变的起止点的坐标也发生变化了。下图是官方规范的一张示意图，演示的是`45deg`渐变的起止点以及方向。

<figure class="wp-block-image">![45deg渐变示意图](http://image.zhangxinxu.com/image/blog/201309/gradient-diagram.png)</figure>记住一个关键点，渐变的起点和终点（默认）在过中心的渐变线的垂直线上，于是，我们就可以确定起点与终点的位置了。按照这个理解，我们就可以画出`400*300` `div`上`135deg`起始点在哪里，然后再确定`(100,100)`和`(200,200)`的位置就轻松多了。

<figure class="wp-block-image">![100,100到200,200渐变位置示意图](http://image.zhangxinxu.com/image/blog/201309/100-300-what-mean.png)</figure>如下示意图：

一图顶前言，反正上面这张图我是看懂了。于是，我们的坐标起止点值其实就变成了，黑色括弧的长度以及紫色括弧的长度值分别多少！

虽然很多人不喜欢数学，但是几何应该都还不错，我们来一起算一下……

//zxx: 长度计算中……

结果为，起点：

```
<pre class="wp-block-preformatted">100 * Math.sqrt(2) = 141.4213562373095;
```

终点为：

```
<pre class="wp-block-preformatted">200 * Math.sqrt(2) = 282.842712474619;
```

CSS用上：

```
<pre class="wp-block-preformatted">{background-image:linear-gradient(135deg, red 141.4213562373095px, yellow 282.842712474619px);}
```

效果：

<figure class="wp-block-image">![作图的效果 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201309/2013-09-05_231904.png)</figure>与PS的效果比对下：