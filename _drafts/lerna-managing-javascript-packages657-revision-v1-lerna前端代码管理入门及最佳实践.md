---
id: 659
title: lerna前端代码管理入门及最佳实践
date: '2019-08-18T23:37:20+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/657-revision-v1/'
permalink: '/?p=659'
---

## 前端开发package面临的问题

在最初开开发package的时候，还属于一种刀耕火种的阶段。没有什么自动化的工具。发布package的时候，都是手动修改**版本号**。如果packages数量不多还可以接受。但是当数量逐渐增多的时候，且这些packages之间还有依赖关系的时候，对开发人员来说，就很痛苦了。工作不仅繁琐，而且需要用掉不少时间。

举个例子，如果你要维护两个package。分别为module-1,module-2。 下面是这两个package的依赖关系。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">/////////module-1   package.json
{

"name": "module-1",

"version": "1.0.0",

"description": "",

"main": "index.js",

"scripts": {

"test": "echo \\"Error: no test specified\\" && exit 1"

},

"keywords": \[\],

"author": "",

"license": "ISC",

"dependencies": {

"module-2": "^1.0.0"

}

}
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">///// module-2  package.json
{

"name": "module-2",

"version": "1.0.0",

"description": "",

"main": "index.js",

"scripts": {

"test": "echo \\"Error: no test specified\\" && exit 1"

},

"keywords": \[\],

"author": "",

"license": "ISC"

}
```

在这样的环境下，module-1是依赖module-2的。如果module-2有修改，需要发布。那么你的工作有这些。

1. 修改module-2版本号，发布。
2. 修改module-1的依赖关系，修改module-1的版本号，发布。

这还仅仅只有两个package,如果依赖关系更复杂，大家可以想想发布的工作量有多大。

## 什么是lerna?为什么要使用lerna?

lerna到底是什么呢？lerna官网上是这样描述的。

> A tool for managing JavaScript projects with multiple packages.

这个介绍可以说很清晰了，引入lerna后，上面提到的问题不仅迎刃而解，更为开发人员提供了一种管理多packages javascript项目的方式。

1. 自动解决packages之间的依赖关系
2. 通过`git` 检测文件改动，自动发布
3. 根据`git` 提交记录，自动生成CHANGELOG

## 使用lerna的基本工作流

### 环境配置

- Git 在一个lerna工程里，是通过git来进行代码管理的。所以你首先要确保本地有正确的git环境。 如果需要多人协作开发，请先创建正确的git中心仓库的链接。 因此需要你了解基本的git操作，在此不再赘述。
- npm仓库 无论你管理的package是要发布到官网还是公司的私有服务器上，都需要正确的仓库地址和用户名。 你可运行下方的命令来检查，本地的npm `registry`地址是否正确。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm config ls
```

- lerna 你需要全局安装lerna工具。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm install lerna -g
```

### 初始化一个lerna工程

> 在这个例子中，我将在我本地`d:/` 根目录下初始化一个lerna工程。

1. 在`d:/` 下创建一个空的文件夹，命名为`lerna-demo`

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">mkdir lerna-demo
```

1. 初始化 通过cmd进入相关目录，进行初始化

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">cd d:/lerna-demo
lerna init
```

执行成功后，目录下将会生成这样的目录结构。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""> - packages(目录)
 - lerna.json(配置文件)
 - package.json(工程描述文件)
```

1. 添加一个测试package

> 默认情况下，package是放在`packages`目录下的。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// 进入packages目录
cd d:/lerna-demo/packages
// 创建一个packge目录
mkdir module-1
// 进入module-1 package目录
cd module-1
// 初始化一个package
npm init -y
```

执行完毕，工程下的目录结构如下

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">--packages
	--module-1
		package.json
--lerna.json
--package.json
```

1. 安装各packages依赖 这一步操作，官网上是这样描述的。

> Bootstrap the packages in the current Lerna repo. Installs all of their dependencies and links any cross-dependencies.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">cd d:/lerna-demo
lerna bootstrap
```

在现在的测试package中，module-1是没有任何依赖的，因此为了更加接近真实情况。你可已在module-1的`package.json` 文件中添加一些第三方库的依赖。 这样的话，当你执行完该条命令后，你会发现module-1的依赖已经安装上了。

1. 发布 在发布的时候，就需要`git` 工具的配合了。 所以在发布之前，请确认此时该lerna工程是否已经连接到git的远程仓库。你可以执行下面的命令进行查看。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">git remote -v
// print log
origin  git@github.com:LittleBreak/lerna-best-practices.git (fetch)
origin  git@github.com:LittleBreak/lerna-best-practices.git (push)
复制代码
```

本篇文章的代码托管在[Github](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FLittleBreak%2Flerna-best-practices)上。因此会显示此远程链接信息。 如果你还没有与远程仓库链接，请首先在github创建一个空的仓库，然后根据相关提示信息，进行链接。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">lerna publish
复制代码
```

执行这条命令，你就可以根据cmd中的提示，一步步的发布packges了。

实际上在执行该条命令的时候，lerna会做很多的工作。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""> -  Run the equivalent of  `lerna updated`  to determine which packages need to be published.
 -  If necessary, increment the  `version`  key in  `lerna.json`.
 -  Update the  `package.json`  of all updated packages to their new versions.
 -  Update all dependencies of the updated packages with the new versions, specified with a  [caret (^)](https://docs.npmjs.com/files/package.json#dependencies).
 -  Create a new git commit and tag for the new version.
 -  Publish updated packages to npm.
复制代码
```

到这里为止，就是一个最简单的lerna的工作流了。但是lerna还有更多的功能等待你去发掘。 lerna有两种工作模式,Independent mode和Fixed/Locked mode，在这里介绍可能会对初学者造成困扰，但因为实在太重要了，还是有必要提一下的。 lerna的默认模式是Fixed/Locked mode，在这种模式下，实际上lerna是把工程当作一个整体来对待。每次发布packges，都是全量发布，无论是否修改。但是在Independent mode下，lerna会配合`Git`，检查文件变动，只发布有改动的packge。

## lerna最佳实践

为了能够使lerna发挥最大的作用，根据这段时间使用`lerna` 的经验，总结出一个最佳实践。下面是一些特性。

1. 采用Independent模式
2. 根据`Git`提交信息，自动生成changelog
3. eslint规则检查
4. prettier自动格式化代码
5. 提交代码，代码检查hook
6. 遵循semver版本规范

大家应该也可以看出来，在开发这种工程的过程的，最为重要的一点就是**规范**。因为应用场景各种各样，你必须保证发布的packge是规范的，代码是规范的，一切都是有迹可循的。这点我认为是非常重要的。

## 工具整合

在这里引入的工具都是为了解决一个问题，就是工程和代码的规范问题。

- husky
- lint-staged
- prettier
- eslint