---
id: 421
title: 'Array find方法在UC浏览器未定义'
date: '2017-04-17T14:58:08+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=421'
permalink: /2017/04/array-find-in-ucbrowser
categories:
    - Javascript
---

今天发现用UC浏览器访问一个项目出现bug，后来检查出来原来UC浏览器并没有find方法（但它有filter方法），所以写了以下垫片。。。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">/*
*  findIndex (UCbrowser)
*/
if (![].findIndex) {
    Array.prototype.findIndex = function (predicate) {
        const array = this;
        for (let i = 0, value; i < array.length; i++) {
            value = array[i];
            if (predicate.apply(array, [value, i, array])) {
                return i;
            }
        }
        return -1;
    };
}

/*
*  find (UCbrowser)
*/
if (![].find) {
    Array.prototype.find = function (predicate) {
        return this[this.findIndex(predicate)];
    };
}
```