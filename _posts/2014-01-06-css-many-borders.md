---
id: 358
title: CSS多边框的几种实现方法
date: '2014-01-06T00:19:44+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=358'
permalink: /2014/01/css-many-borders
categories:
    - 'HTML&amp;CSS'
---

css3中我们知道可以使用box-shadow属性轻松的为元素添加阴影效果，并且可以设置多组效果，每组参数值用逗号隔开。如果把box-shadow特性的两个偏移量 h-shadow 、v-shadow设置为0，将模糊值blur也设置为0，此时增加扩张半径，就会使元素的投影变为实线边框。这样就可以模拟多边框效果了，首先看下box-shadow语法和参数。

## box-shadow语法： 

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">box-shadow：x-shadow  y-shadow  blur  spread  color  inset;
```

参数说明：

- x-shadow：设置对象的阴影水平偏移值，单位可以是px、em或百分比等，允许负值。
- y-shadow：设置对象的阴影垂直偏移值，单位可以是px、em或百分比等，允许负值。
- blur：用于设置边框阴影半径大小。
- spread：扩展半径，设置阴影的尺寸；这个参数可选，缺省时值为0。
- color：设置阴影的颜色；
- inset：这个参数默认不设置。默认情况下为外阴影，inset表示内阴影。

## box-shadow实现多边框：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">div{  
     box-shadow: 0 0 0 10px red,
　　　　　　　   0 0 0 16px green,
　　　　　　　   0 2px 5px 16px rgba(0,0,0,.5);     
     background: yellowgreen;
 }
```

效果如下：

<figure class="wp-block-image">![](http://api.fly63.com/vue_blog/public/Uploads/20180319/5aaf6a9e09875.jpg)</figure>## outline实现

如果我们只需要2层边框，那么使用outline是不错的选择，先设置常规的border边框，再加上outline（描边）。而且outline比上面的box-shadow还有一大优点就是，可以生成虚线等边框。通常outline属性需要和对应的描边偏移outline-offset来实现。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">div{
    width: 200px;
    height: 100px;
    background: #ffffff;
    border: 5px solid #53cfa2;
    outline: #736e21 dashed 1px;
    outline-offset: -20px;
}
```

效果如下：

<figure class="wp-block-image">![](http://api.fly63.com/vue_blog/public/Uploads/20180319/5aaf6bca88356.jpg)</figure>