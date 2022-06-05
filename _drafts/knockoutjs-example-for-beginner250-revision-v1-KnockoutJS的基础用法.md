---
id: 251
title: KnockoutJS的基础用法
date: '2019-08-16T18:46:30+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/250-revision-v1/'
permalink: '/?p=251'
---

# 一、Knockout.js简介

## 1、Knockout.js和MVVM

如今，各种前端框架应接不暇，令人眼花缭乱，有时不得不感叹作为程序猿也真是苦逼，总有学不完的技术，何时是尽头，除非你转化！苦海无边，回头是不是岸，由你决定！

Knockout.js是一个基于MVVM模式的轻量级的前端框架，有多轻？根据官网上面显示的最新版本v3.4.0，仅22kb。能够友好地处理数据模型和界面DOM的绑定，最重要的是，它的绑定是双向的，也就是说数据模型变化了，界面DOM上的数据也会跟着发生变化，反过来，界面DOM上的数据变化了，数据模型也会相应这个变化。这样能够大大减少我们的前端代码量，并且使得我们界面易于维护，再也不用写一大堆事件监控数据模型和界面DOM的变化了。下面博主会根据一个使用实例来说明这两点。

Knockout.js官网：[http://knockoutjs.com](http://knockoutjs.com/)

Knockout.js开源地址：<https://github.com/knockout/knockout>

MVVM模式：这是一种创建用户界面的设计模式，MVVM把它拆分成三块就是Model、View、ViewModel，Model就是数据模型，View就是我们的视图，ViewModel就是一个视图模型，用来绑定数据模型和视图上面的dom元素。如果你使用过WPF和Silverlight，理解这个应该不是啥问题；没有使用过也什么关系，看完此文，你会有一个大致的认识。

## 2、最简单的实例

 一般来说，如果你从零开始使用Knockout.js，你至少需要做以下四部

### 2.1、去官网下载knockout.js文件，然后引用到view页面里面。

```
<pre class="wp-block-preformatted"><script src="~/scripts/knockout/knockout-3.4.0.min.js"></script>
```

注意：knockout.js并不需要jquery的支持，如果你的项目需要用到jquery的相关操作，则引用jquery；否则只引用以上文件即可。

### 2.2、定义ViewModel

viewmodel是什么？其实，在js里面，它看上去就像一个json对象。我们定义一个viewmodel：

```
<pre class="wp-block-preformatted">        var myViewModel = {
            Name: "Lilei",
            profession: "软件工程师",
        };
```

### 2.3、view视图里面定义绑定data-bind的标签

```
<pre class="wp-block-preformatted">    <div> 
        姓名：<label data-bind="text:Name"></label><br />
        职业：<input type="text" data-bind="textinput:Profession" />
    </div>
```

注意：对应input标签的文本，需要使用textinput，而普通标签的文本使用text即可。

### 2.4、激活绑定

做了以上三步，还需要激活knockout的绑定

```
<pre class="wp-block-preformatted">ko.applyBindings(myViewModel);
```

做到这四部，基本实现了一个最简单的viewmodel的数据绑定。得到效果：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160417140524316-757827736.png)</figure>**如果你够细心，会发现ko.applyBindings()方法有两个参数，第一个就是我们需要绑定的viewmodel，第二个又是什么呢？由 ko.applyBindings(myViewModel); 可知，第二个参数是一个可选参数，它表示viewmodel绑定的标签的作用域，比如，我们将以上代码改一下：**

```
<pre class="wp-block-preformatted">    <div> 
        姓名：<label id="lb_name" data-bind="text:Name"></label><br />
        职业：<input type="text" data-bind="textinput:Profession" />
    </div>
```

```
<pre class="wp-block-preformatted">ko.applyBindings(myViewModel,document.getElementById("lb_name"));
```

得到结果：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160417142412207-81297607.png)</figure>由此可知：第二个参数限定了myViewModel的作用范围，也就是说，只有在id=”lb\_name”的标签上面绑定才会生效，如果第二个参数是div等容器标签，它表示该绑定的范围为该div下面的所有子标签。

## 3、监控属性

截止到上面的四步，我们看不到任何效果，看到的无非就是将一个json对象的的数据绑定到了html标签上面，这样做有什么意义呢？不是把简单的问题复杂化吗？别急，马上见证奇迹！上文说了，knockout最重要的意义在于双向绑定，那么如何实现我们的双向绑定呢？答案就是监控属性。

在knockout里面，核心的有三个监控属性：**Observables,DependentObservables,ObservableArray**，Observe的意思翻译过来是观察、观测的意思，如果说成观察属性或者观测属性感觉不太恰当，我们暂且叫监控属性。

### 3.1、Observables：监控属性

我们将上面的例子改成这样：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted"><head>
    <meta name="viewport" content="width=device-width" />
    <title>Index3</title>
    <script src="~/scripts/jquery-1.9.1.min.js"></script>
    <script src="~/scripts/knockout/knockout-3.4.0.min.js"></script>
</head>
<body>
    <div> 
        姓名：<label data-bind="text:Name"></label><br />
        职业：<input type="text" data-bind="textinput:Profession" />
    </div>
    <div>
        <input type="text" id="txt_testobservable" />
    </div>
    <script type="text/javascript">
        //1.定义ViewModel
        var myViewModel = {
            Name: ko.observable("Lilei"),
            Profession: "软件工程师",
        };

        //2.激活绑定
        ko.applyBindings(myViewModel);

        $(function () {
            //注册文本框的textchange事件
            $("#txt_testobservable").on("input", function () {
                myViewModel.Name($(this).val());
            });
        });
    </script>
</body>
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure> ko.observable(“Lilei”) 这一句的意义是将viewmodel的Name属性添加成为监控属性，一定Name属性变成监控属性，神奇的事情就发生了，我们来看看当我们写myViewModel.的时候：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160417162638613-1557901059.png)</figure>Name由原来的属性变成方法了，也就是说一旦添加了ko.observable()，那么对应的属性就会变成方法，那么对于Name的取值和赋值都需要使用myViewModel.Name()来处理。我们先来看看效果：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160417163000082-2118889503.gif)</figure>**代码释疑：很显然 myViewModel.Name($(this).val()); 这一句将当前文本框的值赋给了Name属性，由于界面绑定了Name属性，所以label里面的值也随之发生了变化。或者你会说，这个使用textchange事件也可以做到的，只要将当前文本框的值赋给label标签，也可以达到这个效果，这个不算什么。确实，你的写法也可以达到目的，但是我们的监控属性的意义在于，任何地方改变了Name的值，界面都会随之变化，而不用每个地方去给label标签赋值，JS里面只需要把关注点方法myViewModel.Name()上面即可。是不是很厉害~~**

### 3.2、DependentObservables：监控依赖属性

如果看了上面的监控属性还没过瘾？下面再来看看监控依赖属性的使用。

我们将代码再改下看看：

```
<pre class="wp-block-preformatted"><head>
    <meta name="viewport" content="width=device-width" />
    <title>Index3</title>
    <script src="~/scripts/jquery-1.9.1.min.js"></script>
    <script src="~/scripts/knockout/knockout-3.4.0.min.js"></script>
</head>
<body>
    <div> 
        姓名：<input type="text" data-bind="textinput:Name" /><br />
        职业：<input type="text" data-bind="textinput:Profession" /><br />
        描述：<label data-bind="text:Des"></label>
    </div>
    <script type="text/javascript">
        //1.定义ViewModel
        var myViewModel = {
            Name: ko.observable("Lilei"),
            Profession: ko.observable("软件工程师"),
        };
        myViewModel.Des = ko.dependentObservable(function () {
            return "本人姓名——" + myViewModel.Name() + "，职业——" + myViewModel.Profession();
        });

        //2.激活绑定
        ko.applyBindings(myViewModel);</script>
</body>
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>先来看看效果：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160417165459520-1535747229.gif)</figure>**代码释疑：通过添加监控依赖属性 ko.dependentObservable() 将Des属性的值能同时监控到Name和Profession两个的变化，其中任何一个发生变化，Des绑定的标签都会触发改变，这样做的最大好处就是避免了我们js去操作dom的麻烦，有点意思吧。**

### 3.3、ObservableArray；监控数组

除了上面两种，ko还支持对数组对象的监控。我们来看一个例子：

```
<pre class="wp-block-preformatted"><head>
    <meta name="viewport" content="width=device-width" />
    <title>Index3</title>
    <script src="~/scripts/jquery-1.9.1.min.js"></script>
    <script src="~/scripts/knockout/knockout-3.4.0.min.js"></script>
</head>
<body>
    <div><select data-bind="options:deptArr,
                           optionsText:'Name'"></select>

    </div>
    <div>
        <input type="text" id="txt_testobservable" /><input type="button" id="btn_test" value="新增部门" />
    </div>
    <script type="text/javascript">
            var deptArr = ko.observableArray([
                { id: 1, Name: '研发部' },
                { id: 2, Name: '行政部' },
                { id: 3, Name: '人事部' }
            ]);
            var viewModel = {
                deptArr: deptArr,
            };

            ko.applyBindings(viewModel);

            var i=4;
            $(function () {
                $("#btn_test").on("click", function () {
                    deptArr.push({ id: i++, Name: $("#txt_testobservable").val() });
                });
            });
    </script>
</body>
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>看看效果：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160417195348129-347307189.gif)</figure>**代码释疑：以上通过ko.observableArray()这个方法添加了对数组对象的监控，也就是说，js里面任何地方只要对deptArr数组对象做了数组的改变，都会触发UI给出相应。需要注意的一点是，监控数组实际上是监控的数组对象本身，对于数组对象里面的子对象属性发生变化，是无法监控到的。比如我们将点击事件改成这样：**

```
<pre class="wp-block-preformatted">            $(function () {
                $("#btn_test").on("click", function () {
                    deptArr[1].Name = "aaa";
                });
            });
```

效果：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160417195958785-112751367.gif)</figure>**由此说明数组监控实际上监控的是数组对象本身，对于数组里面元素的属性变化不会监控。如果确实需要对数据里面对象的属性变化进行监控，需要再对数据里面对象属性使用ko.observable()，两者联合使用。有兴趣的可以试试。**

## 4、ko里面常见的data-bind属性

上文中，我们使用了多个data-bind属性，那么在knockout里面，到底有多少个这种data-bind的属性呢。这里我们列出一些常用的属性。

### 4.1、text和inputText

text，顾名思义就是文本的意思，这个绑定属性一般用于&lt;label&gt;、&lt;span&gt;、&lt;div&gt;等标签显示文本，当然，如果你愿意，任何标签都可以使用这个绑定。它是使用基本上没什么好说的。如果没有使用ko.observable()，则是静态绑定，否则是动态绑定。

inputText，input标签的文本，相当于input标签的value属性。

```
<pre class="wp-block-preformatted">　　<div> 
        姓名：<label data-bind="text:Name"></label><br />
        职业：<input type="text" data-bind="textinput:Profession" />
    </div>
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted">　　　　 //1.定义ViewModel
        var myViewModel = {
            Name: ko.observable("Lilei"),
            Profession: "软件工程师",
        };

        //2.激活绑定
        ko.applyBindings(myViewModel);
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>### 4.2、value

这个绑定属性一般用于input标签，和上面的inputText基本相似。只不过value更加规范。

和value一起使用的还有一个参数valueUpdate，它表示界面做一个什么操作的时候更新该value。valueUpdate主要取值有change/keyup/keypress/afterkeydown等。分别表示文本变化、键盘缩起、键盘按下、键盘按下之后等操作时候更新value对应的viewmodel的值。

```
<pre class="wp-block-preformatted">姓名：<input type="text" data-bind="value:Name,valueUpdate:'keyup'" /><br />
```

```
<pre class="wp-block-preformatted">　　　　var myViewModel = {
            Name: ko.observable("Lilei"),
        };//2.激活绑定
        ko.applyBindings(myViewModel);
```

上述代码表示键盘收起的时候更新文本框的value属性和myViewModel的Name属性。

### 4.3、checked

checked绑定一般用于checkbox、radio等可以选中的表单元素，它对应的值是bool类型。和value的用法基本相似，就不做重复说明。

### 4.4、enable

enable绑定一般用于是否启用标签元素，一般用于表单元素的启用和禁用。和disabled相反，对应的值也是bool类型。

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted"> 　　<div>
        <input type="text" data-bind="enable:IsMen"/>
    </div>
    <script type="text/javascript">
        //1.定义ViewModel
        var myViewModel = {
            Name: ko.observable("Lilei"),
            Profession: ko.observable("软件工程师"),
            Age: ko.observable(40),
            IsMen:ko.observable(true)
        };

        //2.激活绑定
        ko.applyBindings(myViewModel);
        myViewModel.IsMen(false);

    </script>
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>由于IsMen属性变成了false，所有对应的文本框会显示禁用状态。

### 4.5、disabled

和enable相反，用法和enable类似。

### 4.6、options

上文中在使用select的绑定时候使用过options，它表示select标签的option的集合，对应的值为一个数组，表示这个下拉框的数据源。可以使用observableArray启用对这个数据源的监控。用法见上面。

### 4.7、html

text绑定实际上是对标签innerText的设置和取值，那么同理，html绑定也是对innerHTML的设置和取值。它对应的值为一段html标签。

### 4.8、css

css绑定是添加或删除一个或多个样式（class）到DOM元素上。使用格式：

```
<pre class="wp-block-preformatted">    <style type="text/css">
        .testbold {
        background-color:powderblue;
        }
    </style>
```

```
<pre class="wp-block-preformatted"><div data-bind="css:{testbold:myViewModel.Name()=='Lilei'}">aaaa</div>
```

```
<pre class="wp-block-preformatted">        var myViewModel = {
            Name: ko.observable("Lilei"),
            Profession: ko.observable("软件工程师"),
            Age:ko.observable(40)
        };
```

该div会显示背景色。

如果需要增加或移除多个样式，只要稍微改下即可，比如：

```
<pre class="wp-block-preformatted"><div data-bind="css:{testbold:myViewModel.Name()=='Lilei',testborder:myViewModel.Profession()=='PHP工程师'}">aaaa</div>
```

### 4.9、style

如果css绑定的作用是向标签动态添加或移除class样式，那么style绑定的作用就是想标签动态添加或移除某一个样式。比如：

```
<pre class="wp-block-preformatted"><div data-bind="css:{ padding: 0px; color: rgb(0, 0, 255); line-height: 1.5 !important;">>aaaa</div>
```

如果是添加或者移除多个，同css绑定的用法

### 4.10、attr

attr绑定主要用于向标签添加移除某一个或多个属性（包括自定义属性），永和和css类似。

### 4.11、click

click绑定表示在对应的DOM元素上面添加点击事件的执行方法。可以在任意元素上面使用。

```
<pre class="wp-block-preformatted">    <div>
        <input type="button" value="测试click绑定" data-bind="click:ClickFunc" />
    </div>
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted"> 　　　　var myViewModel = {
            ClickFunc:function(){
                alert($(event.currentTarget).val());
            }
        };

        ko.applyBindings(myViewModel);
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>event.currentTarget表示当前点击的DOM元素。有时为了简便，我们直接使用匿名函数的方式绑定，比如：

```
<pre class="wp-block-preformatted">    <div>
        <input type="button" value="测试click绑定" data-bind="click:function(){
                alert('点击了');
            }" />
    </div>
```

但是这种将js揉到html里面的写法让博主难以接受，并且觉得维护起来相对不方便，尤其是点击事件里面的逻辑略复杂时。所以，如非必须，不建议直接写这种匿名函数的方式。

### 4.12、其他

关于data-bind的所有绑定，可以看官网上面的介绍，这里就不一一列举了。需要用的时候去官网上查下就好了。看看官网上面列举的所有绑定：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160418095957554-680786556.png)</figure>## 5、Json对象和监控属性的转化及关系

我们知道，为了避免不同语言直接的展现方式，一般情况下我们前端和后端交互的时候统一使用Json格式的数据，我们通过http请求从后端取到的数据模型，而要使用我们的ko的一些特性，必须要将这些普通的数据模型转换成ko的监控属性；反过来，我们使用ko的监控属性，有时又需要把这些属性转换为普通的json数据传到后台，那么如何实现这个转换呢？

### 5.1、JSON对象转换成ViewModel

比如我们从后台取到一个Json对象，然后把它变成到我们的viewmodel，然后绑定到我们的界面DOM。

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted">            $.ajax({
                url: "/Home/GetData",
                type: "get",
                data: {},
                success: function (data, status) {
                    var oJson = data;
                }
            });
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>我们发送一个请求到后端，取到一个json对象，赋值到oJson，然后我们把oJson转换成viewmodel，最直观的方式就是手动转换了。比如我们可以这样：

```
<pre class="wp-block-preformatted">　　　　var myViewModelJson = {
            DeptName: ko.observable(),
            DeptLevel: ko.observable(),
            DeptDesc:ko.observable()
        };
        ko.applyBindings(myViewModelJson);
```

然后在ajax请求的success里面

```
<pre class="wp-block-preformatted">                success: function (data, status) {
                    var oJson = data;
                    myViewModelJson.DeptName(oJson.DeptName);
                    myViewModelJson.DeptLevel(oJson.DetpLevel);
                    myViewModelJson.DeptDesc(oJson.DeptDesc);
                }
```

这样，通过手动绑定，实现了json对象到viewmodel的绑定。这样做的好处就是灵活，坏处显而易见，手工代码量太大。

还好，有我们万能的开源，总有人想到更好的办法，我们使用knockout.Mapping组件就能很好地帮助我们界面json对象到viewmodel的转换。

knockout.Mapping开源地址：[下载](https://github.com/SteveSanderson/knockout.mapping)

下面来简单看看它如何使用，还是上面的例子，我们不用实现定义任何viewmodel，首先需要引用knockout.mapping.js

```
<pre class="wp-block-preformatted">    <script src="~/scripts/knockout/knockout-3.4.0.min.js"></script>
    <script src="~/scripts/knockout/extensions/knockout.mapping-latest.js"></script>
```

**注意：这里knock.mapping-lastest.js必须要放在knockout-3.4.0.min.js的后面，否则调用不到ko.mapping。**

然后直接在success函数里面这样使用

```
<pre class="wp-block-preformatted">　　　　　　　　　success: function (data, status) {
                    var myViewModelJson2 = ko.mapping.fromJS(data);
                    ko.applyBindings(myViewModelJson2);
                }
```

我们来看效果：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160418131942163-1728406537.gif)</figure>**代码释疑：通过ajax请求从后台取到的json对象，通过ko.mapping.fromJS()，很方便地将其转换成了viewmodel，是不是猴犀利！当然除了这种用法，还可以更新已经存在viewmodel，使用如下：**

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted">　　　　var myViewModelJson = {
            DeptName: ko.observable(),
            DeptLevel: ko.observable(),
            DeptDesc:ko.observable()
        };
        ko.applyBindings(myViewModelJson);
        $(function () {
            $.ajax({
                url: "/Home/GetData",
                type: "get",
                data: {},
                success: function (data, status) {
                    ko.mapping.fromJS(data, myViewModelJson)
                }
            });
        });
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>**在success里面，根据data的值去更新myViewModelJson这个viewmodel。**

### 5.2、ViewModel转换成JSON对象

 上面说了JSON对象转化为viewmodel，那么反过来，如果我们需要将viewmodel转换为Json对象传递到后端，怎么办呢？

knockout里面提供了两个方法：

- ko.toJS()：将viewmodel转换为JSON对象
- ko.toJSON()：将viewmodel转换为序列化过的Json string。

比如我们的代码如下：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted">　　　　 $(function () {
            var oJson1 = ko.toJS(myViewModelJson);
            var oJson2 = ko.toJSON(myViewModelJson);
        });
        var myViewModelJson = {
            DeptName: ko.observable("研发部"),
            DeptLevel: ko.observable("2"),
            DeptDesc: ko.observable("开发一伙人")
        };
        ko.applyBindings(myViewModelJson);
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>那么我们来监控下oJson1和oJson2的值：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160418133450085-1129766331.gif)</figure>**代码释疑：通过上面这张图，很容易理解两个方法的区别，这里需要说明一点的是，这两个方法是ko自带的，并不需要mapping组件的支持。**

## 6、创建自己的data-bind属性

上面讲了那么多，都是介绍knockout里面的一些绑定和监控，那么，有些时候，我们需要自定义我们的data-bind，型如： &lt;label data-bind=”myBind:Name”&gt;&lt;/label&gt; ，这种需求再做组件封装的时候尤其有用，是否可以实现呢？当然可以。

在knockout里面，提供了ko.bindingHandlers属性，用于自定义data-bind属性，它的语法如下：

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted">ko.bindingHandlers.MySelect = {
    init: function (element, valueAccessor, allBindingsAccessor, viewModel) {

    },

    update: function (element, valueAccessor, allBindingsAccessor, viewModel) {

    }
};
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>就这样申明一下，然后在我们的html标签里面就可以使用自定义data-bind了。

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted">    <div> 
        <select data-bind="MySelect:$root">
            <option id="1">研发部</option>
            <option id="2">人事部</option>
            <option id="3">行政部</option>
        </select>
    </div>
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>MySelect就是我们自定义的绑定属性，$root暂且可以理解为初始化（虽然这样解释并不严谨，如果有更加合理的解释欢迎指正）。

**代码释疑：通过上面的ko.bindingHandlers就能简单实现自定绑定属性，需要说明两点：**

- **init，顾名思义初始化自定义绑定，它里面包含多个参数，一般使用较多的是前两个参数，第一个参数表示初始化自定义绑定的DOM元素，第二个参数一般用来传递初始化的参数。**
- **update，更新回调，当对应的监控属性变化时，会进入到这个方法。如果不需要回调，此方法可以不声明。**

 在此博主就结合原来分享过的一个下拉框组件MutiSelect来简单说明下自定义绑定的使用。

### **6.1、最简单的MutiSelect**

一般情况下，如果我们需要使用ko去封装一些通用组件，就需要用到我们的ko.bindingHandlers，下面博主就结合MutiSelect组件来说说如何使用。

首先声明自定义的ko.bindingHandlers，在init方法里面初始化我们的select标签

```
<pre class="wp-block-preformatted">ko.bindingHandlers.MySelect = {
    init: function (element, valueAccessor, allBindingsAccessor, viewModel) {
        $(element).multiselect();
    },

    update: function (element, valueAccessor, allBindingsAccessor, viewModel) {

    }
};
```

然后在页面标签里面使用

```
<pre class="wp-block-preformatted">    <div style="text-align:center;"> 
        <select data-bind="MySelect:$root">
            <option id="1">研发部</option>
            <option id="2">人事部</option>
            <option id="3">行政部</option>
        </select>
    </div>
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>最后第三部，激活绑定

```
<pre class="wp-block-preformatted">$(function () {
    var MultiSelect = {};
    ko.applyBindings(MultiSelect);
});
```

如果不需要传递参数，这里只需要绑定一个空的viewmodel即可。有人疑惑了，第三部感觉没啥实际意义呢。博主的理解是，DOM元素需要使用data-bind去绑定数据，必须要启用ko的绑定，也就是这里的ko.applyBindings()。

得到效果：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160418144022476-1671789385.png)</figure>### **6.2、参数传递**

 第一步还是自定义ko.bindingHandlers

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure><figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure>```
<pre class="wp-block-preformatted">ko.bindingHandlers.MySelect = {
    init: function (element, valueAccessor, allBindingsAccessor, viewModel) {
        var oParam = valueAccessor();
        $(element).multiselect(oParam);
    },

    update: function (element, valueAccessor, allBindingsAccessor, viewModel) {

    }
};
```

<figure class="wp-block-image">![复制代码](https://common.cnblogs.com/images/copycode.gif)</figure><figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>第二步和上面相同，在html标签里面使用这个自定义绑定。

第三步，在激活绑定的时候传入参数

```
<pre class="wp-block-preformatted">$(function () {
    var MultiSelect = {
        enableClickableOptGroups: true,//收起分组
        onChange: function (option, checked) {
            alert("选择改变");
        }
    };
    ko.applyBindings(MultiSelect);
});
```

通过这三步即可将参数传到我们的MutiSelect的初始化里面：

<figure class="wp-block-image">![](https://images2015.cnblogs.com/blog/459756/201604/459756-20160418145343929-1644780534.png)</figure>**代码释疑：init事件的第二个参数，我们说了，它主要作用是获取我们viewmodel里面传过来的参数，只不过这里要把它当做方法使用，为什么会这么用，还有待研究！**

</body>