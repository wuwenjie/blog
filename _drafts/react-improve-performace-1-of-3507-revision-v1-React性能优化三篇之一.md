---
id: 508
title: React性能优化三篇之一
date: '2019-08-17T22:50:57+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/507-revision-v1/'
permalink: '/?p=508'
---

> Redux is a predictable state container for JavaScript apps.

简单的说就是Redux能够管理js app的状态，状态是由数据维护的，也就是说Redux是管理数据的。那么Redux是怎么管理数据的呢？

## store

一个Redux app中只有一个store，所有的数据都在这个store中，而通过`createStore(reducer, [initState])`，initState是可选参数，也就是说决定store的是reducer，reducer决定store中存放什么样的数据、处理什么样的数据、处理数据的方式。

store在创建的时候内部会执行`dispatch({ type: ActionTypes.INIT })`，用来初始化整个store的数据结构，同时获取reducer中的默认数据。之所以能拿到全部的数据结构，是因为在`dispatch({ type: ActionTypes.INIT })`的时候，所有的reducer都会执行，并根据reducer的combine结构生成数据。在Redux内，每执行一次dispatch，所有的reducer都会执行。

## reducer

所以这里就有个问题，如果reducer比较多的时候，性能是不是就会出问题。大家可能会想到减少reducer，这也是一个办法，但是如果刻意减少reducer的话，可能会导致某个reducer内过于复杂，后期难以维护。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const dispatcher = (state={}, action) {
  switch (action.type) {
    case "TYPE1": 
    case "TYPE2":
      return reducerFirst(state, action)
    case "TYPE3":
      return reducerSecond(state, action)
    default:
      return state
  }
}
```

通过这样简单的过滤就可以实现只让对action感兴趣的reducer执行，简单，方便，快捷。

## action

action是个object，它必须有个type属性，一般是个常量，来标示action类型，方便reducer处理。除去type剩下的一般就是要处理的数据，如果数据比较简单可以直接使用`Object.assign({}, state, action.data)`来处理，这种方法仅适合简单数据。

如果是复杂数据，有较深的层级，就要使用深度拷贝，这时候你可以使用[lodash](https://lodash.com/)的[cloneDeep](https://lodash.com/docs#cloneDeep)进行深度拷贝。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var objects = [{ 'a': 1 }, { 'b': 2 }];
var deep = _.cloneDeep(objects);
console.log(deep[0] === objects[0]);
// ➜ false
```

## immutable

但是复杂数据的深度拷贝是很花性能的，这个时候就可以使用**[immutable.js](https://facebook.github.io/immutable-js/)**来解决这个问题。immutable不可改变的意思，在Object-C中是原生提供这种数据类型的。对immutable.js生成的数据进行操作之后总是返回一个新的数据，原有的数据不会改变。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var Immutable = require('immutable');
var map1 = Immutable.Map({a:1, b:2, c:3});
var map2 = map1.set('b', 50);
map1.get('b'); // 2
map2.get('b'); // 50
```

immutable.js通过结构共享来解决的数据拷贝时的性能问题，数据被set的时候，immutable.js会只clone它的父级别以上的部分，其他保持不变，这样大家可以共享同样的部分，可以大大提高性能。如图

<figure class="wp-block-image">![immutable-js-share.gif](https://static.segmentfault.com/v-5d2ffc9a/global/img/squares.svg)</figure>## subscribe

每次执行dispatch，通过subscribe注册的listener都会被执行，如果listener较多，或者listener内部处理比较复杂也会对性能产生影响， 而且在listener内部很难区分哪个数据被改变了，如果在listener内部继续dispatch，而没有处理好执行dispatch条件的话，很容易造成dispatch-&gt;listener-&gt;dispatch死循环。所以建议通过middleware的方式来处理，而且在middleware内部可以知道action是什么，就可以只处理关心的action。

## 总结

***预分配reducer、精简reducer***

***精简action数据或使用immutable.js***

***使用middleware处理特殊需求(reducer中不方便处理的需求)***