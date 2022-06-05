---
id: 595
title: 纯css实现展开收起动画
date: '2019-08-18T17:16:54+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/594-revision-v1/'
permalink: '/?p=595'
---

“展开收起”效果是网页中比较常见的一种交互方式，通常的做法是控制`display`属性值在`none`和其它值之间切换，虽说功能可以实现，但是效果略显生硬，所以会有这样的需求——希望元素展开收起能具有**平滑**的效果。传统实现可以使用JQuery的`slideUp()`/`slideDown()`方法，但是，在移动端，由于CSS3动画支持良好，所以移动端的JavaScript框架都是没有动画模块的。此时，使用CSS实现动画成为了最佳的技术选型：

我们的第一反应可能是考虑使用`height`+`overflow:hidden`+`transition`的方案，如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.element {
    height: 0;
    overflow: hidden;
    transition: height .25s;
}

.element:hover {
    height: auto;     /* 没有transition效果，仅仅生硬地隐藏/展开 */ 
}
```

但是结果可能并不会是我们所预期的那样；原因是我们将要展开的元素内容是动态的，即**高度值不确定**，因此，height使用的值是默认的`auto`，从0px到auto是无法计算的，因此无法形成过渡或动画效果。

## 为了解决这个问题，可以使用`max-height`代替，如：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.element {
    max-height: 0;
    overflow: hidden;
    transition: max-height .25s;
}

.element:hover {
    max-height: 666px;     /* 一个足够大的值*/ 
}
```

其中展开后的max-height值，只需要设定为**保证比展开内容高度大**的值即可，在max-height值比height值大的情况下，元素仍会默认采用自身的高度值，即`auto`；如此一来，一个高度不定的元素展开收起动画效果就实现了。

然而在使用本方法时，需要注意一点：虽然从适用范围上说，max-height值越大可供使用的场景越多，但是如果max-height值太大，在元素收起的时候将会产生**延迟**的效果，这是因为在收起时，max-height从设定的特别大的值，到元素自身高度值的变化过程将占用较多时间，此时画面表现则会产生延迟的效果。

因此，建议将max-height值设置为**足够安全的最小值**，这样在收起时即使有略微延迟，也会因为时间很短，难以被用户感知，将不会影响体验。