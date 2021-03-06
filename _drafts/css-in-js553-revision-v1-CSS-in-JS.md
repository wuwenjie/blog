---
id: 554
title: 'CSS in JS'
date: '2019-08-18T00:41:04+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/553-revision-v1/'
permalink: '/?p=554'
---

1、以前，网页开发有一个原则，叫做[“关注点分离”](https://en.wikipedia.org/wiki/Separation_of_concerns#HTML.2C_CSS.2C_JavaScript)（separation of concerns）。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017040604.jpg)</figure>它的意思是，各种技术只负责自己的领域，不要混合在一起，形成耦合。对于网页开发来说，主要是三种技术分离。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017040605.jpg)</figure>- HTML 语言：负责网页的结构，又称语义层
- CSS 语言：负责网页的样式，又称视觉层
- JavaScript 语言：负责网页的逻辑和交互，又称逻辑层或交互层

简单说，就是一句话，不要写”行内样式”（inline style）和”行内脚本”（inline script）。比如，下面代码就很糟糕（查看[完整代码](http://jsbin.com/wagizup/1/edit?html,output)）。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<h1 style="color:red;font-size:46px;"  onclick="alert('Hi')">
  Hello World
</h1>
```

2、

[React](http://www.ruanyifeng.com/blog/2015/03/react.html) 出现以后，这个原则不再适用了。因为，React 是组件结构，强制要求把 HTML、CSS、JavaScript 写在一起。

上面的例子使用 React 改写如下（查看[完整代码](http://jsbin.com/xicelin/1/edit?html,js,output)）。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
const style = {
  'color': 'red',
  'fontSize': '46px'
};

const clickHandler = () => alert('hi'); 

ReactDOM.render(
  <h1 style={style} onclick={clickHandler}>
     Hello, world!
  </h1>,
  document.getElementById('example')
);
```

上面代码在一个文件里面，封装了结构、样式和逻辑，完全违背了”关注点分离”的原则，很多人不适应。

但是，这有利于组件的隔离。每个组件包含了所有需要用到的代码，不依赖外部，组件之间没有耦合，很方便复用。所以，随着 React 的走红和组件模式深入人心，这种”关注点混合”的新写法逐渐成为主流。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017040601.png)</figure>3、

表面上，React 的写法是 HTML、CSS、JavaScript 混合在一起。但是，实际上不是。现在其实是用 JavaScript 在写 HTML 和 CSS。

React 在 JavaScript 里面实现了对 HTML 和 CSS 的封装，我们通过封装去操作 HTML 和 CSS。也就是说，网页的结构和样式都通过 JavaScript 操作。

4、

React 对 HTML 的封装是 [JSX 语言](https://jsx.github.io/) ，这个在各种 React 教程都有详细介绍，本文不再涉及了，下面来看 React 对 CSS 的封装。

React 对 CSS 封装非常简单，就是沿用了 DOM 的 [style 属性对象](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style)，这个在前面已经看到过了。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
const style = {
  'color': 'red',
  'fontSize': '46px'
};
```

上面代码中，CSS 的`font-size`属性要写成`fontSize`，这是 JavaScript 操作 CSS 属性的[约定](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Properties_Reference)。

由于 CSS 的封装非常弱，导致了一系列的第三方库，用来加强 React 的 CSS 操作。它们统称为 CSS in JS，意思就是使用 JS 语言写 CSS。根据不完全统计，各种 CSS in JS 的库至少有[47种](https://github.com/MicheleBertoli/css-in-js)。老实说，现在也看不出来，哪一个库会变成主流。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017040602.jpg)</figure>你可能会问，它们与”CSS 预处理器”（比如 Less 和 [Sass](http://www.ruanyifeng.com/blog/2012/06/sass.html)，包括 PostCSS）有什么区别？回答是 CSS in JS 使用 JavaScript 的语法，是 JavaScript 脚本的一部分，不用从头学习一套专用的 API，也不会多一道编译步骤。

5、

上周，我看到一个新的 CSS in JS 库，叫做 [polished.js](https://polished.js.org/)。它将一些常用的 CSS 属性封装成函数，用起来非常方便，充分体现使用 JavaScript 语言写 CSS 的优势。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017040606.png)</figure>我觉得这个库很值得推荐，这篇文章的主要目的，就是想从这个库来看怎么使用 CSS in JS。

首先，加载 polished.js。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
const polished = require('polished');
```

如果是浏览器，插入下面的脚本。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<script src="https://unpkg.com/polished@1.0.0/dist/polished.min.js">
</script>
```

`polished.js`目前有50多个方法，比如`clearfix`方法用来清理浮动。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
const styles = {
  ...polished.clearFix(),
};
```

上面代码中，`clearFix`就是一个普通的 JavaScript 函数，返回一个对象。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
polished.clearFix()
// {
//  &::after: {
//    clear: "both",
//    content: "",
//    display: "table"
//  }
// }
```

“展开运算符”（`...`）将`clearFix`返回的对象展开，便于与其他 CSS 属性混合。然后，将样式对象赋给 React 组件的`style`属性，这个组件就能清理浮动了。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
ReactDOM.render(
  <h1 style={style}>Hello, React!</h1>,
  document.getElementById('example')
);
```

从这个例子，大家应该能够体会`polished.js`的用法。

6、

下面再看几个很有用的函数。

`ellipsis`将超过指定长度的文本，使用省略号替代（查看[完整代码](https://jsbin.com/heyuri/edit?js,output)）。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
const styles = {
  ...polished.ellipsis('200px')
}

// 返回值
// {
//   'display': 'inline-block',
//   'max-width': '250px',
//   'overflow': 'hidden',
//   'text-overflow': 'ellipsis',
//   'white-space': 'nowrap',
//   'word-wrap': 'normal'
// }
```

`hideText`用于隐藏文本，显示图片。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
const styles = {
  'background-image': 'url(logo.png)',
  ...polished.hideText(),
};

// 返回值
// {
  'background-image': 'url(logo.png)',
  'text-indent': '101%',
  'overflow': 'hidden',
  'white-space': 'nowrap',
}
```

`hiDPI`指定高分屏样式。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
const styles = {
 [polished.hiDPI(1.5)]: {
   width: '200px',
 }
};

// 返回值
//'@media only screen and (-webkit-min-device-pixel-ratio: 1.5),
// only screen and (min--moz-device-pixel-ratio: 1.5),
// only screen and (-o-min-device-pixel-ratio: 1.5/1),
// only screen and (min-resolution: 144dpi),
// only screen and (min-resolution: 1.5dppx)': {
//  'width': '200px',
//}
```

`retinaImage`为高分屏和低分屏设置不同的背景图。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
const styles = {
 ...polished.retinaImage('my-img')
};

// 返回值
//   backgroundImage: 'url(my-img.png)',
//  '@media only screen and (-webkit-min-device-pixel-ratio: 1.3),
//   only screen and (min--moz-device-pixel-ratio: 1.3),
//   only screen and (-o-min-device-pixel-ratio: 1.3/1),
//   only screen and (min-resolution: 144dpi),
//   only screen and (min-resolution: 1.5dppx)': {
//    backgroundImage: 'url(my-img_2x.png)',
//  }
```

7、

`polished.js`提供的其他方法如下，详细用法请参考[文档](https://polished.js.org/docs/)。

- `normalize()`：样式表初始化
- `placeholder()`：对 placeholder 伪元素设置样式
- `selection()`：对 selection 伪元素设置样式
- `darken()`：调节颜色深浅
- `lighten()`：调节颜色深浅
- `desaturate()`：降低颜色的饱和度
- `saturate()`：增加颜色的饱和度
- `opacify()`：调节透明度
- `complement()`：返回互补色
- `grayscale()`：将一个颜色转为灰度
- `rgb()`：指定红、绿、蓝三个值，返回一个颜色
- `rgba()`：指定红、绿、蓝和透明度四个值，返回一个颜色
- `hsl()`：指定色调、饱和度和亮度三个值，返回一个颜色
- `hsla()`：指定色调、饱和度、亮度和透明度三个值，返回一个颜色
- `mix()`：混合两种颜色
- `em()`：将像素转为 em
- `rem()`：将像素转为 rem

目前，`polished.js`只是1.0版，以后应该会有越来越多的方法。

8、

`polished.js`还有一个特色：所有函数默认都是柯里化的，因此可以进行函数组合运算，定制出自己想要的函数。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
import { compose } from 'ramda';
import { lighten, desaturate } from 'polished';

const tone = compose(lighten(10), desaturate(10))
```

上面代码使用 Ramda 函数库完成组合运算。Ramda 的用法可以参考我写的[教程](http://www.ruanyifeng.com/blog/2017/03/ramda.html)。