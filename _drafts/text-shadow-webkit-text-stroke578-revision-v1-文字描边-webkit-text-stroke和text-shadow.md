---
id: 579
title: 文字描边-webkit-text-stroke和text-shadow
date: '2019-08-18T15:13:51+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/578-revision-v1/'
permalink: '/?p=579'
---

### 一、-webkit-text-stroke属性

用[caniuse](http://caniuse.com/#search=text-stroke)查看`text-stroke`兼容性的时候，发现了一件令我震惊的事情，最新版的Firefox以及Edge已经支持`text-stroke`描边属性了，并且全部都是采用`-webkit-`私有前缀。

<figure class="wp-block-image">![text-troke浏览器兼容性](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_153710.png)</figure><figure class="wp-block-image">![Edge 15采用webkit私有前缀示意](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_153932.png)</figure>### 二、-webkit-text-stroke文字描边简介

CSS `text-stroke`属性的语法并不复杂，和`border`，`background`属性类似，其实是若干个CSS属性合并后的名称写法，不过仅仅是下面这两个CSS属性合体：`text-stroke-width`和`text-stroke-color`，也就是描边的宽度和描边的颜色，和`border`不同，对于`text-stroke`属性，我们无法指定描边的类型，只能是实线描边，不支持点线或者虚线，也无法指定描边是外描边还是内描边还是居中描边。

简单示意：

```
<pre class="wp-block-preformatted">.stroke {
  -webkit-text-stroke: 2px red;
}
```

等同于：

```
<pre class="wp-block-preformatted">.stroke {
  -webkit-text-stroke-width: 2px;
  -webkit-text-stroke-color: red;
}
```

另外，和`border`属性还有一个不同之处是宽度的默认值不一样。`border-width`的默认值是`medium`，最终表现等同于设置`3px`，但是`text-stroke-width`的默认值确是`0`，这就意味着，当我们使用`text-stroke`属性的时候，描边宽度是必不可少的，颜色理论上可以不设置，但是由于颜色缺省的时候会使用文字的`color`所谓描边色，导致最终的效果只是单纯的文字变得更壮实了，已然不是描边效果了，例如：

```
<pre class="wp-block-preformatted">.stroke {
  font-size: 40px;
  -webkit-text-stroke: 2px;
}
```

然后下图是有描边和没有描边时候的对比图：

<figure class="wp-block-image">![文字单纯描边对比图](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_162731.png)</figure>### 三、-webkit-text-stroke是外描边，内描边还是居中描边？

`-webkit-text-stroke`是外描边，内描边还是居中描边？

答案是**居中描边**。

下图是demo页面测试结果截图：

<figure class="wp-block-image">![描边位置测试效果截图](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_175554.png)</figure>如果`text-stroke`是外描边，则上图最后两行文字红色部分大小应该是一样的；如果`text-stroke`是内描边，则最后文字重叠的时候，红色边框应该是看不见的；而最终的结果是，最后两行红色边框大小不一，差不多一半大小的样子，由此可以说明`text-stroke`是居中描边效果。

**居中描边特性的应用**  
`text-stroke`的居中描边特性，本质上让真实文本的字重削弱了，例如文字在页面上渲染时候，线条粗细大概是`1`像素，这时候我们设置个`1`像素宽的描边，则真实显示粗细岂不是只剩下`0.5`像素，如果真是这样，我们其实可以模拟`font-weight`属性的不同字重效果。

现代开源字体，你有思源黑体或者思源宋体，或者苹方字体都有丰富的字重，`font-weight:100`和`font-weight:400`可以明显看出文字的粗细不一样，但是对于微软雅黑字体，由于字重的缺失，`font-weight:100`和`font-weight:400`粗细都是一样的，都是正常粗细，这其实很难满足挑剔的设计师的需求，在一些弱表现场景，设计师其实希望我们这粗细更细一点，以营造更精致的感觉。在OS X苹果系的产品下很好实现，因此苹方等字体字重丰富，但是，在大头windows系统下，中文字体弱的很，除了正常和粗就没有其他表现。但是，有了`text-stroke`的居中描边特性，理论上，在windows系统也有了字重解决方案。

对于更大的字重，直接类似`-webkit-text-stroke:1px`即可，关键是如何表现更小的字重。

#### 1. 文字所在背景是纯色

我们只要设置`-webkit-text-stroke-color`描边颜色和背景色保持一致，就可以让文字变细了。话是这么讲没错，但是实际的效果……我们直接看图吧~

Chrome浏览器下（左侧数值是描边大小，上面是字号设置大小）：

<figure class="wp-block-image">![Chrome浏览器下文字变细效果](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_194715.png)</figure>Firefox浏览器下：

<figure class="wp-block-image">![Firefox下文字变细效果截图](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_194743.png)</figure>可以看到最终的效果，似乎确实变细的，但是，又似乎不够细腻，尤其字号较小的时候，文字线条的边缘好像被狗啃了一样，并没有丝滑之感，我们不妨放大一探究竟：

<figure class="wp-block-image">![狗啃效果放大后的呈现](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_195627.png)</figure>由于鄙人的显示器屏幕密度有限，为`1`，最小显示单位为`1`像素，所以，如果要想小数大小的描边效果生效，就需要特殊的文字边缘渲染算法进行视觉上的处理，而由于字符线条切线角度往往是不规则多变的，这就导致细节渲染上无法尽善尽美，于是视觉上会有某县线条位置会有“狗啃”的感觉。如果类似iMac这种5K屏幕，最小渲染单位为`0.2`像素，则显示效果一定非常细腻，这就是为什么高清屏显示效果更佳出众的原因。

但是，如果我们的文字字号比较大，例如`slogan`这种标题文字，则字号变细的效果则要好很多

#### 2. 文字所在背景非纯色

目前为止我还没想到什么好办法。

### 四、text-shadow与文字的外描边效果

如果想要有文字的外描边效果，可以使用CSS3 `text-shadow`属性模拟，兼容IE10+，例如：

```
<pre class="wp-block-preformatted">.strok-outside {
    text-shadow: 0 1px red, 1px 0 red, -1px 0 red, 0 -1px red;
}
```

由于`text-shadow`不支持阴影扩展（`box-shadow`支持），因此，我们使用四向阴影叠加实现，效果类似下面这样：

<figure class="wp-block-image">![text-shadow下的外描边效果截图](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_205440.png)</figure>基本效果达成，但是，实际上，并不是真正外描边效果，因为一个矩形四面投影的时候，四个角实际上会有空隙，这就导致方方正正的汉字最终的外描边效果显得比较的圆润，有点幼圆字体的感觉。

如果设计师对外描边效果比较苛刻，可以使用`-webkit-text-stroke`描边文字和非描边文字相互重叠覆盖的方法模拟，技巧就在于`-webkit-text-stroke`描边文字的描边宽度要是要实现的外描边效果宽度的2倍。本文的第一个demo中的“重叠描边”实际上已经演示了如何操作，这里就不再展开啦！

### 四、-webkit-text-stroke与多重描边效果

`text-shadow`属性值可以不断累加，但是`text-stroke`属性却不行，如果想要实现多重描边效果，可以借助伪元素多层叠加模拟。

例如：

```
<pre class="wp-block-preformatted"><p data-text="多重描边">多重描边</p>
```

```
<pre class="wp-block-preformatted">p {
    -webkit-text-stroke: 1px #fff;
    font-size: 40px;
    position: relative;
    z-index: 0;
}
p::before,
p::after {
    content: attr(data-text);
    position: absolute; left: 0;
    z-index: -1;
}
p::before {
    -webkit-text-stroke: 7px yellow;
}
p::after {
    -webkit-text-stroke: 4px red;
}
```

最后效果这般丰富多彩：

<figure class="wp-block-image">![多重描边效果示意](https://image.zhangxinxu.com/image/blog/201706/2017-06-04_211947.png)</figure>