---
id: 420
title: 'ES6 reduce的一些例子'
date: '2019-08-17T14:50:42+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/418-revision-v1/'
permalink: '/?p=420'
---

reduce() 方法接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。reduce() 方法接受四个参数：初始值（或者上一次回调函数的返回值），当前元素值，当前索引，调用 reduce() 的数组。

reduce() 的几个强大用法：

## 数组求和

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var total = [ 0, 1, 2, 3 ].reduce(( acc, cur ) => {
    return acc + cur
}, 0);
console.log(total)   // 6
```

## 二维数组转为一维数组

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var array = [[1, 2], [3, 4], [5, 6]].reduce(( acc, cur ) => {
    return acc.concat(cur)
}, []);

console.log(array)  // [ 0, 1, 3, 4, 5, 6 ]
```

## 计算数组中每个元素出现的次数

### 1. 方法一

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let names = ['tom', 'jim', 'jack', 'tom', 'jack'];

const countNames = names.reduce((allNames, name) => {
  if (name in allNames) {
    allNames[name] ++;
  }
  else {
    allNames[name] = 1;
  }
  return allNames;
}, {});

console.log(countNames)  // { tom: 2, jim: 1, jack: 2 }
```

### 2. 方法二

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const arraySum = (arr, val) => arr.reduce((acc, cur) => {
    return cur == val ? acc + 1 : acc + 0
}, 0);

let arr = [ 0, 1, 3, 0, 2, 0, 2, 3 ]
console.log(arraySum(arr, 0)) // 数组arr中 0 元素出现的次数为3
```

## 数组去重

### 1.方法一

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let arr = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
let result = arr.sort().reduce((init, current) => {
    if (init.length === 0 || init[init.length - 1] !== current) {
        init.push(current);
    }
    return init;
}, []);
console.log(result); //[1,2,3,4,5]
```

### 2.方法二

当然，对于数组去重，也可以直接使用es6的…\[拓展运算符\] + Set 实现：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// console.log(...new Set([1,2,3,4,5,2,4,1]))

const dedupe = (array) => {
    return Array.from(new Set(array));
}
console.log(dedupe([1, 1, 2, 3])) //[1,2,3]
```