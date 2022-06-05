---
id: 429
title: 'VSCode使用ts-node 调试TypeScript代码'
date: '2017-08-19T16:54:14+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=429'
permalink: /2017/08/ts-node-run-typescript
categories:
    - Javascript
---

## TS-NODE

可以看出这些不足都来自于一个根本原因，**运行之前需要编译**。后来我就发现了一个很强大的工具[ts-node](https://github.com/TypeStrong/ts-node)，来看下ts-node的简介:

> TypeScript execution environment and REPL for node.

简单的说就是它提供了TypeScript的运行环境，让我们免去了麻烦的编译这一步骤。最简单的例子，在注册ts-node之后，我们就可以直接加载并运行ts文件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">require('ts-node').register();

// 这样就能直接加载并运行 ./ts-code.ts...
require('./ts-code');
```

## TS Config

为了断点调试，我们需要在tsconfig.json中开启*sourceMap*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es5",
    "noImplicitAny": true,
    "outDir": "./dist",
    "sourceMap": true
  },
  "include": [
    "src/**/*"
  ],
}
```

## VSC Launch.json 配置

为ts-node注册一个vsc的debug任务，修改项目的launch.json文件，添加一个新的启动方式

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
  "name": "Current TS File",
  "type": "node",
  "request": "launch",
  "args": [
    "${workspaceRoot}/src/index.ts" // 入口文件
  ],
  "runtimeArgs": [
    "--nolazy",
    "-r",
    "ts-node/register"
  ],
  "sourceMaps": true,
  "cwd": "${workspaceRoot}",
  "protocol": "inspector",
  "console": "integratedTerminal",
  "internalConsoleOptions": "neverOpen"
}
```

通过这些简单的配置，我们在vsc的debug界面中选择*Debug by ts-node*的任务，就可以开始愉快的调试了，修改代码之后直接重启服务即可，这里简单的介绍一些vsc debug相关的快捷键，[参考](https://go.microsoft.com/fwlink/?linkid=832143)

1. F5 – 开始调试、继续执行
2. cmd(ctrl) + shift + F5 – 重启
3. shift + F5 – 结束调试
4. F9 – 添加断点
5. F10 – 单步跳过
6. F11 – 单步调试
7. shift + f11 – 单步跳出

## 调试当前打开ts文件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
  "name": "Current TS File",
  "type": "node",
  "request": "launch",
  "args": [
    "${relativeFile}"
  ],
  "runtimeArgs": [
    "--nolazy",
    "-r",
    "ts-node/register"
  ],
  "sourceMaps": true,
  "cwd": "${workspaceRoot}",
  "protocol": "inspector",
  "console": "integratedTerminal",
  "internalConsoleOptions": "neverOpen"
}
```

## 调试 mocha 测试代码

在launch.json中添加

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
  "name": "Debug Current TS Tests File",
  "type": "node",
  "request": "launch",
  "program": "${workspaceRoot}/node_modules/.bin/_mocha",
  "args": [
    "-r",
    "ts-node/register",
    "${relativeFile}", // 调试当前文件
    "--colors",
    "-t 100000" // 设置超时时间，因为调试时执行时间较长容易触发超时
  ],
  "cwd": "${workspaceRoot}",
  "protocol": "inspector"
}
```

然后打开一个包含mocha单元测试的ts文件，添加断点，运行*Debug Current TS Tests File*即可进行断点调试。  
运行项目中的所有单元测试建议在package.json中添加test脚本，比如

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">"scripts": {
  "test": "mocha -r ts-node/register src/**/*.spec.ts --colors"
}
```

然后运行*npm test*即可