---
id: 489
title: will-change提高动画性能与页面滚动性能
date: '2015-11-03T21:30:56+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=489'
permalink: /2015/11/will-change-improve-animation
categories:
    - 'HTML&amp;CSS'
---

### 一、先来看一个例子

下面这个例子来自[某外文](https://www.zhangxinxu.com/wordpress/2015/11/css3-will-change-improve-paint/Fix%20scrolling%20performance%20with%20CSS%20will-change%20property)，我这里简单转述下。

视差滚动现在不是挺流行的嘛，然后[Chris Ruppel](https://fourword.fourkitchens.com/user/13)当其使用`background-attachment: fixed`实现背景图片不随滚动条滚动而滚动效果的时候，发现，页面的绘制性能掉到了每秒30帧，这种帧频人眼已经可以感觉到一定的顿挫感了。

<figure class="wp-block-image">![每秒30帧示意](https://image.zhangxinxu.com/image/blog/201511/fourkitchens-frontpage-before_0_0.png)</figure>后来，参考一些其他同事还是同行的建议，做了一番优化，然后，页面的渲染性能——

<figure class="wp-block-image">![优化后的帧频截图](https://image.zhangxinxu.com/image/blog/201511/fourkitchens-frontpage-after_0_0.png)</figure>这优化之前完全就是便秘，屎拉不出来的感觉；而优化之后，完全就是一泻千里，裤子都来不及脱的感觉。

> 一兄得便秘，在厕所里久久不能如便。  
> 正在他极力努力的时候，看一哥们风一样的冲进厕所，进了他旁边的位置，刚进去就传来一真狂风暴雨，那兄羡慕的对那哥们说：哥们好羡慕你呀！  
> 那哥们说：羡慕啥，裤子还没脱呢。。。

大家肯定会好奇，这到底施了什么魔法，可以让渲染提升如此之显著。3个小tips:

1. `background-attachment: fixed`改成了`position: fixed`，因为前面这玩意滚动实时计算重绘；
2. 背景图片所在的元素替换为`::before`伪元素；
3. 使用了CSS3 `will-change`加速；

相关代码如下（假设类名是`front`）：

```
<pre class="wp-block-preformatted">.front::before {
    content: '';
    position: fixed; // 代替background-attachment
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background-color: white;
    background: url(/img/front/mm.jpg) no-repeat center center;
    background-size: cover;
    will-change: transform; // 创建新的渲染层
    z-index: -1;
  }
```

OK, 主角粉墨登场了，就是`will-change`, 这是什么鬼？

### 二、CSS3 will-change粉墨登场

CSS3 `will-change`属于web标准属性，虽然目前还是草案阶段，但出现已经有些时日了，[兼容性](http://caniuse.com/will-change/embed)这块Chrome/FireFox/Opera都是支持的。

<figure class="wp-block-image">![will-chang兼容性e](https://image.zhangxinxu.com/image/blog/201801/2018-01-09_003111.png)</figure>这个属性作用很单纯，就是“增强页面渲染性能”。那它是如何增强的呢？

我们可能听听说过，3D transform会启用GPU加速<sup>①</sup>，例如`translate3D`, `scaleZ`之类，但是呢，这些属性业界往往称之为`hack`加速法。我们实际上不需要`z`轴的变化，但是还是假模假样地声明了，欺骗浏览器，这其实是一种不人道的做法。

> ① GPU即图形处理器，是与处理和绘制图形相关的硬件。GPU是专为执行复杂的数学和几何计算而设计的，可以让CPU从图形处理的任务中解放出来，从而执行其他更多的系统任务，例如，页面的计算与重绘。

而`will-change`则天生为此设计，顾名思意“我要变形了”，礼貌而友好。

几何老师：“同学们注意，我要开始变形了。”

哈哈，别笑。真是这样的。

当我们通过某些行为（点击、移动或滚动）触发页面进行大面积绘制的时候，浏览器往往是没有准备的，只能被动使用CPU去计算与重绘，由于没有事先准备，应付渲染够呛，于是掉帧，于是卡顿。而`will-change`则是真正的行为触发之前告诉浏览器：“浏览器同学，我待会儿就要变形了，你心理和生理上都准备准备”。于是乎，浏览器同学把GPU给拉上了，从容应对即将到来的变形。

这其实很好理解的，对吧，提前预约从容不迫；突然造访手忙脚乱。

MDN上显示该属性语法如下：

```
<pre class="wp-block-preformatted">/* 关键字值 */
will-change: auto;
will-change: scroll-position;
will-change: contents;
will-change: transform;        /* <custom-ident>示例 */
will-change: opacity;          /* <custom-ident>示例 */
will-change: left, top;        /* 两个<animateable-feature>示例 */

/* 全局值 */
will-change: inherit;
will-change: initial;
will-change: unset;
```

其中：  
**auto**  
就跟`width:auto`一样，实际上没什么卵用，昨天嘛，估计就是用来重置其他比较屌的值。

**scroll-position**  
告诉浏览器，我要开始翻滚了。

**contents**  
告诉浏览器，内容要动画或变化了。

**&lt;custom-ident&gt;**  
顾名思意，[自定义的识别](https://developer.mozilla.org/en-US/docs/Web/CSS/custom-ident)。非规范称呼，应该是MDN自己的称呼，以后可能会明确写入规范。比方说`animation`的名称，计数器`counter-reset`, `counter-increment`定义的名称等等。

上面展示了2个例子，一个是`transform`一个是`opacity`，都是CSS3动画常用属性。如果给定的属性是缩写，则所有缩写相关属性变化都会触发。同时不能是以下这些关键字值：`unset`, `initial`, `inherit`, `will-change`, `auto`, `scroll-position`, 或 `contents`.

**&lt;animateable-feature&gt;**  
可动画的一些特征值。比方说`left`, `top`, `margin`之类。移动端，非`transform`, `opacity`属性的动画性能都是低下的，所以都是建议避免使用`left`/`top`/`margin`之流进行唯一等。但是，如果你觉得自己是`margin`属性奶大的，非要使用之，试试加个`will-change:margin`说不定也会很流畅（移动端目前支持还不是很好）。

就目前而言，使用的基本上都是：

```
<pre class="wp-block-preformatted">.example {
  will-change: transform;
}
```

### 三、CSS3 will-change使用注意事项

`will-change`虽然可以加速，但是，一定一定要适度使用。那种全局都开启`will-change`等待模式的做法，无疑是死路一条。尼玛，用脚趾头想想也知道，你让浏览器各个元素都随时GPU渲染加速待命，还是妥妥搞死！

说到这里，想到了移动端的GPU加速。很多自以为然的同学写CSS3动画的时候，或者静态属性的时候，动不动就把`translateZ`之类GPU hack属性写上。同学们啊，GPU这玩意提高页面渲染性能它是有代价的呀，什么代价呢，就是手机的电量。你真以为有“既要马儿跑，又要马儿不吃草”的好事情啊！

平时，我们一般地CSS动画，平常的渲染处理，手机都是可以比较流畅的。完全没有必要以牺牲其他东西来实现。手机上的电量弥足珍贵。如果发现(尤其Android)机子h5页面不流畅，找找看是不是动画属性使用问题，或者非可视动画层没隐藏等等原因。

回到`will-change`. 同样的，`will-change`的使用也要谨慎，遵循最小化影响原则，所以，一开始的例子，才使用伪元素去搞，独立渲染（虽然我没看出来这个梗在什么地方）。

sitePoint网站上的[这篇文章](https://www.zhangxinxu.com/wordpress/2015/11/css3-will-change-improve-paint/An%20Introduction%20to%20the%20CSS%20will-change%20Property)展示了几个处理例子：

不要这样直接写在默认状态中，因为`will-change`会一直挂着：

```
<pre class="wp-block-preformatted">.will-change {
  will-change: transform;
  transition: transform 0.3s;
}
.will-change:hover {
  transform: scale(1.5);
}
```

可以让父元素hover的时候，声明`will-change`，这样，移出的时候就会自动`remove`，触发的范围基本上是有效元素范围。

```
<pre class="wp-block-preformatted">.will-change-parent:hover .will-change {
  will-change: transform;
}
.will-change {
  transition: transform 0.3s;
}
.will-change:hover {
  transform: scale(1.5);
}
```

如果使用JS添加`will-change`, 事件或动画完毕，一定要及时`remove`. 比方说点击某个按钮，其他某个元素进行动画。点击按钮(click)，要先按下(mousedown)再抬起才出发。因此，可以`mousedown`时候打声招呼, 动画结束自带回调，于是（示意，不要在意细节）：

```
<pre class="wp-block-preformatted">dom.onmousedown = function() {
    target.style.willChange = 'transform';
};
dom.onclick = function() {
    // target动画哔哩哔哩...
};
target.onanimationend = function() {
    // 动画结束回调，移除will-change
    this.style.willChange = 'auto';
};
```

等。

### 四、参考文章

- [Fix scrolling performance with CSS will-change property](https://fourword.fourkitchens.com/article/fix-scrolling-performance-css-will-change-property)
- [MDN:will-change](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)
- [An Introduction to the CSS will-change Property](http://www.sitepoint.com/introduction-css-will-change-property/)