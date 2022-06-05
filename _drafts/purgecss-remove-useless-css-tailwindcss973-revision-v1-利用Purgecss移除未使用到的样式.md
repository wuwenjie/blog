---
id: 978
title: 利用Purgecss移除未使用到的样式
date: '2020-04-16T11:15:16+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2020/04/973-revision-v1'
permalink: '/?p=978'
---

 我们做项目时，通常会使用一些常用的css框架，比如我会经常用到 [tailwindcss](https://tailwindcss.com/) ，bootstrap 等等，但是使用这些框架后，打包出来的css文件过于庞大，而且很多样式是我们没有使用到的，完全没有必要打包进去。还有一种情况是我们的网站进行改版了，有些样式已经不再使用了，但是我们没有在css文件中移除，导致css越来越大。这时候我们就可以用 Purgecss来只打包我们项目中正在使用到的样式来减小css文件体积。本篇文章使用 [tailwindcss](https://tailwindcss.com/) 在 vue中的例子进行说明：

1\. 我们用vue-cli创建一个vue项目

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="shell" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">vue create test-purgecss
```

 2. 安装tailwindcss

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="shell" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">cd test-purgecss
npm i tailwindcss -save
npm i @fullhuman/postcss-purgecss --save-dev
```

3\. 引入tailwindcss

在assets文件夹下，新建index.css , 写入以下内容

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="css" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@tailwind base;
@tailwind components;
@tailwind utilities;
```

在 main.js中引入

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import './assets/index.css';
```

4\. 在根目录下新建postcss.config.js，写入以下内容

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const autoprefixer = require("autoprefixer");
const tailwindcss = require("tailwindcss");

const postcssPurgecss = require(`@fullhuman/postcss-purgecss`);
const purgecss = postcssPurgecss({
  content: [ `./public/**/*.html`, `./src/**/*.vue` ],
  defaultExtractor (content) {
    const contentWithoutStyleBlocks = content.replace(/<style[^]+?<\/style>/gi, '')
    return contentWithoutStyleBlocks.match(/[A-Za-z0-9-_/:]*[A-Za-z0-9-_/]+/g) || []
  },
  whitelist: [],
  whitelistPatterns: [ /-(leave|enter|appear)(|-(to|from|active))$/, /^(?!(|.*?:)cursor-move).+-move$/, /^router-link(|-exact)-active$/, /data-v-.*/ ],
});

module.exports = {
  plugins: [
    tailwindcss,
    autoprefixer,
    ...(process.env.NODE_ENV === "production" ? [purgecss] : []),
  ],
};
```

 我们运行 npm run build后，会发现dist中生成的css，只有我们用到的，并不是所有的thaiwindcss，非常的小，所以已经达到了我们的目的。

在react中或者其他地方中的使用方式，可以去purgecss官网进行查看，本篇文章参考： <https://purgecss.com/guides/vue.html#use-the-vue-cli-plugin>