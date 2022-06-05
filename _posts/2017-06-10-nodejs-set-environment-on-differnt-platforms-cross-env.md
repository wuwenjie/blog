---
id: 570
title: Nodejs跨平台环境变量设置cross-env
date: '2017-06-10T14:51:56+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=570'
permalink: /2017/06/nodejs-set-environment-on-differnt-platforms-cross-env
categories:
    - Javascript
---

在搭建公司新的前端工程的架构中，需要在在`package.json`的`scripts`标签下配置一系列命令，如下所示：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">"scripts": {
    "clear": "rm -rf build&& mkdir build",
    "start": "npm run clear&& NODE_ENV=development webpack-dev-server --host 0.0.0.0 --devtool eval --progress --color --profile",
    "deploy": "npm run pre&& npm run clear&& NODE_ENV=production webpack -p --progress"
  },
```

上面配置中的的`&&`最开始使用的是`;`，后来发现`;`在windows环境中无法正常运行，于是改成了\*unix和windows都兼容的`&&`。但是公司的部分使用windows的同事在运行`npm start`的时候，依然会报错：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">'NODE_ENV' 不是内部或外部命令，也不是可运行的程序或批处理文件。 npm ERR! Windows_NT 6.1.7601npm ERR! argv "D:\\nodejs\\node.exe" "D:\\nodejs\\node_modules\\npm\\bin\\npm-cli.js" "start"npm ERR! node v4.0.0-rc.5npm ERR! npm  v2.14.2npm ERR! code ELIFECYCLEnpm ERR! yy-ydh-web@1.0.7 start: `npm run clear&& NODE_ENV=development && webpack-dev-server --host 0.0.0.0 --devtool eval --progress --color --profile`npm ERR! Exit status 1npm ERR!npm ERR! Failed at the yy-ydh-web@1.0.7 start script 'npm run clear&& NODE_ENV=development && webpack-dev-server --host0.0.0.0 --devtool eval --progress --color --profile'.npm ERR! This is most likely a problem with the yy-ydh-web package,npm ERR! not with npm itself.npm ERR! Tell the author that this fails on your system:npm ERR!     npm run clear&& NODE_ENV=development && webpack-dev-server --host 0.0.0.0 --devtool eval --progress --color --profilenpm ERR! You can get their info via:npm ERR!     npm owner ls yy-ydh-webnpm ERR! There is likely additional logging output above. npm ERR! Please include the following file with any support request:npm ERR!     D:\workspace\node_modules\yy-ydh-web\npm-debug.log
```

简单来说，就是windows不支持`NODE_ENV=development`的设置方式。

### 解决方式[cross-env](https://www.npmjs.com/package/cross-env)

### 使用方法：

- 安装across-env:`npm install cross-env --save-dev`
- 在`NODE_ENV=xxxxxxx`前面添加`cross-env`就可以了。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">set NODE_ENV=development && webpack --config webpack.config.dev.js
```