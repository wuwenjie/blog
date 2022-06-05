---
id: 485
title: CSS中的@关键字
date: '2015-10-17T19:07:30+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=485'
permalink: /2015/10/css-symbol-at-keywords
categories:
    - 'HTML&amp;CSS'
---

大家可能在CSS中见到过字符`@`然后加一些关键字的用法，这种用法就称之为AT规则，在CSS中，种类还是很多的，这里总结列举下。

### 常规规则

所谓“常规规则”指的是语法类似下面的规则：

```
<pre class="wp-block-preformatted">@[KEYWORD] (RULE);
```

包括：

- **@charset**  
    定义字符集。字符设置据说会被HTTP头覆盖。某些软件，例如Dreamweaver新建CSS文件时候，自动会带有下面所示代码，但实际开发时候，作用不大，因为meta中已经有所设置(`<meta charset="utf-8">`)，会覆盖，所以我都是直接删掉的。@charset “utf-8”;
- **@import**  
    导入其他CSS样式文件。实际上线时候，不建议使用，多请求，阻塞加载之类。但，本地开发可以使用，用做CSS模块化开发，然后使用一些(如grunt)工具进行压缩并合并。但是呢，相比less, sass等还是有不足，就是`@import`语句只能在CSS文件顶部，使得文件的前后关系控制，就不那么灵活。@import ‘global.css’;
- **@namespace**  
    此规则应用到XML HTML(XHTML)上特别有用，因为这样的话XHTML元素可以作为选择器在CSS中使用。/\* XHTML命名空间 \*/ @namespace url(http://www.w3.org/1999/xhtml); /\* 内嵌在XHTML的SVG的命名空间 \*/ @namespace svg url(http://www.w3.org/2000/svg);

### 嵌套规则

所谓“嵌套规则”，就是带有花括号`{}`, 语法类似下面的规则：

```
<pre class="wp-block-preformatted">@[KEYWORD] {
  /* 嵌套语句 */
}
```

包括：

- **@document**  
    CSS 4.0规范有相关说明。如果文档满足给定的一些条件，就可以应用我们指定的一些样式。比如说，这个CSS文件被子站A调用，和被子站C调用，我们可以通过域名匹配来执行不同的CSS样式。这样，我们可以有效避免冲突，或者防止外链之类。@document /\* 页面URL需要是 \*/ url(http://www.zhangxinxu.com/), /\* 页面URL的开头必须是… \*/ url-prefix(www.zhangxinxu.com/wordpress/), /\* 该域上的所有页面 \*/ domain(zhangxinxu.com), /\* 所有https协议页面 \*/ regexp(“https:.\*”) { /\* 开始样式 \*/ body { font-family: Comic Sans; } }由于这个AT规则是CSS4水平的，所以目前的浏览器支持情况很弱，只有FireFox浏览器支持，而且还需要加前缀。但是，10年之后，也就是2025年再来看，估计就是另外一番景象了。
- **@font-face**  
    这个大家可能比较熟，自定义字体用的。IE6也支持。目前相关文章也挺多，就不啰嗦，放个示意：@font-face { font-family: ‘MyWebFont’; src: url(‘myfont.woff2’) format(‘woff2’), url(‘myfont.woff’) format(‘woff’); }
- **@keyframes**  
    喜欢CSS3的同学这个也耳熟能详了，输入法打出来发现居然是“详”而不是“翔”，原来不是“听得太多耳朵都能拉出屎来”的意思哦，学习了！用来声明CSS3 animation动画关键帧用的，一笑而过：@keyframes fadeIn { 0% { opacity: 0; } 100% { opacity: 1; } }
- **@media**  
    媒介查询，解释非常常用的。响应式宽度啊，retina屏幕判断啦，打印屏幕啦，甚至IE7,IE8浏览器的hack啦，很多，本文标题是了解，不深入，给大家简单演示下使用就好了：@media all and (min-width: 1280px) { /\* 宽度大于1280干嘛干嘛嘞… \*/ }@media (-webkit-min-device-pixel-ratio: 1.5), (min-resolution: 2dppx) { /\* Retina屏幕干嘛干嘛嘞… \*/ }@media print { /\* 闪开闪开，我要打印啦！ \*/ }@media \\0screen\\,screen\\9 { /\* IE7,IE8干嘛干嘛嘞… \*/ }@media screen\\9 { /\* IE7干嘛干嘛嘞… \*/ }
- **@page**  
    这个规则用在打印文档时候修改一些CSS属性。使用`@page`我们只能改变部分CSS属性，例如间距属性`margin`, 打印相关的`orphans`, `widows`, 以及`page-break-*`, 其他CSS属性会被忽略。@page :first { margin: 1in; }上面CSS表示`:first`页面也要有`:left`, `:right`页面`margin`间距。zxx: //`@page`的伪类包括：`:blank`, `:first`, `:left`, `:right`。
- **@supports**  
    是否支持某CSS属性声明的AT规则，浏览器对齐支持性越来越好了，鄙人已经在实际项目中使用了这个规则，干嘛用呢？说来惭愧，当作hack使用了。具体细节不表。下面是一些使用示意：/\* 检查是否支持CSS声明 \*/ @supports (display: flex) { .module { display: flex; } } /\* 检查多个条件 \*/ @supports (display: flex) and (-webkit-appearance: checkbox) { .module { display: flex; } }

### 参考文章

- [The At-Rules of CSS](https://css-tricks.com/the-at-rules-of-css/)
- [MDN-At-rule](https://developer.mozilla.org/en-US/docs/Web/CSS/At-rule)