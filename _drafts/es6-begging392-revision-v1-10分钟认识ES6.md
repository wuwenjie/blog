---
id: 393
title: 10分钟认识ES6
date: '2019-08-17T13:25:10+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/392-revision-v1/'
permalink: '/?p=393'
---

# 模块Module的引入

都说了复杂的大型应用了，所以我们第一个要讨论的重要特性就是模块概念，我们做一个复杂的项目必定需要两步走：

① 分得开，并且需要分开

② 合得起来

我们普遍认为没有复杂的应用，只有分不开的应用，再复杂的应用，一旦可以使用组件化、模块化的方式分成不同的小单元，那么其难度便会大大降低，模块化是大型、复杂项目的主要拦路虎。为了解决这个问题，社区制定了一些模块加载方案，对于浏览器开发来说，我们用的最多的是AMD规范，也就是大家熟知的requireJS，而ES6中在语音标准层面实现了模块功能，用以取代服务端通信的CommonJS和AMD规范，成为了通用的规范，多说无益，我们这里上一段代码说明：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 /*
 2 validate.js 多用于表单验证
 3 */
 4 export function isEmail (text) {
 5     var reg = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
 6     return reg.test(text);
 7 }
 8 
 9 export function  isPassword (text) {
10     var reg = /^[a-zA-Z0-9]{6,20}$/;
11     return reg.test(text);
12 }
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>那么我们现在想在页面里面使用这个工具类该怎么做呢：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 
 2 <html lang="en">
 3 <head>
 4     <meta charset="UTF-8">
 5     <title>Title</title>
 6 </head>
 7 <body>
 8 <!-- 请注意这里type=module才能运行 -->
 9 <script type="module">
10     import {isEmail} from './validate.js';
11     var e1 = 'dddd';
12     var e2 = 'yexiaochai@qq.com'
13     console.log(isEmail(e1))
14     console.log(isEmail(e2))
15 </script>
16 </body>
17 </html>
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>ES6中的Module提出，在我这里看来是想在官方完成之前requireJS干的工作，这里也有一些本质上的不一样：

① requireJS是使用加载script标签的方式载入js，没有什么限制

② import命令会被js引擎静态分析，先于模块其他语句执行

以上特性会直接给我们带来一些困扰，比如原来我们项目控制器会有这么一段代码：

```
<pre class="wp-block-preformatted">1 var viewId = ''; //由浏览器获取试图id,url可能为?viewId=booking|list|...
2 //如果不存在则需要构建，记住构建时需要使用viewdata继承源view
3 requirejs(viewId, function(View) {
4     //执行根据url参数动态加载view逻辑
5 })
```

前面说过了，import命令会被js引擎静态分析，先于模块其他语句执行，所以我们在根本不能将import执行滞后，或者动态化，做不到的，这种写法也是报错的：

```
<pre class="wp-block-preformatted">if (viewId) {
  import view from './' + viewId;
}
```

<figure class="wp-block-image">![](https://images2018.cnblogs.com/blog/294743/201807/294743-20180724151826864-2032150411.png)</figure>这种设计会有利于提高编译器效率，但是之前的动态业务逻辑就不知道如何继续了？而ES6如果提供import的方法，我们变可以执行逻辑：

```
<pre class="wp-block-preformatted">1 import(viewId, function() {
2     //渲染页面
3 })
```

事实上他也提供了：

<figure class="wp-block-image">![](https://images2018.cnblogs.com/blog/294743/201807/294743-20180724152659396-1180705960.png)</figure>现在看起来，JS中的模块便十分完美了，至于其中一些细节，便可以用到的时候再说了

# ES6中的类Class

我们对我们的定位一直是非常清晰的，我们就是要干大项目的，我们是要干复杂的项目，除了模块概念，类的概念也非常重要，我们之前用的这种方式实现一个类，我们来温故而知新。

当一个函数被创建时，Function构造函数产生的函数会隐式的被赋予一个prototype属性，prototype包含一个constructor对象

而constructor便是该新函数对象（constructor意义不大，但是可以帮我们找到继承关系）

每个函数都会有一个prototype属性，该属性指向另一对象，这个对象包含可以由特定类型的所有实例共享的属性和方法

每次实例化后，实例内部都会包含一个\[\[prototype\]\]（\_\_proto\_\_）的内部属性，这个属性指向prototype

```
<pre class="wp-block-preformatted">① 我们通过isPrototypeOf来确定某个对象是不是我的原型
② hasOwnPrototype 可以检测一个属性是存在实例中还是原型中，该属性不是原型属性才返回true
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">var Person = function (name, age) {
    this.name = name;
    this.age = age;
};
Person.prototype.getName = function () {
    return this.name;
};
var y = new Person('叶小钗', 30);
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![](https://images0.cnblogs.com/blog/294743/201306/29171655-6dae3b7ac53b4f39a31b5c3c6446f993.png)</figure>为了方便，使用，我们做了更为复杂的封装：

<figure class="wp-block-image">![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)</figure> View Code

这里写一个demo：

<figure class="wp-block-image">![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)</figure> View Code

<figure class="wp-block-image">![](https://images2018.cnblogs.com/blog/294743/201807/294743-20180724165909628-1622302904.png)</figure>ES6中直接从标准层面解决了我们的问题，他提出了Class关键词让我们可以更好的定义类，我们这里用我们ES6的模块语法重新实现一次：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 export class AbstractView {
 2     constructor(opts) {
 3         opts = opts || {};
 4         this.wrapper = opts.wrapper || $('body');
 5         //事件集合
 6         this.events = {};
 7         this.isCreate = false;
 8     }
 9     on(type, fn) {
10         if (!this.events[type]) this.events[type] = [];
11         this.events[type].push(fn);
12     }
13     trigger(type) {
14         if (!this.events[type]) return;
15         for (var i = 0, len = this.events[type].length; i < len; i++) {
16             this.events[type][i].call(this)
17         }
18     }
19     createHtml() {
20         throw '必须重写';
21     }
22     create() {
23         this.root = $(this.createHtml());
24         this.wrapper.append(this.root);
25         this.trigger('onCreate');
26         this.isCreate = true;
27     }
28     show() {
29         if (!this.isCreate) this.create();
30         this.root.show();
31         this.trigger('onShow');
32     }
33     hide() {
34         this.root.hide();
35     }
36 }
37 export class Alert extends AbstractView {
38     createHtml() {
39         return '<div class="alert">这里是alert框</div>';
40     }
41 }
42 export class AlertTitle extends Alert {
43     constructor(opts) {
44         super(opts);
45         this.title = '';
46     }
47     createHtml() {
48         return '<div class="alert"><h2>' + this.title + '</h2>这里是带标题alert框</div>';
49     }
50     setTitle(title) {
51         this.title = title;
52         this.root.find('h2').html(title)
53     }
54 }
55 export class  AlertTitleButton extends AlertTitle {
56     constructor(opts) {
57         super(opts);
58         this.on('onShow', function () {
59             var bt = $('<input type="button" value="点击我" />');
60             bt.click($.proxy(function () {
61                 alert(this.title);
62             }, this));
63             this.root.append(bt)
64         });
65     }
66 }
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 
 2 <html lang="en">
 3 <head>
 4     <meta charset="UTF-8">
 5     <title>Title</title>
 6 </head>
 7 <body>
 8 <script type="text/javascript" src="zepto.js"></script>
 9 
10 <!-- 请注意这里type=module才能运行 -->
11 <script type="module">
12 import {Alert, AlertTitle, AlertTitleButton} from './es6class.js';
13 var v1 = new Alert();
14 v1.show();
15 var v2 = new AlertTitle();
16 v2.show();
17 v2.setTitle('我是标题');
18 var v3 = new AlertTitleButton();
19 v3.show();
20 v3.setTitle('我是标题和按钮的alert');
21 </script>
22 </body>
23 </html>
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>这里的代码完成了与上面一样的功能，而代码更加的清爽了。

# ES6中的函数

我们这里学习ES6，由大到小，首先讨论模块，其次讨论类，这个时候理所当然到了我们的函数了，ES6中函数也多了很多新特性或者说语法糖吧，首先我们来说一下这里的箭头函数

### 箭头函数

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">//ES5
$('#bt').click(function (e) {
    //doing something
})
//ES6
$('#bt').click(e => {
    //doing something
})
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>有点语法糖的感觉，有一个很大不同的是，箭头函数不具有this属性，箭头函数直接使用的是外部的this的作用域，这个想不想用看个人习惯吧。

### 参数新特性

ES6可以为参数提供默认属性

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">1 function log(x, y = 'World') {
2   console.log(x, y);
3 }
4 
5 log('Hello') // Hello World
6 log('Hello', 'China') // Hello China
7 log('Hello', '') // Hello
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>至于不定参数撒的，我这里没有多过多的使用，等项目遇到再说吧，如果研究的太细碎，反而不适合我们开展工作。

# let、const和var

之前的js世界里，我们定义变量都是使用的var，别说还真挺好用的，虽有会有一些问题，但是对于熟悉js特性的小伙伴都能很好的解决，一般记住：变量提升会解决绝大多数问题。

就能解决很多问题，而且真实项目中，我们会会避免出现变量出现重名的情况所以有时候大家面试题中看到的场景在实际工作中很少发生，只要不刻意臆想、制造一些难以判断的场景，其实并不会出现多少BUG，不能因为想考察人家对语言特性的了解，就做一些容易容易忘掉的陷阱题。

无论如何，var 声明的变量受到了一定诟病，事实上在强类型语言看来也确实是设计BUG，但是完全废弃var的使用显然不是js该做的事情，这种情况下出现了let关键词。

let与var一致用以声明变量，并且一切用var的地方都可以使用let替换，新的标准也建议大家不要再使用var了，let具有更好的作用域规则，也许这个规则是边界更加清晰了：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>这里是一个经典的闭包问题：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>因为i在全局范围有效，共享同一个作用域，所以i就只有10了，为了解决这个问题，我们之前会引入闭包，产生新的作用域空间（好像学名是变量对象，我给忘了），但是那里的i跟这里的i已经不是一个东西了，但如果将var改成let，上面的答案是符合预期的。可以简单理解为每一次“{}”，let定义的变量都会产生新的作用域空间，这里产生了循环，所以每一次都不一样，这里与闭包有点类似是开辟了不同的空间。

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>这里因为内部重新声明了i，事实上产生了3个作用域，这里一共有4个作用域指向，let最大的作用就是js中块级作用域的存在，并且内部的变量不会被外部所访问，所以之前为了防止变量侮辱的立即执行函数，似乎变得不是那么必要了。

之前我们定义一个常量会采用全部大写的方式：

```
<pre class="wp-block-preformatted">var NUM = 10;
```

为了解决这个问题，ES6引入了const命令，让我们定义只读常量，这里不对细节做过多研究，直接后续项目实践吧，项目出真知。

# 生成器Generators

ES6中提出了生成器Generators的概念，这是一种异步编程的解决方案，可以将其理解为一种状态机，封装了多个内部状态，这里来个demo：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
var hw = helloWorldGenerator();
hw.next()
// { value: 'hello', done: false }
hw.next()
// { value: 'world', done: false }
hw.next()
// { value: 'ending', done: true }
hw.next()
// { value: undefined, done: true }
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>这个yield（产出）类似于之前的return，直观的理解就是一个函数可以返回多次了，或者说函数具有“顺序状态”，yield提供了暂停功能。这里我想写个代码来验证下期中的作用域状态：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">function* test(){
    let i = 0;
    setTimeout(function() {
        i++;
    }, 1000);
    yield i;
    yield i++;
    return i
}

let t = test();
console.log(t.next());

setTimeout(function() {
    console.log(t.next());
}, 2000);
console.log(t.next());

//{value: 0, done: false}
//{value: 0, done: false}
//{value: 2, done: true}
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>之前我们写一个城市级联的代码，可能会有些令人蛋疼：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 $.get('getCity', {id: 0}, function(province) {
 2     let pid = province[0];
 3     //根据省id获取城市数据
 4     $.get('getCity', {id: pid}, function(city) {
 5         let cityId = city[0];
 6         //根据市级id获取县
 7         $.get('getCity', {id: cityId}, function(city) {
 8             //do smt.
 9         });
10     });
11 });
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>这个代码大家应当比较熟悉了，用promise能从语法层面解决一些问题，这里简单介绍下promise。

### Promise

Promise是一种异步解决方案，有些同事认为其出现就是为了我们代码变得更好看，解决回调地狱的语法糖，ES6将其写入了语音标准，提供了原生Promise对象。Promise为一容器，里面保存异步事件的结果，他是一个对象具有三个状态：pending（进行中）、fulfilled（已成功）、rejected（已失败），这里还是来个简单代码说明：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">1 function timeout(ms) {
2   return new Promise((resolve, reject) => {
3     setTimeout(resolve, ms, 'done');
4   });
5 }
6 
7 timeout(100).then((value) => {
8   console.log(value);
9 });
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>实例化Promise时，第一个回调必须提供，是进行转为成功时候会执行，第二个也是一个函数失败时候调用，非必须，这里来个demo：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 let  timeout = function (ms) {
 2     return new Promise(function (resolve) {
 3         setTimeout(resolve, ms);
 4     });
 5 };
 6 
 7 timeout(1000).then(function () {
 8     return timeout(1000).then(function () {
 9         let s = '大家';
10         console.log(s)
11         return s;
12     })
13 
14 }).then(function (data) {
15     return timeout(1000).then(function () {
16         let s = data + '好,';
17         console.log(s)
18         return s;
19     })
20 }).then(function(data) {
21     return timeout(1000).then(function () {
22         let s = data + '我是叶小钗';
23         console.log(s)
24         return s;
25     });
26 }).then(function(data) {
27     console.log(data)
28 });
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>如果我们请求有依赖的话，第一个请求依赖于第二个请求，代码就可以这样写：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 let getData = function(url, param) {
 2     return new Promise(function (resolve) {
 3         $.get(url, param, resolve );
 4     });
 5 }
 6 getData('http://api.kuai.baidu.com/city/getstartcitys?callback=?').then(function (data) {
 7     console.log('我获取了省数据,我们马上根据省数据申请市数据', data);
 8     return getData('http://api.kuai.baidu.com/city/getstartcitys?callback=?').then(function (data1) {
 9         console.log(data1);
10         return '我是市数据';
11     })
12 
13 }).then(function(data) {
14     //前面的参数传过来了
15     console.log(data);
16     console.log('我获取了市数据,我们马上根据市数据申请县数据');
17     getData('http://api.kuai.baidu.com/city/getstartcitys?callback=?').then(function (data1) {
18         console.log(data1);
19     });
20 })
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![](https://images2018.cnblogs.com/blog/294743/201807/294743-20180725154939571-541050671.png)</figure>如此便可以避免多层嵌套了，关于Promise的知识点还很多，我们遇到复杂的工作场景再拿出来说吧，我对他的定位就是一个语法糖，将异步的方式变成同步的写法，骨子里还是异步，上面我们用Promise解决回调地狱问题，但是回调地狱问题遇到的不多，却发现Promise一堆then看见就有点烦，我们的Generator函数似乎可以让这个情况得到缓解。

但是暂时在实际工作中我没有找到更好的使用场景，这里暂时到这里，后面工作遇到再详述，对这块不是很熟悉也不妨碍我们使用ES6写代码。

# 代理

代理，其实就是你要做什么我帮你做了就行了，一般代理的原因都是，我需要做点手脚，或者多点操作，或者做点“赋能”，如我们常常包装setTimeout一般：

```
<pre class="wp-block-preformatted">1 let timeout = function (ms, callback) {
2     setTimeout(callback, ms);
3 }
```

我们包装setTimeout往往是为了clearTimeout的时候能全部清理掉，其实就是拦截下，ES6提供了Proxy关键词用于设置代理器：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 var obj = new Proxy({}, {
 2   get: function (target, key, receiver) {
 3     console.log(`getting ${key}!`);
 4     return Reflect.get(target, key, receiver);
 5   },
 6   set: function (target, key, value, receiver) {
 7     console.log(`setting ${key}!`);
 8     return Reflect.set(target, key, value, receiver);
 9   }
10 });
11 obj.count = 1
12 //  setting count!
13 ++obj.count
14 //  getting count!
15 //  setting count!
16 //  2
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">//target参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为
var proxy = new Proxy(target, handler);
```

我们这里继续写一个简单的demo：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted"> 1 let person = {
 2     constructor: function(name, age = 20) {
 3         this.name = name;
 4         this.age = age
 5     },
 6     addAge: function() {
 7         this.age++;
 8     },
 9     getAge: function() {
10         return this.age;
11     }
12 }
13 
14 var proxy = new Proxy(person, {
15     get: function(target, property) {
16         console.log(arguments);
17         return target[property];
18     },
19     set: function(target, property) {
20         console.log(arguments);
21     }
22 });
23 
24 person.constructor('叶小钗', 30);
25 console.log(person.age)
26 console.log(proxy.age)
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![](https://images2018.cnblogs.com/blog/294743/201807/294743-20180725171946602-1251154964.png)</figure>但是暂时我没有发现比较好的业务场景，比如说，我现在重写了一个实例的get方法，便能在一个全局容器中记录这个被执行了多少次，这里一个业务场景是：我一次个页面连续发出了很多次请求，但是我单页应用做页面跳转时候，我需要将所有的请求句柄移除，这个似乎也不是代理完成的工作，于是要使用ES6写代码，似乎可以暂时忽略代理。

</body></html>