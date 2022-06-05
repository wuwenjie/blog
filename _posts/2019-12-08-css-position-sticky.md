---
id: 917
title: 'position:sticky的尝试'
date: '2019-12-08T03:01:08+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=917'
permalink: /2019/12/css-position-sticky
categories:
    - 'HTML&amp;CSS'
---

## 前言

sticky这种设计效果是经常出现的，比如陶宝右侧的工具栏，当我们向下滚动到它的位置时，它就会黏住顶部跟随滚动，类似position: fixed的效果，只不过它的触发条件是当我们滚动到所在位置时，才触发fixed的效果的：

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2019/12/stikcy-sidebar.png)</figure>我们经常的做法是用JavaScript去监听滚动事件然后进行处理，比如会用到类似stickyjs的一些插件 <http://stickyjs.com/> ，从2017年左右开始css中的position:sticky就是为了这中设计而诞生的，今天我们来认识一下它。

## 兼容性

差不多两年时间了，兼容性还算可以的，对于那种面向技术人员，后台管理人员的项目，我倒是觉得可以用上，毕竟他们只是升级一下浏览器不仅能体验更好的效果，也能降低码农的工作量：

<https://caniuse.com/#search=position%3A%20sticky>

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2019/12/sticky-compatable-1024x433.png)</figure>## 学习使用

我们来实现陶宝右侧的效果，就特别简单了，没什么好学的，直接设置就行了：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="css" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.sidebar {
    position: -webkit-sticky;
    position: sticky;
    top: 0;
}
```

简单例子: <https://jsbin.com/cukoliwafu/edit?html,css,output>

还有一种设计效果，比如叠加效果也能实现，还有很多效果，具体大家可以根据sticky特性自由发挥： 叠加效果： <https://jsbin.com/fegiqoquki/edit?html,css,output>

## 特性

`position:sticky`有个非常重要的特性，那就是**sticky元素效果完全受制于父级元素们们们**。如果你发现你设置了不起效果，可以检查以下两个原因：

1. 父级元素不能有任何`overflow:visible`以外的overflow设置，否则没有效果，因为改变了滚动容器（即使没有出现滚动条）。
2. 父级元素高度过小，根本不够stikcy块的滚动，就会没有效果。

## 唠叨

近几年Web开发语言都在互相协作着，这种协作是一种促进，有时候我们总调侃学不动了，一直在变化，可是哪知道这些变化反而解决了我们的痛点，也许你习惯了，觉得不是痛点不去学，但是不能否认你学会之后，你会说以前太痛了，再也不想回去了，就像学会vue，react, angular 等，再回去jquery真的觉得有点痛苦了，但你不想学之前，总觉得jQuery不怎么痛，你习惯了。

前后端的分离让 JS接替了部分后端语言的工作，比如数据绑定交互等；css接替了部分JS工作，比如动画和各种常见设计效果等，各种语言都在共同协作着，只是为了解决码农痛点，我们能做的就是抽空瞄一眼，试一试，学一学 ，利永远大于弊，学习的时间不仅可以为你大大的节省开发时间，更会让你拥有愉悦的编程体验 🙂