---
id: 723
title: inline-block空格间距问题的解决
date: '2019-08-22T18:21:26+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/269-revision-v1/'
permalink: '/?p=723'
---

我一般用第5种方法。。。

### 一、现象描述

真正意义上的inline-block水平呈现的元素间，换行显示或空格分隔的情况下会有间距，很简单的个例子：

```
<pre class="wp-block-preformatted"><input /> <input type="submit" />
```

<figure class="wp-block-image">![表单控件之间的间距例子](http://image.zhangxinxu.com/image/blog/201204/2012-04-24_162919.png)</figure>间距就来了~~

我们使用CSS更改非inline-block水平元素为inline-block水平，也会有该问题：

```
<pre class="wp-block-preformatted">.space a {
    display: inline-block;
    padding: .5em 1em;
    background-color: #cad5eb;
}
```

```
<pre class="wp-block-preformatted"><div class="space">
    <a href="##">惆怅</a>
    <a href="##">淡定</a>
    <a href="##">热血</a>
</div>
```

<figure class="wp-block-image">![inline-block水平元素间的间距示意 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201204/2012-04-24_163352.png)</figure>不过，这类间距有时会对我们布局，或是兼容性处理产生影响，需要去掉它，该怎么办呢？

### 二、解决方法：使用font-size:0

类似下面的代码：

```
<pre class="wp-block-preformatted">.space {
    font-size: 0;
}
.space a {
    font-size: 12px;
}
```

这个方法，基本上可以解决大部分浏览器下inline-block元素之间的间距(IE7等浏览器有时候会有1像素的间距)。不过有个浏览器，就是Chrome, 其默认有最小字体大小限制，因为，考虑到兼容性，我们还需要添加：  
类似下面的代码：

```
<pre class="wp-block-preformatted">.space {
    font-size: 0;
    -webkit-text-size-adjust:none;
}
```

目前Chrome浏览器已经取消了最小字体限制。因此，上面的`-webkit-text-size-adjust:none;`代码估计时日不多了。

## 还有以下的方法，仅做了解，不实用，节约时间的话，不要继续往下看

### 1、方法之移除空格

元素间留白间距出现的原因就是标签段之间的空格，因此，去掉HTML中的空格，自然间距就木有了。考虑到代码可读性，显然连成一行的写法是不可取的，我们可以：

```
<pre class="wp-block-preformatted"><div class="space">
    <a href="##">
    惆怅</a><a href="##">
    淡定</a><a href="##">
    热血</a>
</div>
```

或者是：

```
<pre class="wp-block-preformatted"><div class="space">
    <a href="##">惆怅</a
    ><a href="##">淡定</a
    ><a href="##">热血</a>
</div>
```

或者是借助HTML注释：

```
<pre class="wp-block-preformatted"><div class="space">
    <a href="##">惆怅</a><!--
    --><a href="##">淡定</a><!--
    --><a href="##">热血</a>
</div>
```

等。

### 2、使用margin负值

```
<pre class="wp-block-preformatted">.space a {
    display: inline-block;
    margin-right: -3px;
}
```

<figure class="wp-block-image">![inline-block元素间间隔大小与字体和文字大小之前的关系表截图](http://image.zhangxinxu.com/image/blog/201204/2012-04-24_205406.png)</figure>margin负值的大小与上下文的字体和文字大小相关

例如，对于12像素大小的上下文，Arial字体的`margin`负值为`-3`像素，Tahoma和Verdana就是`-4`像素，而Geneva为`-6`像素。

由于外部环境的不确定性，以及最后一个元素多出的父margin值等问题，这个方法不适合大规模使用。

### 3、让闭合标签吃胶囊

如下处理：

```
<pre class="wp-block-preformatted"><div class="space">
    <a href="##">惆怅
    <a href="##">淡定
    <a href="##">热血</a>
</div>
```

注意，为了向下兼容IE6/IE7等喝蒙牛长大的浏览器，最后一个列表的标签的结束（闭合）标签不能丢。

在HTML5中，我们直接：

```
<pre class="wp-block-preformatted"><div class="space">
    <a href="##">惆怅
    <a href="##">淡定
    <a href="##">热血
</div>
```

好吧，虽然感觉上有点怪怪的，但是，这是OK的。

<figure class="wp-block-image">![无闭合标签与inline-block水平元素间距的去除 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201204/2012-04-24_211852.png)</figure>### 4、使用letter-spacing

类似下面的代码：

```
<pre class="wp-block-preformatted">.space {
    letter-spacing: -3px;
}
.space a {
    letter-spacing: 0;
}
```

根据我去年的测试，该方法可以搞定基本上所有浏览器，包括吃“东鞋”、“西毒(胶囊)”、“南地(沟油)”、“北钙(三鹿)”的IE6/IE7浏览器，不过Opera浏览器下有蛋疼的问题：最小间距1像素，然后，`letter-spacing`再小就还原了。

### 5、使用word-spacing

类似下面代码：

```
<pre class="wp-block-preformatted">.space {
    word-spacing: -6px;
}
.space a {
    word-spacing: 0;
}
```

一个是字符间距(`letter-spacing`)一个是单词间距(`word-spacing`)，大同小异。据我测试，`word-spacing`的负值只要大到一定程度，其兼容性上的差异就可以被忽略。因为，貌似，`word-spacing`即使负值很大，也不会发生重叠。

与上面demo一样的效果，这里就不截图展示了。如果您使用Chrome浏览器，可能看到的是间距依旧存在。确实是有该问题，原因我是不清楚，不过我知道，可以添加`display: table;`或`display:inline-table;`让Chrome浏览器也变得乖巧。

```
<pre class="wp-block-preformatted">.space {
    display: inline-table;
    word-spacing: -6px;
}
```

### 6、其他成品方法

下面展示的是[YUI 3 CSS Grids ](http://yuilibrary.com/yui/docs/cssgrids/)使用`letter-spacing`和`word-spacing`去除格栅单元见间隔方法（注意，其针对的是block水平的元素，因此对IE8-浏览器做了hack处理）：

```
<pre class="wp-block-preformatted">.yui3-g {
    letter-spacing: -0.31em; /* webkit */
    *letter-spacing: normal; /* IE < 8 重置 */
    word-spacing: -0.43em; /* IE < 8 && gecko */
}

.yui3-u {
    display: inline-block;
    zoom: 1; *display: inline; /* IE < 8: 伪造 inline-block */
    letter-spacing: normal;
    word-spacing: normal;
    vertical-align: top;
}
```

以下是一个名叫[RayM](http://raym31.home.comcast.net/)的人提供的方法：

```
<pre class="wp-block-preformatted">li {
    display:inline-block;
    background: orange;
    padding:10px;
    word-spacing:0;
    }
ul {
    width:100%;
    display:table;  /* 调教webkit*/
    word-spacing:-1em;
}

.nav li { *display:inline;}
```

也就是上面一系列CSS方法的组组合合。