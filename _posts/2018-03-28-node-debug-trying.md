---
id: 602
title: node调试工具入门
date: '2018-03-28T18:35:41+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=602'
permalink: /2018/03/node-debug-trying
categories:
    - Javascript
---

JavaScript 程序越来越复杂，调试工具的重要性日益凸显。客户端脚本有浏览器，Node 脚本怎么调试呢？

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031901.png)</figure>2016年，Node 决定将 Chrome 浏览器的”开发者工具”作为官方的调试工具，使得 Node 脚本也可以使用图形界面调试，这大大方便了开发者。

本文介绍如何使用 Node 脚本的调试工具。

## 一、示例程序

为了方便讲解，下面是一个示例脚本。首先，新建一个工作目录，并进入该目录。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ mkdir debug-demo
$ cd debug-demo
```

然后，生成`package.json`文件，并安装 [Koa](http://www.ruanyifeng.com/blog/2017/08/koa.html) 框架和 koa-route 模块。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ npm init -y
$ npm install --save koa koa-route
```

接着，新建一个脚本`app.js`，并写入下面的内容。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
// app.js
const Koa = require('koa');
const router = require('koa-route');

const app = new Koa();

const main = ctx => {
  ctx.response.body = 'Hello World';
};

const welcome = (ctx, name) => {
  ctx.response.body = 'Hello ' + name;
};

app.use(router.get('/', main));
app.use(router.get('/:name', welcome));

app.listen(3000);
console.log('listening on port 3000');
```

上面代码是一个简单的 Web 应用，指定了两个路由，访问后会显示一行欢迎信息。如果想了解代码的详细含义，可以参考 [Koa 教程](http://www.ruanyifeng.com/blog/2017/08/koa.html)。

## 二、启动开发者工具

现在，运行上面的脚本。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ node --inspect app.js
```

上面代码中，`--inspect`参数是启动调试模式必需的。这时，打开浏览器访问`http://127.0.0.1:3000`，就可以看到 Hello World 了。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031902.png)</figure>接下来，就要开始调试了。一共有两种打开调试工具的方法，第一种是在 Chrome 浏览器的地址栏，键入 `chrome://inspect`或者`about:inspect`，回车后就可以看到下面的界面。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031903.png)</figure>在 Target 部分，点击 inspect 链接，就能进入调试工具了。

第二种进入调试工具的方法，是在 http://127.0.0.1:3000 的窗口打开”开发者工具”，顶部左上角有一个 Node 的绿色标志，点击就可以进入。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031904.png)</figure>## 三、调试工具窗口

调试工具其实就是”开发者工具”的定制版，省去了那些对服务器脚本没用的部分。

它主要有四个面板。

- Console：控制台
- Memory：内存
- Profiler：性能
- Sources：源码

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031905.png)</figure>这些面板的用法，基本上跟浏览器环境差不多，这里只介绍 Sources （源码）面板。

## 四、设置断点

进入 Sources 面板，找到正在运行的脚本`app.js`。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031906.png)</figure>在第11行（也就是下面这一行）的行号上点一下，就设置了一个断点。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
ctx.response.body = 'Hello ' + name;
```

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031907.png)</figure>这时，浏览器访问 http://127.0.0.1:3000/alice ，页面会显示正在等待服务器返回。切换到调试工具，可以看到 Node 主线程处于暂停（paused）阶段。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031908.png)</figure>进入 Console 面板，输入 name，会返回 alice。这表明我们正处在断点处的上下文（context）。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031909.png)</figure>再切回 Sources 面板，右侧可以看到 Watch、Call Stack、Scope、Breakpoints 等折叠项。打开 Scope 折叠项，可以看到 Local 作用域和 Global 作用域里面的所有变量。

Local 作用域里面，变量`name`的值是`alice`，双击进入编辑状态，把它改成`bob`。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031910.png)</figure>然后，点击顶部工具栏的继续运行按钮。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031911.png)</figure>页面上就可以看到 Hello bob 了。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031912.png)</figure>命令行下，按下 ctrl + c，终止运行`app.js`。

## 五、调试非服务脚本

Web 服务脚本会一直在后台运行，但是大部分脚本只是处理某个任务，运行完就会终止。这时，你可能根本没有时间打开调试工具。等你打开了，脚本早就结束运行了。这时怎么调试呢？

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ node --inspect=9229 -e "setTimeout(function() { console.log('yes'); }, 30000)"
```

上面代码中，`--inspect=9229`指定调试端口为 9229，这是调试工具默认的通信端口。`-e`参数指定一个字符串，作为代码运行。

访问`chrome://inspect`，就可以进入调试工具，调试这段代码了。

<figure class="wp-block-image">![](http://www.ruanyifeng.com/blogimg/asset/2018/bg2018031913.png)</figure>代码放在`setTimeout`里面，总是不太方便。那些运行时间较短的脚本，可能根本来不及打开调试工具。这时就要使用下面的方法。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ node --inspect-brk=9229 app.js
```

上面代码中，`--inspect-brk`指定在第一行就设置断点。也就是说，一开始运行，就是暂停的状态。

## 六、忘了写 –inspect 怎么办？

打开调试工具的前提是，启动 Node 脚本时就加上`--inspect`参数。如果忘了这个参数，还能不能调试呢？

回答是可以的。首先，正常启动脚本。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ node app.js
```

然后，在另一个命令行窗口，查找上面脚本的进程号。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ ps ax | grep app.js 

30464 pts/11   Sl+    0:00 node app.js
30541 pts/12   S+     0:00 grep app.js
```

上面命令中，`app.js`的进程号是`30464`。

接着，运行下面的命令。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ node -e 'process._debugProcess(30464)'
```

上面命令会建立进程 30464 与调试工具的连接，然后就可以打开调试工具了。

还有一种方法，就是向脚本进程发送 [SIGUSR1](https://stackoverflow.com/questions/13052548/node-js-how-to-attach-to-a-running-process-and-to-debug-the-server-with-a-conso) 信号，也可以建立调试连接。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
$ kill -SIGUSR1 30464
```

## 七、参考链接

- [Debugging Node.js with Google Chrome](https://medium.com/the-node-js-collection/debugging-node-js-with-google-chrome-4965b5f910f4), by Jacopo Daeli
- [Debugging Node.js with Chrome DevTools](https://medium.com/@paul_irish/debugging-node-js-nightlies-with-chrome-devtools-7c4a1b95ae27), by Paul Irish
- [Last minute node debugging](https://remysharp.com/2018/03/03/last-minute-node-debugging), by Remy Sharp