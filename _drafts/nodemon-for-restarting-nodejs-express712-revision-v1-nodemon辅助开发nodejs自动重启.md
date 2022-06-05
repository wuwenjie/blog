---
id: 713
title: nodemon辅助开发nodejs自动重启
date: '2019-08-20T22:33:07+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/712-revision-v1/'
permalink: '/?p=713'
---

## 前面的话

 修改代码后，需要重新启动 Express 应用，所做的修改才能生效。若之后的每次代码修改都要重复这样的操作，势必会影响开发效率，本文将详细介绍Nodemon，它会监测项目中的所有文件，一旦发现文件有改动，Nodemon 会自动重启应用

### 安装及使用

 全局安装 nodemon 包，这样新创建的 Node.js 应用都能使用 Nodemon 运行起来了

```
<pre class="wp-block-preformatted">npm install -g nodemon
```

 安装完成之后，Nodemon 就可以启动 Express 应用了，先关闭当前正在执行的应用程序，然后再执行命令：

```
<pre class="wp-block-preformatted">nodemon index.js
```

 默认地，nodemon会自动打开index.js。所以，也可以使用如下命令

```
<pre class="wp-block-preformatted">nodemon
```

 通过 Nodemon 启动应用之后，不管是修改了代码，还是安装了新的 npm 包，Nodemon 都会重新启动应用

### 配置文件

 Nodemon 默认会监听当前目录下（也就是执行 nodemon 命令所在的目录）的所有文件，不过有些情况下，虽然项目文件发生了改动，但是不需要 Nodemon 重启应用，那如何让文件不被 Nodemon 监听呢？不需要监听的文件，可以通过设置 Nodemon 的配置文件排除掉，新建文件 server/nodemon.json，添加代码：

```
<pre class="wp-block-preformatted">{
  "ignore": [
    "config.default.js"
  ]
}
```

 Nodemon 配置文件是 JSON 文件，通过设置 ignore 属性值，一个由文件名组成的字符串数组，指定不需要监听的文件

### 手动重启

 有时候可能 Nodemon 还在运行的时候，需要手动重启它，在这种情况下不需要关闭正在运行的 Nodemon 进程然后再重启 Nodemon，只要在 Nodemon 命令运行的终端 窗口中输入 rs 两个字符，然后再按下回车键，就能重启 Nodemon 了

```
<pre class="wp-block-preformatted">rs 
```

 当然，Nodemon 不是只专门服务于 Node.js 应用的，它还可以用于其它语言开发的应用，比如Typescript也是经常用到。