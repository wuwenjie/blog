---
id: 326
title: 一些原生写法可以替代Jquery方法
date: '2019-08-16T22:55:16+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/325-revision-v1/'
permalink: '/?p=326'
---

**一、选取DOM元素**

jQuery的核心是通过各种选择器，选中DOM元素，可以用querySelectorAll方法模拟这个功能。

> var $ = document.querySelectorAll.bind(document);

这里需要注意的是，querySelectorAll方法返回的是NodeList对象，它很像数组（有数字索引和length属性），但不是数组，不能使用pop、push等数组特有方法。如果有需要，可以考虑将Nodelist对象转为数组。

> myList = Array.prototype.slice.call(myNodeList);

**二、DOM操作**

DOM本身就具有很丰富的操作方法，可以取代jQuery提供的操作方法。

尾部追加DOM元素。

> // jQuery写法  
>  $(parent).append($(child));
> 
>  // DOM写法  
>  parent.appendChild(child)

头部插入DOM元素。

> // jQuery写法  
>  $(parent).prepend($(child));
> 
>  // DOM写法  
>  parent.insertBefore(child, parent.childNodes\[0\])

删除DOM元素。

> // jQuery写法  
>  $(child).remove()
> 
>  // DOM写法  
>  child.parentNode.removeChild(child)

**三、事件的监听**

jQuery的on方法，完全可以用addEventListener模拟。

> Element.prototype.on = Element.prototype.addEventListener;

为了使用方便，可以在NodeList对象上也部署这个方法。

> NodeList.prototype.on = function (event, fn) {  
>  \[\]\[‘forEach’\].call(this, function (el) {  
>  el.on(event, fn);  
>  });  
>  return this;  
>  };

**四、事件的触发**

jQuery的trigger方法则需要单独部署，相对复杂一些。

> Element.prototype.trigger = function (type, data) {  
>  var event = document.createEvent(‘HTMLEvents’);  
>  event.initEvent(type, true, true);  
>  event.data = data || {};  
>  event.eventName = type;  
>  event.target = this;  
>  this.dispatchEvent(event);  
>  return this;  
>  };

在NodeList对象上也部署这个方法。

> NodeList.prototype.trigger = function (event) {  
>  \[\]\[‘forEach’\].call(this, function (el) {  
>  el\[‘trigger’\](event);  
>  });  
>  return this;  
>  };

**五、document.ready**

目前的最佳实践，是将JavaScript脚本文件都放在页面底部加载。这样的话，其实document.ready方法（jQuery简写为$(function)）已经不必要了，因为等到运行的时候，DOM对象已经生成了。

**六、attr方法**

jQuery使用attr方法，读写网页元素的属性。

> $(“#picture”).attr(“src”, “http://url/to/image”);

DOM元素允许直接读取属性值，写法要简洁许多。

> $(“#picture”).src = “http://url/to/image”;

需要注意，input元素的this.value返回的是输入框中的值，链接元素的this.href返回的是绝对URL。如果需要用到这两个网页元素的属性准确值，可以用this.getAttribute(‘value’)和this.getAttibute(‘href’)。

**七、addClass方法**

jQuery的addClass方法，用于为DOM元素添加一个class。

> $(‘body’).addClass(‘hasJS’);

DOM元素本身有一个可读写的className属性，可以用来操作class。

> document.body.className = ‘hasJS’;
> 
>  // or
> 
>  document.body.className += ‘ hasJS’;

HTML 5还提供一个classList对象，功能更强大（IE 9不支持）。

> document.body.classList.add(‘hasJS’);
> 
>  document.body.classList.remove(‘hasJS’);
> 
>  document.body.classList.toggle(‘hasJS’);
> 
>  document.body.classList.contains(‘hasJS’);

**八、CSS**

jQuery的css方法，用来设置网页元素的样式。

> $(node).css( “color”, “red” );

DOM元素有一个style属性，可以直接操作。

> element.style.color = “red”;;
> 
>  // or
> 
>  element.style.cssText += ‘color:red’;

**九、数据储存**

jQuery对象可以储存数据。

> $(“body”).data(“foo”, 52);

HTML 5有一个dataset对象，也有类似的功能（IE 10不支持），不过只能保存字符串。

> element.dataset.user = JSON.stringify(user);
> 
>  element.dataset.score = score;

**十、Ajax**

jQuery的Ajax方法，用于异步操作。

> $.ajax({  
>  type: “POST”,  
>  url: “some.php”,  
>  data: { name: “John”, location: “Boston” }  
>  }).done(function( msg ) {  
>  alert( “Data Saved: ” + msg );  
>  });

我们可以定义一个request函数，模拟Ajax方法。

> function request(type, url, opts, callback) {
> 
>  var xhr = new XMLHttpRequest();
> 
>  if (typeof opts === ‘function’) {  
>  callback = opts;  
>  opts = null;  
>  }
> 
>  xhr.open(type, url);
> 
>  var fd = new FormData();
> 
>  if (type === ‘POST’ &amp;&amp; opts) {  
>  for (var key in opts) {  
>  fd.append(key, JSON.stringify(opts\[key\]));  
>  }  
>  }
> 
>  xhr.onload = function () {  
>  callback(JSON.parse(xhr.response));  
>  };
> 
>  xhr.send(opts ? fd : null);
> 
>  }

然后，基于request函数，模拟jQuery的get和post方法。

> var get = request.bind(this, ‘GET’);
> 
>  var post = request.bind(this, ‘POST’);

**十一、动画**

jQuery的animate方法，用于生成动画效果。

> $foo.animate(‘slow’, { x: ‘+=10px’ })；

jQuery的动画效果，很大部分基于DOM。但是目前，CSS 3的动画远比DOM强大，所以可以把动画效果写进CSS，然后通过操作DOM元素的class，来展示动画。

> foo.classList.add(‘animate’)；

如果需要对动画使用回调函数，CSS 3也定义了相应的事件。

> el.addEventListener(“webkitTransitionEnd”, transitionEnded);
> 
>  el.addEventListener(“transitionend”, transitionEnded);

**十二、替代方案**

由于jQuery体积过大，替代方案层出不穷。

其中，最有名的是[zepto.js](http://zeptojs.com/)。它的设计目标是以最小的体积，做到最大兼容jQuery的API。zepto.js 1.0版的原始大小是55KB，优化后是29KB，gzip压缩后为10KB。

如果不求最大兼容，只希望模拟jQuery的基本功能，那么，[min.js](https://github.com/remy/min.js)优化后只有200字节，而[dolla](https://github.com/lelandrichardson/dolla)优化后是1.7KB。

此外，jQuery本身采用模块设计，可以只选择使用自己需要的模块。具体做法参见它的[github网站](https://github.com/jquery/jquery)，或者使用专用的[Web界面](http://projects.jga.me/jquery-builder/)。