---
id: 388
title: '自适应表格字符换行 单行溢出省略号table-layout: fixed;'
date: '2014-05-05T13:09:39+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=388'
permalink: /2014/05/table-overflow-ellipse-table-layout-fixed
categories:
    - 'HTML&amp;CSS'
---

我们应该都知道使用

```
<pre class="wp-block-preformatted">word-break: break-all;
```

让连续的英文数字字符换行显示；  
以及

```
<pre class="wp-block-preformatted">white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
```

让单行文字超出的时候使用点点点表示（Chrome目前有属性可以让多行文字点点点，且点的位置是在中间，见下2行示例代码）。

```
<pre class="wp-block-preformatted">display: -webkit-box; /* height: 36px; line-height: 18px; */ -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden;
```

但是，如果是自适应的表格中，我们要实现上面两个效果，可能就会遇到挫折，你会发现屡试不爽的方法现在完全被无视了！

如果解决这个问题呢？实际上很简单，给最外面的`table`标签增加一个声明：

```
<pre class="wp-block-preformatted">table-layout: fixed;
```

`table-layout: fixed`的作用在于，让表格布局固定，也就是表格的宽度不是跟随单元格内容多少自动计算尺寸。

兼容IE6+

以后，大家或多或少，或者已经开始使用表格相关`display`属性帮助构建web页面了，总会遇到类似的连续英文字符不换行，或者单行文字溢出没有效果的。此时，您就可以想到`table-layout: fixed`这厮。您可以试试：

```
<pre class="wp-block-preformatted">display:table; width:100%; table-layout:fixed;
```