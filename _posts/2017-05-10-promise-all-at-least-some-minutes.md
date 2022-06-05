---
id: 550
title: 利用ES6的Promise.all实现至少请求多长时间
date: '2017-05-10T00:36:57+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=550'
permalink: /2017/05/promise-all-at-least-some-minutes
categories:
    - Javascript
---

### 1、背景

我们都知道ajax请求可以加个timeout，就是最多请求多少时间，如果超过这个时间直接就报错。 这个是最多请求多长时间，我现在要做的是，最少要请求多长时间，然后才能执行后续的逻辑。

比如，一个ajax请求 x 毫秒就执行完毕了，但我要让他至少执行1秒钟，那我们会这么想： ajax完成后 ， 1. 如果x&lt;1s, 那我们先setTimeout =&gt; 1s – x ，然后执行后续操作。 2 如果x&gt;=1s, 那我们直接执行后续操作。 想想这可繁琐了，我们还要在前面记录一下开始时间，后面记录一下结束时间，然后才能得到x。。。。

或者变量flag，ajax里面完成设置flag，setTimeout里面完成也设置flag等等等方法，都很繁琐、

### 2、Solution

现在ES6有个Promise.all，非常适合解决此类问题。直接这样 Promise.all(\[ajaxPromise(), waitPromise(1s)\]).then(()=&gt; 至少执行了1s) 。。。

如果说是 多个ajax（promise）按顺序执行，但总共加起来的时间至少是1s呢？ 那就用一个Promise把多个ajax包起来。然后 Promise.all(\[ajaxPromiseAll(), waitPromise(1s)\]).then(()=&gt; 至少执行了1s) 。。。

### 3、 讲得很抽象，实例为证

这个实例是这样的，微信里面有拆红包，当我们点击 開 的时候，那个字至少会完整的 翻一翻。那个完整翻一翻的时间我们假定需要1秒钟。 如果我们直接点击开的时候，立即请求ajax，等ajax完成立即拆开红包，这里的时间 有可能不足1s，那 就不能做到完整翻一翻。 如果请求大于1s，那就让它一直翻转吧，直到完成请求。所以我们为了解决这个问题，就需要用到上面的技术。

参考代码如下(Chrome最新版下测试)：

See the Pen [Promise.all的应用实例](https://codepen.io/wenjie/pen/rzZbGG/) by wenjie ([@wenjie](https://codepen.io/wenjie)) on [CodePen](https://codepen.io/).

因为async和await使用起来比Promise爽，所以我采用了这两个语法糖来写，用setTimeout来模拟ajax请求, ajax模拟A和 ajax模拟B有顺序关系的，比如先检测这个人是否还有机会打开红包，然后再请求打开红包获得随机红包金额 。