---
id: 416
title: ES6中的几个常用特性
date: '2014-08-02T14:43:23+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=416'
permalink: /2014/08/es6-new-good-features
categories:
    - Javascript
    - 教程
---

1.Default Parameters（默认参数） in ES6

es6之前，定义默认参数的方法是在一个方法内部定义

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">1 var link = function (height, color, url) {
2     var height = height || 50;
3     var color = color || 'red';
4     var url = url || 'http://azat.co';
5     ...
6 }
```

es6写法，简单粗暴了很多了

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var link = function(height = 50, color = 'red', url = 'http://azat.co') {
  ...
}
```

2.Template Literals（模板对象） in ES6

es6之前定义模版字符串要这样写，其中first和last 是变量

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">1 var name = 'Your name is ' + first + ' ' + last + '.';
2 var url = 'http://localhost:3000/api/messages/' + id;
```

es6中使用新的语法${ }，就简单多啦，注意es6中的模版字符串用得是反引号“

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var name = `Your name is ${first} ${last}. `;
var url = `http://localhost:3000/api/messages/${id}`;
```

3.Multi-line Strings （多行字符串）in ES6

es6之前定义多行字符串要这样写

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">1 var roadPoem = 'Then took the other, as just as fair,nt'
2     + 'And having perhaps the better claimnt'
3     + 'Because it was grassy and wanted wear,nt'
4     + 'Though as for that the passing therent'
5     + 'Had worn them really about the same,nt';
6 var fourAgreements = 'You have the right to be you.n
7     You can only be you when you do your best.';
```

es6中的模版字符串也可以轻松实现，甚至插入变量也变得非常简单

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">1 var roadPoem = `Then took the other, as just as fair,
2     And having perhaps the better claim
3     Because it was grassy and wanted wear,
4     Though as for that the passing there
5     Had worn them really about the same,`;
6 var fourAgreements = `You have the right to be you.
7     You can only be you when you do your best.`;
```

  
4.Destructuring Assignment （解构赋值）in ES6

在es5中是这样写的

```
<pre class="wp-block-preformatted">1 var data = $('body').data(), // data has properties house and mouse
2    house = data.house,
3    mouse = data.mouse;
```

在nodejs中是这样的

```
<pre class="wp-block-preformatted">1 var jsonMiddleware = require('body-parser').jsonMiddleware ;
2 var body = req.body, // body has username and password
3    username = body.username,
4    password = body.password;
```

在es6中就简化了很多

```
<pre class="wp-block-preformatted">1 var { house, mouse} = $('body').data(); // we'll get house and mouse variables
2 var {jsonMiddleware} = require('body-parser');
3 var {username, password} = req.body;
```

同样适用于数组

```
<pre class="wp-block-preformatted">1 var [col1, col2]  = $('.column'),
2    [line1, line2, line3, , line5] = file.split('n');
```

5.Enhanced Object Literals （增强的对象字面量）in ES6

一个典型ES5对象文本，里面有一些方法和属性

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 var serviceBase = {port: 3000, url: 'azat.co'},
 2     getAccounts = function(){return [1,2,3]};
 3 var accountServiceES5 = {
 4   port: serviceBase.port,
 5   url: serviceBase.url,
 6   getAccounts: getAccounts,
 7    toString: function() {
 8       return JSON.stringify(this.valueOf());
 9   },
10   getUrl: function() {return "http://" + this.url + ':' + this.port},
11   valueOf_1_2_3: getAccounts()
12 }
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>我们想让它更有意思，我们可以用Object.create从serviceBase继承原型的方法：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">1 var accountServiceES5ObjectCreate = Object.create(serviceBase)
2 var accountServiceES5ObjectCreate = {
3   getAccounts: getAccounts,
4   toString: function() {
5     return JSON.stringify(this.valueOf());
6   },
7   getUrl: function() {return "http://" + this.url + ':' + this.port},
8   valueOf_1_2_3: getAccounts()
9 }
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>在ES6的对象文本中，既可以直接分配getAccounts: getAccounts,也可以只需用一个getAccounts，此外，我们在这里通过\_\_proto\_\_（并不是通过’proto’）设置属性，如下所示：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 var serviceBase = {port: 3000, url: 'azat.co'},
 2 getAccounts = function(){return [1,2,3]};
 3 var accountService = {
 4     __proto__: serviceBase,
 5     getAccounts,
 6     toString() {
 7      return JSON.stringify((super.valueOf()));
 8     },
 9     getUrl() {return "http://" + this.url + ':' + this.port},
10     [ 'valueOf_' + getAccounts().join('_') ]: getAccounts()
11 };
12 console.log(accountService)
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>ES6对象文本对于旧版的对象文本来说是一个很大的进步。

6.Arrow Functions in（箭头函数） ES6、

在ES6中，有了丰富的箭头函数。这些丰富的箭头是令人惊讶的因为它们将使许多操作变成现实，比如，以前我们使用闭包，this总是预期之外地产生改变，而箭头函数的迷人之处在于，现在你的this可以按照你的预期使用了，身处箭头函数里面，this还是原来的this。有了箭头函数在ES6中， 我们就不必用that = this或 self = this 或 \_this = this 或.bind(this)。例如，下面的代码用ES5就不是很优雅：

```
<pre class="wp-block-preformatted">1 var _this = this;
2 $('.btn').click(function(event){
3   _this.sendData();
4 })
```

在ES6中就不需要用 \_this = this：

```
<pre class="wp-block-preformatted">1 $('.btn').click((event) =>{
2   this.sendData();
3 })
```

一个另外的例子，我们通过call传递文本给logUpperCase() 函数在ES5中：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 var logUpperCase = function() {
 2   var _this = this;
 3  
 4   this.string = this.string.toUpperCase();
 5   return function () {
 6     return console.log(_this.string);
 7   }
 8 }
 9  
10 logUpperCase.call({ string: 'ES6 rocks' })();
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>而在ES6，我们并不需要用\_this浪费时间：

```
<pre class="wp-block-preformatted">1 var logUpperCase = function() {
2   this.string = this.string.toUpperCase();
3   return () => console.log(this.string);
4 }
5 logUpperCase.call({ string: 'ES6 rocks' })();
```

请注意，只要你愿意，在ES6中=&gt;可以混合和匹配老的函数一起使用。当在一行代码中用了箭头函数，它就变成了一个表达式。它将暗地里返回单个语句的结果。如果你超过了一行，将需要明确使用return。

这是用ES5代码创建一个消息数组：

```
<pre class="wp-block-preformatted">1 var ids = ['5632953c4e345e145fdf2df8','563295464e345e145fdf2df9'];
2 var messages = ids.map(function (value) {
3   return "ID is " + value; // explicit return
4 });
```

ES6是这样：

```
<pre class="wp-block-preformatted">1 var ids = ['5632953c4e345e145fdf2df8','563295464e345e145fdf2df9'];
2 var messages = ids.map(value => `ID is ${value}`); // implicit return
```

请注意，这里用了字符串模板。在箭头函数中，对于单个参数，括号()是可选的，但当你超过一个参数的时候你就需要他们。在ES5代码有明确的返回功能：

```
<pre class="wp-block-preformatted">1 var ids = ['5632953c4e345e145fdf2df8', '563295464e345e145fdf2df9'];
2 var messages = ids.map(function (value, index, list) {
3   return 'ID of ' + index + ' element is ' + value + ' '; // explicit return
4 });
```

在ES6中有更加严谨的版本，参数需要被包含在括号里并且它是隐式的返回：

```
<pre class="wp-block-preformatted">1 var ids = ['5632953c4e345e145fdf2df8','563295464e345e145fdf2df9'];
2 var messages = ids.map((value, index, list) => `ID of ${index} element is ${value} `); // implicit return
```

## 7. Promises in ES6，

es6中极为强大的promise

Promises 是一个有争议的话题。因此有许多略微不同的promise 实现语法。Q，bluebird，deferred.js，vow, avow, jquery 一些可以列出名字的。也有人说我们不需要promises，仅仅使用异步，生成器，回调等就够了。但令人高兴的是，在ES6中有标准的Promise实现。

下面是一个简单的用setTimeout()实现的异步延迟加载函数:

```
<pre class="wp-block-preformatted">1 setTimeout(function(){
2   console.log('Yay!');
3 }, 1000);
```

在ES6中，我们可以用promise重写:

```
<pre class="wp-block-preformatted">1 var wait1000 =  new Promise(function(resolve, reject) {
2   setTimeout(resolve, 1000);
3 }).then(function() {
4   console.log('Yay!');
5 });
```

或者用ES6的箭头函数：

```
<pre class="wp-block-preformatted">1 var wait1000 =  new Promise((resolve, reject)=> {
2   setTimeout(resolve, 1000);
3 }).then(()=> {
4   console.log('Yay!');
5 });
```

有人会说：脱裤子放屁，多此一举嘛！

但是：如果我们有更多的嵌套逻辑在setTimeout()回调函数中，我们将发现更多好处：

```
<pre class="wp-block-preformatted">1 setTimeout(function(){
2   console.log('Yay!');
3   setTimeout(function(){
4     console.log('Wheeyee!');
5   }, 1000)
6 }, 1000);
```

在ES6中我们可以用promises重写：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">1 var wait1000 =  ()=> new Promise((resolve, reject)=> {setTimeout(resolve, 1000)});
2 wait1000()
3     .then(function() {
4         console.log('Yay!')
5         return wait1000()
6     })
7     .then(function() {
8         console.log('Wheeyee!')
9     });
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>这种写法，方法的执行顺序和嵌套关系清晰明了，如果有多层嵌套，那你懂的！

## 8.Block-Scoped Constructs Let and Const（块作用域和构造let和const）

在ES6代码中，你可能已经看到那熟悉的身影let。在ES6里let并不是一个花俏的特性，它是更复杂的。Let是一种新的变量申明方式，它允许你把变量作用域控制在块级里面。我们用大括号定义代码块，在ES5中，块级作用域起不了任何作用：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 function calculateTotalAmount (vip) {
 2   var amount = 0;
 3   if (vip) {
 4     var amount = 1;
 5   }
 6   { // more crazy blocks!
 7     var amount = 100;
 8     {
 9       var amount = 1000;
10     }
11   }  
12   return amount;
13 }
14 console.log(calculateTotalAmount(true));   //输出1000
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>结果将返回1000，这真是一个bug。在ES6中，我们用let限制块级作用域。而var是限制函数作用域。

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">function calculateTotalAmount (vip) {
  var amount = 0; // probably should also be let, but you can mix var and let
  if (vip) {
    let amount = 1; // first amount is still 0
  } 
  { // more crazy blocks!
    let amount = 100; // first amount is still 0
    {
      let amount = 1000; // first amount is still 0
    }
  }  
  return amount;
}
 
console.log(calculateTotalAmount(true));  //0
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>## 9. Classes （类）in ES6

如果你喜欢面向对象编程（OOP），那么你将喜爱这个特性。以后写一个类和继承将变得容易。类的创建和使用真是一件令人头疼的事情在过去的ES5中，因为没有一个关键字class （它被保留，但是什么也不能做）。在此之上，大量的继承模型像[pseudo classical](http://javascript.info/tutorial/pseudo-classical-pattern), [classical](http://www.crockford.com/javascript/inheritance.html), [functional](http://javascript.info/tutorial/factory-constructor-pattern) 更加增加了混乱，JavaScript 之间的宗教战争只会更加火上浇油。用ES5写一个类，有很多种方法，这里就先不说了。现在就来看看如何用ES6写一个类吧。ES6没有用函数, 而是使用原型实现类。我们创建一个类baseModel ，并且在这个类里定义了一个constructor 和一个 getName()方法：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 class baseModel {
 2   constructor(options, data) { // class constructor，node.js 5.6暂时不支持options = {}, data = []这样传参
 3     this.name = 'Base';
 4     this.url = 'http://azat.co/api';
 5     this.data = data;
 6     this.options = options;
 7    }
 8  
 9     getName() { // class method
10         console.log(`Class name: ${this.name}`);
11     }
12 }
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>注意我们对options 和data使用了默认参数值。此外方法名也不需要加function关键字，而且冒号(：)也不需要了。另外一个大的区别就是你不需要分配属性this。现在设置一个属性的值，只需简单的在构造函数中分配。

AccountModel 从类baseModel 中继承而来:

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 class AccountModel extends baseModel {
 2     constructor(options, data) {
 3        super({private: true}, ['32113123123', '524214691']); 
 4        this.name = 'Account Model';
 5        this.url +='/accounts/';
 6     }
 7     get accountsData() { //calculated attribute getter
 8     // ... make XHR
 9         return this.data;
10     }
11 }
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>那么，你如何调用他们呢？它是非常容易的：

```
<pre class="wp-block-preformatted">1 let accounts = new AccountModel(5);
2 accounts.getName();
3 console.log('Data is %s', accounts.accountsData);
```

结果令人惊讶，输出是：Class name: Account Model Data is 32113123123,524214691

## 10. Modules （模块）in ES6

众所周知，在ES6以前JavaScript并不支持本地的模块。人们想出了AMD，RequireJS，CommonJS以及其它解决方法。现在ES6中可以用模块import 和export 操作了。在ES5中，你可以在 &lt;script&gt;中直接写可以运行的代码（简称IIFE），或者一些库像AMD。然而在ES6中，你可以用export导入你的类。下面举个例子，在ES5中,module.js有port变量和getAccounts 方法:

```
<pre class="wp-block-preformatted">1 module.exports = {
2   port: 3000,
3   getAccounts: function() {
4     ...
5   }
6 }
```

在ES5中，main.js需要依赖require(‘module’) 导入module.js：

```
<pre class="wp-block-preformatted">1 var service = require('module.js');
2 console.log(service.port); // 3000
```

但在ES6中，我们将用export and import。例如，这是我们用ES6 写的module.js文件库：

```
<pre class="wp-block-preformatted">1 export var port = 3000;
2 export function getAccounts(url) {
3   ...
4 }
```

如果用ES6来导入到文件main.js中，我们需用import {name} from ‘my-module’语法，例如：

```
<pre class="wp-block-preformatted">1 import {port, getAccounts} from 'module';
2 console.log(port); // 3000
```

或者我们可以在main.js中把整个模块导入, 并命名为 service：

```
<pre class="wp-block-preformatted">1 import * as service from 'module';
2 console.log(service.port); // 3000
```

这里还有许多ES6的其它特性你可能会使用到，排名不分先后：

1、全新的Math, Number, String, Array 和 Object 方法

2、二进制和八进制数据类型

3、默认参数不定参数扩展运算符

4、Symbols符号

5、tail调用

6、Generators (生成器)

7、New data structures like Map and Set(新的数据构造对像MAP和set)