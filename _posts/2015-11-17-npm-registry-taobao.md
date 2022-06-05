---
id: 495
title: npm淘宝镜像的使用
date: '2015-11-17T21:56:09+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=495'
permalink: /2015/11/npm-registry-taobao
categories:
    - Javascript
---

外网在国内访问太慢了，所以一般大家都把镜像改成taobao镜像会加快安装速度。。。

## 一、最顶级的使用

1、安装cnpm

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm i -g cnpm --registry=https://registry.npm.taobao.org
```

2、然后就可以cnpm安装依赖包了

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">cnpm i -g  vue vue-cli
```

3、cnpm config ls 查看

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">E:\我的项目\2018-08>cnpm config ls
; cli configs
disturl = "https://npm.taobao.org/mirrors/node"
metrics-registry = "https://registry.npm.taobao.org/"
registry = "https://registry.npm.taobao.org/"
scope = ""
user-agent = "npm/6.3.0 node/v8.11.3 win32 x64"
userconfig = "C:\\Users\\Administrator\\.cnpmrc"

; node bin location = D:\Program Files\nodejs\node.exe
; cwd = E:\我的项目\2018-08
; HOME = C:\Users\Administrator
; "npm config ls -l" to show all defaults.


E:\我的项目\2018-08>
```

会发现里面的registry变成了淘宝的镜像（仓库）：<https://registry.npm.taobao.org/>

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">【本文里面的“镜像”等同于“仓库”，下同】
```

# 二、原始的npm

1、查看原始配置 npm config ls

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">E:\我的项目\2018-08>npm config ls
; cli configs
metrics-registry = "https://registry.npmjs.org/"
scope = ""
user-agent = "npm/5.6.0 node/v8.11.3 win32 x64"

; userconfig C:\Users\Administrator\.npmrc
cache = "D:\\Program Files\\nodejs\\node_cache"
prefix = "D:\\Program Files\\nodejs\\node_global"

; builtin config undefined

; node bin location = D:\Program Files\nodejs\node.exe
; cwd = E:\我的项目\2018-08
; HOME = C:\Users\Administrator
; "npm config ls -l" to show all defaults.


E:\我的项目\2018-08>
```

会发现里面的registry是npm原始的镜像：<https://registry.npmjs.org/>

2、npm临时使用淘宝镜像安装依赖包

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm i -g express --registry https://registry.npm.taobao.org
```

3、npm持久使用淘宝镜像安装依赖包

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm config set registry https://registry.npm.taobao.org
npm i -g express
```

注意，不推荐这样子，因为把npm的镜像完全设为了淘宝的镜像，万一我们有些依赖包只有npm原始镜像里面才有，而淘宝里面没有，那就悲剧了。所以分开npm和cnpm是最好的。

# 三、一些常用设置

1、查看【npm 与 cnpm 是2个不同的】

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm config ls
cnpm config ls
```

2、设置：主要是设置`cache`和`prefix`

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm cofig set cache "D:\Program Files\nodejs\node_cache"
npm cofig set prefix "D:\Program Files\nodejs\node_global"

cnpm cofig set cache "D:\Program Files\nodejs\node_cache"
cnpm cofig set prefix "D:\Program Files\nodejs\node_global"
```

3、最后的结果

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">E:\我的项目\2018-08>cnpm config ls
; cli configs
disturl = "https://npm.taobao.org/mirrors/node"
metrics-registry = "https://registry.npm.taobao.org/"
registry = "https://registry.npm.taobao.org/"
scope = ""
user-agent = "npm/6.3.0 node/v8.11.3 win32 x64"
userconfig = "C:\\Users\\Administrator\\.cnpmrc"

; userconfig C:\Users\Administrator\.cnpmrc
cache = "D:\\Program Files\\nodejs\\node_cache"
prefix = "D:\\Program Files\\nodejs\\node_global"

; node bin location = D:\Program Files\nodejs\node.exe
; cwd = E:\我的项目\2018-08
; HOME = C:\Users\Administrator
; "npm config ls -l" to show all defaults.


E:\我的项目\2018-08>npm config ls
; cli configs
metrics-registry = "https://registry.npmjs.org/"
scope = ""
user-agent = "npm/5.6.0 node/v8.11.3 win32 x64"

; userconfig C:\Users\Administrator\.npmrc
cache = "D:\\Program Files\\nodejs\\node_cache"
prefix = "D:\\Program Files\\nodejs\\node_global"

; builtin config undefined

; node bin location = D:\Program Files\nodejs\node.exe
; cwd = E:\我的项目\2018-08
; HOME = C:\Users\Administrator
; "npm config ls -l" to show all defaults.


E:\我的项目\2018-08>
```