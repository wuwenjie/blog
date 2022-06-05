---
id: 802
title: WebAssembly的初步了解
date: '2019-09-17T15:21:08+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=802'
permalink: /2019/09/webassembly-new-web-technology
categories:
    - Javascript
    - 教程
---

## 技术背景

2017年2月28日，四大浏览器，IE，FF，Chrome，Safari宣布达成共识，即 WebAssembly 的 MVP （最小化可行产品）已经完成。大约一周后，Firefox会默认打开 WebAssembly 支持，而Chrome则在第二周开始。它也可用于预览版本的Edge和Safari。如今过去两年多了，发展还是挺迅速的。

使用WebAssembly，可以更快地在 web 应用上运行代码。这里有 几个 WebAssembly 代码运行速度比 JavaScript 高效的原因。

文件加载 – WebAssembly 文件体积更小，所以下载速度更快。

解析 – 解码 WebAssembly 比解析 JavaScript 要快

编译和优化 – 编译和优化所需的时间较少，因为在将文件推送到服务器之前已经进行了更多优化，JavaScript 需要为动态类型多次编译代码

重新优化 – WebAssembly 代码不需要重新优化，因为编译器有足够的信息可以在第一次运行时获得正确的代码

执行 – 执行可以更快，WebAssembly 指令更接近机器码

垃圾回收 – 目前 WebAssembly 不直接支持垃圾回收，垃圾回收都是手动控制的，所以比自动垃圾回收效率更高。

目前浏览器中的 MVP（最小化可行产品） 已经很快了。在接下来的几年里，随着浏览器的发展和新功能的增加，它将在未来几年内变得更快。没有人可以肯定地说，这些性能改进可以实现什么样的应用。但是，如果过去有任何迹象，我们可以期待惊奇。

## 什么时候使用WebAssembly？

说了这么多，我到底什么时候该使用它呢？总结下来，大部分情况分两个点。

- 对性能有很高要求的App/Module/游戏
- 在Web中使用C/C++/Rust/Go的库  
    举个简单的例子。如果你要实现的Web版本的Ins或者Facebook， 你想要提高效率。那么就可以把其中对图片进行压缩、解压缩、处理的工具，用C++实现，然后再编译回WebAssembly。

## WebAssembly的几个开发工具

- [AssemblyScript](https://github.com/AssemblyScript/assemblyscript)。支持直接将TypeScript编译成WebAssembly。这对于很多前端同学来说，入门的门槛还是很低的。
- [Emscripten](https://github.com/kripken/emscripten)。可以说是WebAssembly的灵魂工具不为过，上面说了很多编译，这个就是那个编译器。将其他的高级语言，编译成WebAssembly。
- [WABT](https://github.com/WebAssembly/wabt)。是个将WebAssembly在字节码和文本格式相互转换的一个工具，方便开发者去理解这个wasm到底是在做什么事。

## WebAssembly的意义

在我的个人理解上，WebAssembly并没有要替代JavaScript，一统天下的意思。我总结下来就两个点。

- 给了Web更好的性能
- 给了Web更多的可能  
    关于WebAssembly的性能问题，之前也花了很大的篇幅讲过了。而更多的可能，随着WebAssembly的技术越来越成熟，势必会有更多的应用，从Desktop被搬到Web上，这会使本来已经十分强大的Web更加丰富和强大。

## WebAssembly实操

要进行这个实际操作，你需要安装上文提到过的编译器[Emscripten](https://github.com/kripken/emscripten)，然后按照[这个](http://webassembly.org.cn/getting-started/developers-guide/)步骤去安装。以下的步骤都默认为你已经安装了Emscripten。

### WebAssembly在Node中的应用

#### 导入Emscripten环境变量

进入到你的emscripten安装目录，执行以下代码。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">source emsdk/emsdk_env.sh
```

#### 新建C文件

用C实现一个求和文件`test.c`，如下。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">int add(int a, int b) {
    return a + b;
}
```

#### 使用Emscripten编译C文件

在同样的目录下执行如下代码。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">emcc test.c -Os -s WASM=1 -s SIDE_MODULE=1 -o test.wasm
```

`emcc`就是Emscripten编译器，`test.c`是我们的输入文件，`-Os`表示这次编译需要优化，`-s WASM=1`表示输出wasm的文件，因为默认的是输出asm.js，`-s SIDE_MODULE=1`表示就只要这一个模块，不要给我其他乱七八糟的代码，`-o test.wasm`是我们的输出文件。

编译成功之后，当前目录下就会生成`test.wasm`。

#### 编写在Node中调用的代码

新建一个js文件`test.js`。代码如下。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const fs = require('fs');
let src = new Uint8Array(fs.readFileSync('./test.wasm'));
const env = {
    memoryBase: 0,
    tableBase: 0,
    memory: new WebAssembly.Memory({
        initial: 256
    }),
    table: new WebAssembly.Table({
        initial: 2,
        element: 'anyfunc'
    }),
    abort: () => {throw 'abort';}
}
WebAssembly.instantiate(src, {env: env})
.then(result => {
    console.log(result.instance.exports._add(20, 89));
})
.catch(e => console.log(e));
```

#### 执行test.js

运行以下代码。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">node test.js
```

然后就可以看到输出的结果109了。

### WebAssembly在React当中的应用

#### 通过fetch的方法调用

直接用fetch的方式。大概的调用方式如下。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const fibonacciUrl = './fibonacci.wasm';
const {_fibonacci} = await this.getExportFunction(fibonacciUrl);
```

而`getExportFunction`具体代码如下。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">getExportFunction = async (url) => {
    const env = {
      memoryBase: 0,
      tableBase: 0,
      memory: new WebAssembly.Memory({
        initial: 256
      }),
      table: new WebAssembly.Table({
        initial: 2,
        element: 'anyfunc'
      })
    };
    const instance = await fetch(url).then((response) => {
      return response.arrayBuffer();
    }).then((bytes) => {
      return WebAssembly.instantiate(bytes, {env: env})
    }).then((instance) => {
      return instance.instance.exports;
    });
    return instance;
};
```

#### 通过import C文件来调用

先通过Import的方式来引进依赖。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import wasmC from './add.c';
```

然后进行调用。具体的方式如下。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">wasmC({
  'global': {},
  'env': {
    'memoryBase': 0,
    'tableBase': 0,
    'memory': new WebAssembly.Memory({initial: 256}),
    'table': new WebAssembly.Table({initial: 0, element: 'anyfunc'})
  }
}).then(result => {
  const exports = result.instance.exports;
  const add = exports._add;
  const fibonacci = exports._fibonacci;
  console.log('C return value was', add(2, 5643));
  console.log('Fibonacci', fibonacci(2));
});
```

## WebAssembly在大型项目中的应用

在这里能够举的例子还是很多，比如AutoCAD、GoogleEarth、Unity、Unreal、PSPDKit、WebPack等等。拿其中几个来简单说一下。

### AutoCAD

这是一个用于画图的软件，在很长的一段时间是没有Web的版本的，原因有两个，其一，是Web的性能的确不能满足他们的需求。其二，在WebAssembly没有面世之前，AutoCAD是用C++实现的，要将其搬到Web上，就意味着要重写他们所有的代码，这代价十分的巨大。

而在WebAssembly面世之后，AutoCAD得以利用编译器，将其沉淀了30多年的代码直接编译成WebAssembly，同时性能基于之前的普通Web应用得到了很大的提升。正是这些原因，得以让AutoCAD将其应用从Desktop搬到Web中。

### Google Earth

Google Earth也就是谷歌地球，因为需要展示很多3D的图像，对性能要求十分高，所以采取了一些Native的技术。最初的时候就连Google Chrome浏览器都不支持Web的版本，需要单独下载Google Earth的Destop应用。而在WebAssembly之后呢，谷歌地球推出了Web的版本。而据说下一个可以运行谷歌地球的浏览器是FireFox。

### Unity和Unreal游戏引擎

这里给两个油管的链接自己体验一下，大家注意上网的方式。

- Unity WebGL的戳[这里](https://youtu.be/rIyIlATjNcE)
- Unreal引擎的戳[这里](https://www.youtube.com/watch?v=TwuIRcpeUWE)