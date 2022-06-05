---
id: 778
title: 'SVG 图标在React项目中的优化'
date: '2019-08-28T19:34:58+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/772-revision-v1'
permalink: '/?p=778'
---

## 一、在 webpack 中有各种 loader 可以加载 SVG：

#### **1、url-loader**

官方文档：<https://webpack.docschina.org/loaders/url-loader/>

可以把 svg 当作普通 jpg、png 图片来使用。

安装：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm install url-loader --save-dev
```

webpack 配置：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">test: /\.(png|jpg|gif|svg)$/i,
use: [
    {
        loader: 'url-loader',
    }
]
```

在 React 组件中的引入方式：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import test from './test.svg'
export default class App extends Component {
  render() {
    return (
       <img src={test} />
    );
  }
};
```

经过 url-loader 处理后，test.svg 文件被处理为 “data:image/svg+xml;base64,PHN2ZyBpZ…..” 这样的 base64 编码。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">1
module.exports = "data:image/svg+xml;base64,PHN2ZyBpZ..
```

我们用 **webpack-bundle-analyzer** 插件测试看看 svg 文件被打包后的大小：

<figure class="wp-block-image">[![image-20190727213907016](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727213907016.png)](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727213907016.png)</figure>此外 file-loader 也可以。

这种方式的优点： SVG 资源可被缓存，SVG 资源可单独加载。

缺点：加载多个 SVG 文件时，会产生多个 http 请求，影响页面加载性能。

#### **2、svg-react-loader**

安装：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">	
npm install svg-react-loader --save-dev
```

webpack 配置：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
    test: /\.svg$/,
    loader: 'svg-react-loader',
}
```

在 React 组件中的引入方式：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import Test from "./svg/test.svg";
export default class App extends Component {
    render() {
        return <Test className={iconType} />
    }
}
```

svg-react-loader 会将 svg 文件处理成 React 组件，最后会以 svg 标签的形式渲染到 html 中。从最后渲染到 html 中的代码来看，svg-react-loader 是有对 svg 原文件进行优化的。从打包后的文件大小可以看出来文件有被压缩：

<figure class="wp-block-image">[![image-20190727214315507](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727214315507.png)](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727214315507.png)</figure>这种方式的缺点：SVG 资源不可被缓存。且会将 svg 资源的处理逻辑与页面的交互逻辑一起打包。

最好的方式是：SVG 资源与 JS 资源分离，图片的加载不影响页面的主要执行逻辑。并且所有的 SVG 资源希望能一次 HTTP 请求就能搞定。

#### 3、svg-inline-loader

官方文档：<https://webpack.js.org/loaders/svg-inline-loader/>

svg-inline-loader 会根据配置项去除 SVG 中冗余的代码或者不必要的代码，以减少 SVG 的文件大小。

安装：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm install svg-inline-loader --save-dev
```

webpack 配置：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
    test: /\.svg$/,
    loader: 'svg-inline-loader'
}
```

在 React 组件中的引入方式：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import test from "./test.svg";
 
export default class App extends Component {
  render() {
    return (
        <span dangerouslySetInnerHTML={{__html: test}} />
    );
  }
};
```

经过 svg-inline-loader 处理后 svg 文件被处理为这样的字符串：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">module.exports = \"<svg xmlns=\\\"http://www.w3.org/2000/svg\\\">....
```

看下打包大小，相对比，这个体积优化的效果还不错。

<figure class="wp-block-image">[![image-20190727220013803](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727220013803.png)](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727220013803.png)</figure>此外还有 raw-loader 等都可以处理 SVG 文件。

### 二、SVG 优化

#### 1. SVG 文件压缩

一般设计师会使用 Adobe suite 或者 Sketch 等工具导出 SVG 文件，但这样的 SVG 一般是有属性冗余的，所以需要对 SVG 进行一定的压缩。

手动压缩：当然，也不需要手动压缩，但是可以看看哪些属性是有冗余的。

工具压缩：推荐使用 SVGO。

webpack 项目中引入 SVGO：

安装：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">	
npm install svgo svgo-loader --save-dev
```

webpack 配置：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
    test: /\.svg$/,
    exclude: /node_modules/,
    loaders: [
        'url-loader',
        'svgo-loader'
    ],
},
```

在单独使用 url-loader 时，文件 test.svg 打包后的大小是：17.82 KB。在使用 svgo-loader 后，我们看下打包大小，确实是有很大幅度的压缩。

<figure class="wp-block-image">[![image-20190727222022385](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727222022385.png)](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727222022385.png)</figure>#### 2. SVG 雪碧图

当项目需要加载多个 SVG 文件时，上述加载方式就需要优化了。我们考虑以下几种情况：

1）使用 url-loader 加载多个 svg 文件

<figure class="wp-block-image">[![image-20190727230155985](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727230155985.png)](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190727230155985.png)</figure>这种方式会产生多次 http 请求，而浏览器对并发请求数目是有限制的，请求太多会影响页面加载性能。这种情况就需要引入雪碧图，将多个 svg 文件合成一个 svg 文件。

2）使用 svg-react-loader ，当一个组件需要加载多个 svg 文件时，所有的 svg 文件都会被打包到 index.js 文件中。如果 svg 文件过多，就会增大 index.js 文件体积。而 SVG 作为图片资源，最好是作为一个单独文件异步加载，与页面主要执行逻辑分离。当然这里也同样需要引入雪碧图。

##### 第一种方法

第一种方法是把所有的图标通过`<symbol>` 元素定义在 SVG 代码中，嵌入在`<symbol>` 元素中的图标是不会被直接显示的。通过使用`<use>` 元素的 xlink:href=”#id” 来引用单个图标。

也就是说合成后的大 SVG 会是这样：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" id="__SVG_SPRITE_NODE__">
    <symbol class="icon" viewBox="0 0 1024 1024" id="icon名1">{{省略的icon path}}</symbol>
    <symbol class="icon" viewBox="0 0 1024 1024" id="icon名2">{{省略的icon path}}</symbol>
</svg>
```

在需要引入单个图标的时候：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><use xlink:href="#symbolId"></use>
```

这里我们使用 **svg-sprite-loader** 来自动生成 svg 雪碧图。

安装：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm install svg-sprite-loader --save-dev
```

loader 配置：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
     test: /\.svg$/,
     exclude: /node_modules/,
     use: [{
         loader: 'svg-sprite-loader',
     }],
}
```

react 组件中引入：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import "./svg/node.svg";
import "./svg/test.svg";
import "./svg/logo.svg";
export default class App extends Component {
    render() {
        return (
            <div>
                <svg><use xlinkHref="#node" /></svg>
                <svg><use xlinkHref="#test" /></svg>
                <svg><use xlinkHref="#logo" /></svg>
            </div>
        );
    }
}
```

这样配置打包后，body 下会自动注入一个大的 SVG 元素，达到了雪碧图的效果。可是这样还是有两个缺点：1）当需要使用这些 svg 的时候，需要在组件中单独 import。2）这个大的 SVG 资源并没有与 JS 资源分离。

<figure class="wp-block-image">[![image-20190728141342715](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190728141342715%E7%9A%84%E5%89%AF%E6%9C%AC.png)](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190728141342715%E7%9A%84%E5%89%AF%E6%9C%AC.png)</figure><figure class="wp-block-image">[![image-20190728142106996](http://www.alloyteam.com/wp-content/uploads/2019/07/QQ20190731-225113@2x.png)](http://www.alloyteam.com/wp-content/uploads/2019/07/QQ20190731-225113@2x.png)</figure>为了解决第一个缺点：在组件加载前，增加自动 import 所有 svg 的功能：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// requires and returns all modules that match
 
const requireAll = requireContext => requireContext.keys().map(requireContext);
 
// import all svg
 
const req = require.context('./svg', true, /\.svg$/);
 
requireAll(req);
```

现在可以自动导入了，不用到每个组件中去单独 import 了。可是还需要解决第二个问题：SVG 资源与 JS 资源分离。

从 svg-sprite-loader 文档中看到可以利用 spriteloaderplugin 插件将所有 SVG 文件打包输出为一个大的 SVG：

这里的 loader 配置：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
    test: /\.svg$/,
    exclude: /node_modules/,
    loaders: [
        {
            loader: 'svg-sprite-loader',
            options: {
                  extract: true,
                  spriteFilename:  'sprite.svg', // 生成的 SVG 雪碧图资源路径
             }
        },
        'svgo-loader'
   ],
},
```

插件配置：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">plugins: [
    new SpriteLoaderPlugin({
        plainSprite: true
    })
]
```

这种方式会在打包目录下生成 sprite.svg 文件，我们可以通过 ajax 请求的方式获取到该 svg 资源，然后将其注入到 html 中去，这样就将 svg 资源与 js 资源分离了。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fetch(`sprite.svg`).then(res => {
    return res.text();
}).then(data => {
    document.getElementById('svg-sprite').innerHTML = data;
});
```

这样的方式是我们自己手动去请求的，思考下能否使用 webpack 配置或者一些插件就能达到 svg 与 js 资源分离的目的呢。于是想到了 webpack 的 SplitChunksPlugin 插件提供的抽取公共代码的能力。

于是尝试把 import 所有 svg 的逻辑抽离出来成为一个单独的 js 文件。配置好 SplitChunks 后，webpack 就会把这部分逻辑抽离出来，单独打包成一个 js 文件。这样就实现了 svg 与 js 资源分离了。

<figure class="wp-block-image">[![image-20190731212610492](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190731212610492.png)](http://www.alloyteam.com/wp-content/uploads/2019/07/image-20190731212610492.png)</figure>webpack 配置：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">optimization: {
        splitChunks: {
          cacheGroups: {
            commons: {
              name: 'commons',
              chunks: 'initial',
              minChunks: 2
            }
          }
        }
     },
```

##### 第二种方法

第二种方法是使用 SVG 的 viewbox 属性来指定显示 SVG 画布的区域，跟 background-position 原理差不多。待探索~