---
id: 665
title: JS深拷贝几种实现方法
date: '2018-12-23T00:11:55+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=665'
permalink: /2018/12/js-deepclone-methods
categories:
    - Javascript
---

## 1、使用递归（循环）的方式实现深拷贝

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">//使用递归的方式实现数组、对象的深拷贝
function deepClone1(obj) {
  //判断拷贝的要进行深拷贝的是数组还是对象，是数组的话进行数组拷贝，对象的话进行对象拷贝
  
  //进行深拷贝的不能为空，并且是对象或者是
  if (obj && typeof obj === "object") {
    var objClone = Array.isArray(obj) ? [] : {};
    for (key in obj) {
      if (obj.hasOwnProperty(key)) {
        if (obj[key] && typeof obj[key] === "object") {
          objClone[key] = deepClone1(obj[key]);
        } else {
          objClone[key] = obj[key];
        }
      }
    }
     return objClone;
  }
  return obj;
}
```

### 2、lodash函数库实现深拷贝

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">lodash很热门的函数库，提供了 lodash.cloneDeep()实现深拷贝
```

### 3、通过 JSON 对象实现深拷贝

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">//通过js的内置对象JSON来进行数组对象的深拷贝
function deepClone2(obj) {
  var _obj = JSON.stringify(obj),
    objClone = JSON.parse(_obj);
  return objClone;
}
```

### 4、Object.assign()拷贝（并不深，一级拷贝）

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">当对象中只有一级属性，没有二级属性的时候，此方法为深拷贝，但是对象中有对象的时候，此方法，在二级属性以后就是浅拷贝。
```