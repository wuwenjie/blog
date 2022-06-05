---
id: 280
title: 'Javascript文件加载 ——LABjs和RequireJS'
date: '2012-08-10T20:05:59+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=280'
permalink: /2012/08/javascript-import-file-labjs-requirejs
categories:
    - Javascript
---

传统上，加载Javascript文件都是使用&lt;script&gt;标签。

就像下面这样：

> &lt;script type=”text/javascript” src=”example.js”&gt;&lt;/script&gt;

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/201110/bg2011100301.png)</figure>&lt;script&gt;标签很方便，只要加入网页，浏览器就会读取并运行。但是，它存在一些严重的缺陷。

 **（1）严格的读取顺序。**由于浏览器按照&lt;script&gt;在网页中出现的顺序，读取Javascript文件，然后立即运行，导致在多个文件互相依赖的情况下，依赖性最小的文件必须放在最前面，依赖性最大的文件必须放在最后面，否则代码会报错。

 **（2）性能问题。**浏览器采用”同步模式”加载&lt;script&gt;标签，也就是说，页面会”堵塞”（blocking），等待javascript文件加载完成，然后再运行后面的HTML代码。当存在多个&lt;script&gt;标签时，浏览器无法同时读取，必须读取完一个再去读取另一个，造成读取时间大大延长，页面响应缓慢。

为了解决这些问题，可以使用DOM方法，动态加载Javascript文件。

> function loadScript(url){
> 
>  var script = document.createElement(“script”);
> 
>  script.type = “text/javascript”;
> 
>  script.src = url;
> 
>  document.body.appendChild(script);
> 
>  }

这样做的原理是，浏览器即时创造出一个&lt;script&gt;标签，然后”异步”读取Javascript文件。这样不会造成页面堵塞，但会造成另外一个问题：这样加载的Javascript文件，不在原始的DOM结构之中，因此在DOM-ready（DOMContentLoaded）事件和window.onload事件中指定的回调函数对它无效。

外部函数库[LABjs](http://labjs.com/)和[RequireJS](http://requirejs.org/)，可以帮助我们更有效地管理Javascript加载。

下面根据[ScriptJunkie](http://msdn.microsoft.com/en-us/scriptjunkie/ff943568)的文章，举一个最简单的例子，来说明这两个函数库的基本用法。更高级的用法，请参阅它们的文档。

> &lt;script src=”script1.js”&gt;&lt;/script&gt;
> 
>  &lt;script src=”script2-a.js”&gt;&lt;/script&gt;
> 
>  &lt;script src=”script2-b.js”&gt;&lt;/script&gt;
> 
>  &lt;script type=”text/javascript”&gt;
> 
>  initScript1();
> 
>  initScript2();
> 
>  &lt;/script&gt;
> 
>  &lt;script src=”script3.js”&gt;&lt;/script&gt;
> 
>  &lt;script type=”text/javascript”&gt;
> 
>  initScript3();
> 
>  &lt;/script&gt;

上面这段代码，将依次加载4个javascript文件：script1.js、script2-a.js、script2-b.js和script3.js。在加载完前三个文件后，运行两个函数initScript1()和initScript2()；加载完第四个文件后，再运行函数initScript3()。

下面，用LABjs对其进行改写：

> &lt;script src=”LAB.js”&gt;&lt;/script&gt;
> 
>  &lt;script type=”text/javascript”&gt;
> 
>  $LAB
> 
>  .script(“script1.js”).wait()
> 
>  .script(“script2-a.js”)
> 
>  .script(“script2-b.js”)
> 
>  .wait(function(){
> 
>  initScript1();
> 
>  initScript2();
> 
>  })
> 
>  .script(“script3.js”)
> 
>  .wait(function(){
> 
>  initScript3();
> 
>  });
> 
>  &lt;/script&gt;

首先，$LAB对象替代了&lt;script&gt;标签，然后.script()方法表示加载Javascript文件，不带参数的.wait()方法表示立即运行刚才加载的Javascript文件，带参数的.wait()方法也是立即运行刚才加载的Javascript文件，但是还运行参数中指定的函数。

这里需要注意的是，可以同时运行多条$LAB链，但是它们之间是完全独立的，不存在次序关系。如果你要确保一个Javascript文件在另一个文件之后运行，你只能把它们写在同一个链操作之中。只有当某些脚本是完全无关的时候，你才应该考虑把它们分成不同的$LAB链，表示它们之间不存在相关关系。

接下来是requireJS的改写：

> &lt;script src=”require.js”&gt;&lt;/script&gt;
> 
>  &lt;script type=”text/javascript”&gt;
> 
>  require(\[
> 
>  ”script1.js”,  
>  ”script2-a.js”,  
>  ”script2-b.js”,  
>  ”script3.js”
> 
>  \],
> 
>  function(){
> 
>  initScript1();  
>  initScript2();  
>  initScript3();
> 
>  }
> 
>  );
> 
>  &lt;/script&gt;

require()接受两个参数，第一个数组表示所要加载的Javascript文件，第二个是加载完成后所要运行的回调函数。原生的require()不支持按次序加载，所以四个Javascript文件到底先加载哪个，无法事前知道，require()只保证这四个文件全部加载完成之后，才会运行所指定的回调函数。

如果按次序加载对你很重要，你可以使用官方提供的[order插件](http://requirejs.org/docs/api.html#order)。