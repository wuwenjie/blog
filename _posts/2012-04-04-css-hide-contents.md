---
id: 267
title: CSS隐藏内容的几种做法
date: '2012-04-04T19:30:58+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=267'
permalink: /2012/04/css-hide-contents
categories:
    - 'HTML&amp;CSS'
---

### 一、CSS元素隐藏

在CSS中，让元素隐藏（指屏幕范围内肉眼不可见）的方法很多，有的占据空间，有的不占据空间；有的可以响应点击，有的不能响应点击。后宫选秀——一个一个看。

```
<pre class="wp-block-preformatted">{ display: none; /* 不占据空间，无法点击 */ }
```

```
<pre class="wp-block-preformatted">{ visibility: hidden; /* 占据空间，无法点击 */ }
```

```
<pre class="wp-block-preformatted">{ position: absolute; clip:rect(1px 1px 1px 1px); /* 不占据空间，无法点击 */ }
```

```
<pre class="wp-block-preformatted">{ position: absolute; top: -999em; /* 不占据空间，无法点击 */ }
```

```
<pre class="wp-block-preformatted">{ position: relative; top: -999em; /* 占据空间，无法点击 */ }
```

```
<pre class="wp-block-preformatted">{ position: absolute; visibility: hidden; /* 不占据空间，无法点击 */ }
```

```
<pre class="wp-block-preformatted">{ height: 0; overflow: hidden; /* 不占据空间，无法点击 */ }
```

```
<pre class="wp-block-preformatted">{ opacity: 0; filter:Alpha(opacity=0); /* 占据空间，可以点击 */ }
```

```
<pre class="wp-block-preformatted">{ position: absolute; opacity: 0; filter:Alpha(opacity=0); /* 不占据空间，可以点击 */ }
```

```
<pre class="wp-block-preformatted">{ 
    zoom: 0.001;
    -moz-transform: scale(0);
    -webkit-transform: scale(0);
    -o-transform: scale(0);
    transform: scale(0);
    /* IE6/IE7/IE9不占据空间，IE8/FireFox/Chrome/Opera占据空间。都无法点击 */
}
```

```
<pre class="wp-block-preformatted">{
    position: absolute;
    zoom: 0.001;
    -moz-transform: scale(0);
    -webkit-transform: scale(0);
    -o-transform: scale(0);
    transform: scale(0); 
    /* 不占据空间，无法点击 */
}
```

### 二、display:none和visibility:hidden的区别

1. 空间占据 visibility:hidden占据空间
2. 回流与渲染
3. 株连性

`display:none`隐藏产生reflow和repaint(回流与重绘)，而`visibility:hidden`没有这个影响前端性能的问题；第三点估计是不少同行不知道的，就是“株连性”方面的差异。

株连性： 一旦父节点元素应用了`display:none`，父节点及其子孙节点元素全部不可见，而且无论其子孙元素如何不屈地挣扎都无济于事。

而我们给一个父元素应用`visibility:hidden` 如果子孙元素应用了`visibility:visible`，那么这个子孙元素又会显现出来。

### 三、height:0和overflow:hidden的组合

`overflow:hidden`用中文理解就是“溢出隐藏”，也就是盒子以外的内容都咔嚓掉不可见的。加上`height:0`，只要是一般的非inline水平元素，则元素内部所有子孙都应该是不可见的

`height:0`和`overflow:hidden`组合隐藏“失效”的条件如下：祖先元素没有`position:relative/absolute/fixed`声明，同时内部子元素应用了`position:absolute/fixed`声明。

`overflow`可以剪裁超出块状元素之外的元素。除非超出元素的包含块是整个视区或是该overflow元素的祖先元素。然后绝对定位元素的包含块应该就是含有`position:relative/absolute/fixed`的祖先元素。

因此：

```
<pre class="wp-block-preformatted">body
    height: 0; overflow: hidden;
        position: absolute; /* 不会被隐藏 */
```

```
<pre class="wp-block-preformatted">position: relative;
    height: 0; overflow: hidden;
        position: absolute; /* 不会被隐藏 */
```

```
<pre class="wp-block-preformatted">height: 0; overflow: hidden;  position: relative;
    position: absolute; /* 会被隐藏 */
```

```
<pre class="wp-block-preformatted">height: 0; overflow: hidden;
    position: relative;
        position: absolute; /* 会被隐藏 */
```

记住这个：`position: absolute`元素溢出`overflow: hidden`元素的时候，如果其第一个含有`position`属性(`static`除外)的祖先元素（一直到`body`）是`overflow: hidden`元素祖先元素的时候，则不隐藏；否则，隐藏。