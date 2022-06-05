---
id: 371
title: height百分比以及高度自适应问题
date: '2019-08-17T10:37:25+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/370-revision-v1/'
permalink: '/?p=371'
---

1\. 你曾经是否说想要 高度占页面或者占div百分比无效的问题，相信你也搜索过了，就是说 需要 设置父亲父亲一直到祖宗html都要设置百分比，才有效果。

 总之一句话：想用百分比设置他的高度，则它的父亲中至少有一个是固定高度，不然的话 全部父亲直到祖宗html（因为html外面就是浏览器，它是有高度的）**都必须**设置上百分比。 当然要排除那些脱离文档流的div,比如position:absolute,fixed，可以认为他们的父级就是浏览器，所以height百分比总是有效的，因为浏览器的高度是可以直到的啊。

2\. 有时候 并不是直接用百分比，可能有部分margin啊，padding啊需要扣除掉，这时候 可以用 height: calc(100% – 1rem); height: calc(50% – 2px); 等等，这种情况有效的条件 也是必须是 第一点说明的，因为它也是用到了height百分比啊。

3\. 有时候我们想通过设置百分比， 来维持高宽比例。 我们会想到用 width: 50%; height:50%; 这肯定不对的，因为height是相对容器高度的百分比，而宽是相对容器宽度的百分比。 两个参照物不一样，他们的比例不一定是1:1，而始终是外部容器的比例。因此我们的目的很简单，让他参照物相同。我们知道padding的参照物就是宽度的。那我们就用padding了。 原理很简单， 就是用padding-bottom去代替height。 比如博客园的logo我们想作为背景图，宽高=133/51， 如果我们把背景图的容器设置宽为50%，那么padding-bottom= 0.5\*51/133 = 19.172932330827066% ; 这样宽高比例就会固定住了。 知道了这个方法，可以做很多事情。

4\. 嗯、有些时候还可以考虑用 vh代替。。。

全部效果在这边，自己研究一下吧：

See the Pen [Height百分比](https://codepen.io/wenjie/pen/OgGNOj/) by wenjie ([@wenjie](https://codepen.io/wenjie)) on [CodePen](https://codepen.io/).