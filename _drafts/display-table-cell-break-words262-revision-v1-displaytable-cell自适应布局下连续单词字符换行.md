---
id: 263
title: 'display:table-cell自适应布局下连续单词字符换行'
date: '2019-08-16T19:15:58+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/262-revision-v1/'
permalink: '/?p=263'
---

套用HTML显示则为：

```
<pre class="wp-block-preformatted"><div class="fix">
    <div class="l"></div>
    <div class="cell"></div>
</div>
```

这几个类名对应CSS为：

```
<pre class="wp-block-preformatted">.fix{*zoom:1;}.fix:after{display:block; content:"clear"; height:0; clear:both; visibility:hidden;}
.l{float:left;}
.cell{display:table-cell; *display:inline-block; width:2000px; *width:auto;}
```

这种方式实现的自适应布局，元素宽度无需定值，且margin(浮动部分)与padding自由设置，支持百分比宽度（table-cell内），且可以无限制嵌套，兼容性不错，在我看来是最佳自适应布局方式。

人生不如意事八九，display:table-cell自适应布局虽然强大，但是，其有个比较烦人的克星，就是连续单词字符换行的问题。例如sbsbsbsbsbsbsbsbsbsb这样的连续字符。对于一般的元素，很好办的直接：

```
<pre class="wp-block-preformatted">word-wrap:break-word;
```

而`display:table-cell`声明的作用就是让元素以`td`标签的形式呈现，因此，对于连续单词字符，`display:table-cell`下的自适应布局就会中招（支持`display:table-cell`的IE8+以及其他现代浏览器下）。如何解决捏？

对于`pre`标签，辅助：

```
<pre class="wp-block-preformatted">white-space:pre-wrap;
```

但是，对于`td`类标签呢？

经过我的N多此时与实践，发现可行的方法为 → 对于含有连续单词字符的元素（不能是应用了`display:table-cell`的框架元素），附加大致如下的CSS代码：

```
<pre class="wp-block-preformatted">display:table; width:100%; table-layout:fixed; word-wrap:break-word;
```

其中`width`属性是必须的，但是`width`值大小您可以根据实际情况进行设置。个人认为`100%`基本上就可以受用于所有情况，无需另作修改。