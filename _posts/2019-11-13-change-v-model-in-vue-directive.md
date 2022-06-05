---
id: 879
title: 'Vue directive中修改v-model值'
date: '2019-11-13T00:45:36+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=879'
permalink: /2019/11/change-v-model-in-vue-directive
categories:
    - Javascript
---

最近在重构一个旧项目，这个项目用到了一些bootstrap的插件，我们想保留这些插件，就会遇到一些问题，比如我们把插件封装成了directive，在双向绑定方面需要做如下处理：

1. &lt;div v-custom= “customData”&gt;Test&lt;/div&gt; ， 我们想要在directive中，对customData进行改变，可以使用 vnode.context\[binding.expression\]进行赋值即可，如下例子：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""> Vue.directive("custom", {
  bind: function(element, binding, vnode) {
    $(element).on("click", function() {
      vnode.context[binding.expression] = false; // sync binding.value = false;
    });
  }
});
```

 还有一种做法是把customData放入一个对象，因为对象是引用类型，所以在directive中直接修改也能改到，如 &lt;div v-custom= “{ data ：customData}”&gt;Test&lt;/div&gt;

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">Vue.directive("custom", {
  bind: function(element, binding, vnode) {
    $(element).on("click", function() {
     binding.value.data.customData = false;
    });
  }
});
```

2\. &lt;input v-model=”customValue” v-custom= “customData” /&gt; ， 我们知道v-model其实是input事件改变value的语法糖，所以我们想要在directive中对v-model进行改变，可以使用dispatchEvent(‘input’)的方法，如下例子：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""> // 这个函数是从vue.js源码中复制的，方便我们手动触发事件
function trigger(el, type) {
    var e = document.createEvent("HTMLEvents");
    e.initEvent(type, true, true);
    el.dispatchEvent(e);
  }
 Vue.directive("custom", {
  bind: function(element, binding, vnode) {
    $(element).on("focus", function() {
       element.value = 'FOCUS';
       trigger(element, "input");
    });
   }
});
```