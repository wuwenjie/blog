---
id: 240
title: 对html与body的一些研究与理解
date: '2019-08-16T18:15:01+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/239-revision-v1/'
permalink: '/?p=240'
---

### 一、写在前面的

最近一直构思着写篇关于`<html>`标签的文章，虽说之前处理过`<html>`标签，也解决过不少棘手的问题，但是对其理解还不是很透彻，很多原理都是自己推测的。

在网上查阅相关资料想整合一下相关的信息，发现深入探讨`<html>`，与`<body>`的文章很少，只有在蓝色理想看到一篇“一叶千鸟”的这篇“[正确认识html与body](http://www.blueidea.com/tech/site/2006/3130.asp)”有点价值，其余都没有什么相关的好文章。

而这里，我要讲述的与上面的交集并不多，只要是讲述一些关于`<html>`与`<body>`的各种表现，以及我自己对这些表现的合理的解释和并提供一些过去处理类似问题的经验，希望对大家能够有所帮助。

### 二、关于html与body的一些表现

#### 1.背景色

一般情况下，我们css控制的最高节点就是`body`，例如设置：

```
<pre class="wp-block-preformatted">body{background:#069;}
```

则浏览器界面就是完全的`#068`的背景色。这里看上去是`<body>`标签下的背景色起作用了，我到不这么认为，这里不是`body`的`background`起作用，而是`body`作为一个根节点起作用了，`<html>`标签未被激活，`body`担当类似于根节点的节点，其`background`背景色被浏览器俘获，浏览器界面背景色为`background`的背景色，以上是我的推论，这种推论不是我凭空想象出来的，而是有一定的根据的。看下面的一段css代码：

```
<pre class="wp-block-preformatted">body{background:#069; margin:100px; border:30px solid #093;}
```

意思很简明：外边距`100`像素，边框`30`像素，背景色`#069`，按照对一般标签的理解，`100`像素的外边距应该不含有背景色的，然而显示的结果是（Firefox下表现与此类似）：

<figure class="wp-block-image">![IE6下body设置background颜色边框和边距后的表现](http://image.zhangxinxu.com/image/blog/200909/2009-09-18_153712.png)</figure>IE6下body设置background颜色边框和边距后的表现

还有一点可以证明我上面的推论，就是一旦设置了`<html>`节点的`background`背景色之后，`<body>`的背景色将失效。例如下面的简短代码：

```
<pre class="wp-block-preformatted">html{background:#999;}
body{background:#069; margin:100px; border:30px solid #093;}
```

跟上面的想比，就是添加了`html`的背景色，结果（截自IE6，Firefox浏览器下表现一致）：

<figure class="wp-block-image">![html标签设置背景色后的样式表现](http://image.zhangxinxu.com/image/blog/200909/2009-09-18_175421.png)</figure>html标签设置背景色后的样式表现

结果是什么呢？`<body>`标签的满屏的背景色不见了，“失效”了。其实，在我看来，不是“失效”，是生效了。当`<html>`标签无背景样式时，`<body>`的背景色其实不是`<body>`标签的背景色，而是浏览器的。一旦html标签含有背景色，则`<body>`的背景色变成了正常的`<body>`标签（一个实实在在，普普通通标签）的背景色，而此时的`<html>`标签最顶级，背景色被浏览器获取，成为浏览器的背景色。

#### 2.margin支持

从上面的实例可以看出，无论是IE6浏览器，还是Firefox浏览器，`<body>`的`margin`属性是支持的，`padding`也是如此，那么对于`<html>`标签呢。如下测试代码：

```
<pre class="wp-block-preformatted">html{background:#999;margin:100px; border:30px solid #093;}
body{background:#069; margin:100px; border:30px solid #093;}
```

html标签也增加了margin间距和30像素的边框，结果如下：  
IE6下：

<figure class="wp-block-image">![IE6浏览器下html对margin不敏感不支持](http://image.zhangxinxu.com/image/blog/200909/2009-09-18_190950.png)</figure>IE6浏览器下html对margin不敏感不支持

Firefox浏览器下：

<figure class="wp-block-image">![Firefox浏览器下html标签支持margin外边距](http://image.zhangxinxu.com/image/blog/200909/2009-09-18_191216.png)</figure>Firefox浏览器下html标签支持margin外边距

这是缩放到`500`像素宽的图片，仔细看还是会发现IE6下`margin`没有起作用，顶边的是30像素的边框，Firefox下`margin`起作用了，可以看到`30`像素的边框外部还有一段间距。此图`<html>`标签背景色的全屏显示也进一步证明了我上面有关背景色显示原理的推论。

#### 3.关于滚动条

打开一个空白页面，观察浏览器右侧，会发现IE浏览器会有一段滚动条的槽道，而Firefox浏览器下没有。

<figure class="wp-block-image">![空白页面下IE和Firefox火狐浏览器左侧的滚动条与否](http://image.zhangxinxu.com/image/blog/200909/2009-09-18_192701.png)</figure>空白页面下IE和Firefox火狐浏览器左侧的滚动条与否

Firefox下没有滚动条，有时候会产生体验上的一些问题，比如：假设一个页面高度有限，无滚动条，当鼠标移到一个元素上，要显示一个浮动层，但是这个浮动层有一定的高度，加上鼠标位置偏低，一旦浮动层出现，说不定页面的高度被撑高并出现滚动条，这是在Firefox浏览器下就会产生页面的晃动，原因是滚动条的出现导致页面的宽度减小，布局发生一些偏移，而这种便宜造成的体验是不好的。如何避免呢？很多人会想到设置

```
<pre class="wp-block-preformatted">body{overflow-y:scroll;}
```

Firefox下是出现了滚动槽了，但是IE下出现了两个滚动条，如下：

<figure class="wp-block-image">![IE6下的双滚动条](http://image.zhangxinxu.com/image/blog/200909/2009-09-18_194201.png)</figure>可能有人会想到hack，no，no，no，其实仔细想一想，问题很简单。为什么IE会产生双固定条，里面那一个肯定是`<body>`的，那么外面的那一个呢？啊，或许您想到了，`<html>`标签的，就是说默认状态下，IE6下html有个o`verflow:scroll`声明，证明很简单，您设置`<html>`标签`overflow:hidden`看滚动条是否没有了（我这里证明是没有了）。所以呢，只要在css中些写上：

```
<pre class="wp-block-preformatted">html{overflow-y:scroll;}
```

就可以让IE和Firefox（包括chrome）浏览器默认产生滚动条的滚动槽了。

#### 4.关于background的fixed固定定位

Firefox是支持`background:fixed`定位的，IE6只能说是半支持，好吧，这么说吧，可能不太严谨，就是背景图片固定的效果似乎只在根结点起作用。举个很简单的例子：  
先看这段css代码：

```
<pre class="wp-block-preformatted">body{background:url(../image/404.png) no-repeat fixed center center;}
div{height:2000px;}
```

HTML部分为：

```
<pre class="wp-block-preformatted"><body><div></div></body>
```

其结果是无论IE6还是火狐浏览器下，背景图片都是固定的死死的，不错。但是，一旦`<html>`标签带着`background`属性参合进来，事情就要发生转变了。问题代码：

```
<pre class="wp-block-preformatted">html{background:white;}
body{background:url(../image/404.png) no-repeat fixed center center;}
div{height:2000px;}
```

结果IE6下，背景不固定了，只看到背景图片随着滚动条上下移动而移动。[演示页面](https://www.zhangxinxu.com/study/200909/html-body-study-simple.html)

要解决这个问题呢，也是有办法的，就是将`fixed`属性值转移到`html`标签上就可以了。即：

```
<pre class="wp-block-preformatted">html{background:white url(../image/404.png) no-repeat fixed center center;}
div{height:2000px;}
```

就可以了。

#### 5.关于height:100%;

要想高度百分比起作用，一般来说，要满足两个条件：其一，父标签有高度可寻，就是向上遍历父标签要找到一个定值高度（`body`，`html`另外讨论），如果中途有个`height`为`auto`或是没有设置`height`属性，则高度百分比不起作用；其二，标签本身的属性，如果`inline`属性的标签，如果没有浮动，`zoom`，或是绝对定位之类属性是不支持百分比高度的，`block`或`inline-block`属性可以说是高度百分比起作用的前提条件之一吧。

而这里要讲的是关于body和html的高度百分比显示的。

默认状态下，`<body>`不是高度100%显示的，不要看`<body>`定义`background`属性好像`<body>`就是满屏显示的，正如上面所推断的，此背景已非`<body>`之背景。用下面这个一测便知。

```
<pre class="wp-block-preformatted">body{background:#039; border:50px solid #C00;}
```

看边框范围是否高度`100%`显示，答案是否定的。见下图（截自IE6，Firefox浏览器下表现一致）：

<figure class="wp-block-image">![body默认高度是不100%显示的](https://image.zhangxinxu.com/image/blog/200909/2009-09-18_203335.png)</figure>body默认高度是不100%显示的

那么`<body>`是否支持`height:100%`呢？经过我的测试，IE6支持，Firefox浏览器不支持。

要想让Firefox浏览器也支持`<body>`的`height:100%`是简单的，就是设置`<html>`标签`height:100%`，一旦设置了`height:100%`则无论哪个浏览器下`<body>`都支持`height:100%`了，而`<body>`内部的容器也可以支持`height:100%`了。

前段时间看到百度的一道面试题，说什么透明层无论滚动与否都满屏显示，其实就是对`<html>`和`<body>`标签做一番手脚，两者高度`100%`显示，同时溢出隐藏（`overflow:hidden`），然后用一个`<div>`高度`100%`显示，溢出滚动。而这个透明层就使用绝对定位且与这个`<div>`平级，高宽`100%`显示，就可以使得无论怎么滚动这个透明覆盖层都是满屏显示的。这其实也就解决IE6下浮动层固定定位的经典方法。

#### 6.关于html，body的css hack

不知不觉讲了不少了，这就算最后一条吧。

我见过的有关`<html>`和`<body>`标签的hack有这么几个，一个是`* html body`，一个是`*+html`，还有个是`html > body`，以及`body:nth-of-type(1)`。前者是经典的IE6 css hack，在当前主流浏览器中，就IE6支持，其含义是指在`<html>`标签外还有一个隐藏的幽灵标签，我也不知道什么东西，反正IE6认得它就行了。`*+html`据说只有IE7认得它。`html > body`也据说就IE7认识它，而`body:nth-of-type(1)`是针对chrome浏览器的hack，意思是说某某标签的第一个元素，因为一个页面就只有一个`<body>`标签，所以这个肯定会成立的，所以`body:nth-of-type(1) div{height:1px;}`这个hack就会起作用了。

#### 7. 关于body overflow隐藏 （新增与2019-03-01）

单纯如下body overflow是无法隐藏高度不足一屏的元素的，例如：

```
<pre class="wp-block-preformatted">body {
    height: 30px;
    overflow: hidden;
}
body > div {
    height: 300px;
    background-color: #cd0000;
}
```

此时body的子元素div依然保持300px高度的显示，如果想要隐藏，需要设置html的`overflow:hidden`。

并不是说body overflow无效，而是body天然的overflow计算容器是一屏高度，因此，如果div高度很高，例如3000px：

```
<pre class="wp-block-preformatted">body > div {
    height: 3000px;
    background-color: #cd0000;
}
```

大家可以看到body的子元素div也就显示了屏幕的高度。

### 三、最后的结语

技术无止境，css这东西真是很博大精深，即使我键盘敲了不知多少个小时，还是有些话没有说完。要是再结合JavaScript，demo展开说的话，可能要通宵才能说完。这里就先讲这些，点到为止。关于html和body这两个标签里面蕴含的知识是很多的，我这里全当抛砖引玉，更多的内容还需要您去研究去探索。

</body>