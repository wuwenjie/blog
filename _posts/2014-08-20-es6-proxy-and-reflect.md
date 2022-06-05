---
id: 423
title: ES6中的Proxy和Reflect
date: '2014-08-20T15:02:55+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=423'
permalink: /2014/08/es6-proxy-and-reflect
categories:
    - Javascript
---

**一、Proxy**  
1、概述  
Proxy取其英文意思即**“代理”**。  
所谓代理，是你要取得某样东西或对其进行某些操作的**中间媒介**，而不是直接作用在这个对象上。这就类似我们网购东西，需要在网店平台上购买，而不是直接向厂家购买。  
Proxy 对象就是这样的媒介，要操作这个对象的话，需要经过这个媒介的同意。  
使用方式： **let p = new Proxy(target, habdler);**  
**target：用 Proxy 包装的目标对象（可以是数组对象，函数，或者另一个代理）；**  
**handler：一个对象，拦截过滤代理操作的函数。**

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let obj = {
  name: "猿跑跑",
  age:28
}
  
let p = new Proxy(obj, {
  get: function (target, key) {
    if (key in target) {
      return target[key]
    } else {
      console.log("对象没有此属性");
    }
  },
  set: function (target, key, value) {
    if (key == "age" & value < 1) {
      console.log("参数有误");
    } else {
      target[key] = value;
    }
  }
});
p.age = -1;
p.age = 22;
console.log(p.age);
console.log(p.name);
// 参数有误
// 22
// 猿跑跑
```

**2、实例方法**  
除了上面代码中set 和 get 两个实例方法外，Proxy 对象实例方法如下表显示：

| 方法 | 描述 |
|---|---|
| [handler.apply()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/apply) | 拦截 Proxy 实例作为函数调用的操作 |
| [handler.construct()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/construct) | 拦截 Proxy 实例作为函数调用的操作 |
| [handler.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/defineProperty) | 拦截 Object.defineProperty() 的操作 |
| [handler.deleteProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/deleteProperty) | 拦截 Proxy 实例删除属性操作 |
| [handler.get()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/get) | 拦截 读取属性的操作 |
| [handler.set()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/set) | 拦截 属性赋值的操作 |
| [handler.getOwnPropertyDescriptor()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/getOwnPropertyDescriptor) | 拦截 Object.getOwnPropertyDescriptor() 的操作 |
| [handler.getPrototypeOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/getPrototypeOf) | 拦截 获取原型对象的操作 |
| [handler.has()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/has) | 拦截 属性检索操作 |
| [handler.isExtensible()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/isExtensible) | 拦截 Object.isExtensible()操作 |
| [handler.ownKeys()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/ownKeys) | 拦截 Object.getOwnPropertyDescriptor() 的操作 |
| [handler.preventExtension()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/preventExtensions) | 拦截 Object().preventExtension() 操作 |
| [handler.setPrototypeOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/setPrototypeOf) | 拦截Object.setPrototypeOf()操作 |
| [Proxy.revocable()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy/revocable) | 创建一个可取消的 Proxy 实例 |

**二、Reflect**  
1、概述  
与Proxy相同，也是ES6新增。  
它新增了一些方法，这些方法可以使一些操作更加规范化，更加便利。对象有如下特点:  
**（1）.只要Proxy对象具有的代理方法，Reflect对象全部具有，以静态方法的形式存在。这些方法能够执行默认行为，无论Proxy怎么修改默认行为，总是可以通过Reflect对应的方法获取默认行为。  
（2）.新增的方法与现有一些方法功能重复，新增的方法会取代现有的方法**，比如Reflect.getPrototypeOf()，Object对象具有同样的方法，功能也相同，但是将getPrototypeOf()方法移植到Reflect更加合理。还有一些新增方法用来取代现有的命令式操作，比如判断属性是否存在的in命令，用Reflect.has()方法替代。  
**2、实例方法**

| 方法 | 描述 |
|---|---|
| [handler.apply()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/apply) | 通过指定的参数列表发起对目标函数的调用 |
| [handler.construct()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/construct) | 此方法行为有点像 new 操作符构造函数，相当于运行 new target(…args) |
| [handler.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/defineProperty) | 方法功能类似于Object.defineProperty()方法 |
| [handler.deleteProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/deleteProperty) | 功能类似于delete运算符 |
| [handler.get()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/get) | 从对象获取指定属性值 |
| [handler.set()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/set) | 设置指定对象的属性，比如为对象添加新属性或者修改原有属性的值 |
| [handler.getOwnPropertyDescriptor()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/getOwnPropertyDescriptor) | 功能类似于Object.getOwnPropertyDescriptor() |
| [handler.getPrototypeOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/getPrototypeOf) | 获取对象的原型对象 |
| [handler.has()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/has) | 获取对象的原型对象 |
| [handler.isExtensible()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/isExtensible) | 判断一个对象是否是可扩展的 |
| [handler.ownKeys()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys) | 返回一个数组，此数组中包含有参数对象自有属性名称 |
| [handler.preventExtension()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/preventExtensions) | 将对象设置为不可扩展 |
| [handler.setPrototypeOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/setPrototypeOf) | 设置指定对象的原型对象 |

三、应用实例  
1、操作节点（切换两个不同的元素的属性或类名）

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let view = new Proxy({
  selected: null
},
{
  set: function(obj, prop, newval) {
    let oldval = obj[prop];

    if (prop === 'selected') {
      if (oldval) {
        oldval.setAttribute('aria-selected', 'false');
      }
      if (newval) {
        newval.setAttribute('aria-selected', 'true');
      }
    }

    // The default behavior to store the value
    obj[prop] = newval;
  }
});

let i1 = view.selected = document.getElementById('item-1');
console.log(i1.getAttribute('aria-selected')); // 'true'

let i2 = view.selected = document.getElementById('item-2');
console.log(i1.getAttribute('aria-selected')); // 'false'
console.log(i2.getAttribute('aria-selected')); // 'true'
```

2、对象多重继承

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var obj1 = {
    name: "obj-1",
    foo() {
        console.log( "obj1.foo:", this.name );
    }
},
obj2 = {
    name: "obj-2",
    foo() {
        console.log( "obj2.foo:", this.name );
    },
    bar() {
        console.log( "obj2.bar:", this.name );
    }
},
handlers = {
    get(target,key,context) {
        if (Reflect.has( target, key )) {
            return Reflect.get(target, key, context);
        }
        else {
            for (var P of target[Symbol.for( "[[Prototype]]" )]) {
                if (Reflect.has( P, key )) {
                    return Reflect.get(P, key, context);
                }
            }
        }
    }
},
obj3 = new Proxy({
    name: "obj-3",
    baz() {
        this.foo();
        this.bar();
    }
},handlers);

obj3[Symbol.for("[[Prototype]]")] = [obj1, obj2];
obj3.baz();
//obj1.foo:obj-3
//obj2.bar:obj-3
```

如果我们要实现对象间的单继承，比如obj3继承在obj1，可以使用Object.setPrototypeOf方法，但是没法实现多继承。所以上面的代码中用了一个自定义的属性Symbol.for(“\[\[Prototype\]\]”)来表示要继承的多个父对象。  
然后用Proxy来拦截所有obj3中的get请求，先检查obj3中是否有相应的属性或者方法，使用的就是Reflect.has方法，如果有，就直接转发；如果没有，就遍历父对象列表，在父对象中逐个检查是否有相应的属性或者方法，有就调用。如果都没有，那get就相当于返回undefined了。逻辑还是非常好理解的，代码也比较清楚，应该不需要太多的解释。

作者：贵在随心  
链接：https://www.jianshu.com/p/d36b06a8010e  
来源：简书  
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。