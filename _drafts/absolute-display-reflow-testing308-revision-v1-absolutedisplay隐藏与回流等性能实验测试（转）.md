---
id: 309
title: absolute/display隐藏与回流等性能实验测试（转）
date: '2019-08-16T22:15:42+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/308-revision-v1/'
permalink: '/?p=309'
---

**1. 关于demo**  
Opera是个性能相当不错的浏览器，要想制作一个可以显示出时间的demo可不是件容易的事情，简单的几个div层的reflow时间必然是0ms, 到底是有回流呢还是没有呢？回流的时间与dom的深度有关，因此，本文的所有demo都是内部为御用图片的**498层**<sup>②</sup>嵌套div。因此，**友情提醒：千万不要使用firebug等工具去查看HTML代码**，浏览器崩掉none of my business！

//zxx: ② 貌似Opera中有500层标签最大嵌套的显示，498个div层+1个p标签层+1个button标签=500层；层级再高就会显示异常。FireFox浏览器最大层级显示似乎小得多，Chrome浏览器貌似没有问题，我尝试了800层嵌套也是瞬间显示无压力。

<figure class="wp-block-image">![面板上的profiler的工具项 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201301/2013-01-17_145724.png)</figure>**2. 测试follow me**  
在较新版本的Opera浏览器上打开上面demo页面（没有的直接看截图以及数据哈~），右键页面→检查元素，打开工具面板，切换到下图所示这一项（会禁用其他所有项，为了更准确结果）：

<figure class="wp-block-image">![开始profiler按钮](http://image.zhangxinxu.com/image/blog/201301/2013-01-17_151106.png)</figure>然后点击左上方那个红色圆点按钮，开始记录JS线程、CSS样式、回流、布局、渲染等。

<figure class="wp-block-image">![测试触发按钮](http://image.zhangxinxu.com/image/blog/201301/2013-01-17_151746.png)</figure><figure class="wp-block-image">![停止记录按钮](http://image.zhangxinxu.com/image/blog/201301/2013-01-17_151820.png)</figure>点击demo页面中间区域的按钮（或者tab键focus按钮回车触发），触发图片的position状态变化，大约10次连续触发后<sup>③</sup>，再次点击左上方那个红色圆点按钮，停止记录。

//zxx:③ 回流等数据的产生是有波动的，一次状态的改变并不能准确反映是否真的产生了变化。

<figure class="wp-block-image">![记录的一次结果](http://image.zhangxinxu.com/image/blog/201301/2013-01-17_153807.png)</figure>下图为一次点击按钮触发的普通记录<sup>④</sup>：

//zxx:④ 上截图第一个回流时间要长一点，是因为为了防止二次点击，按钮disabled造成了冗余回流，回流16m时长偏大。现在已去除了这个干扰项。但不影响终结论。

从中我们可以**基本**可以得到一些结论：

1. 元素`position`在`absolute`和`static`之间切换的时候，都会产生回流；
2. 鼠标经过按钮的内发光等效果产生的渲染远比图片乾坤大挪移来的大；
3. 布局的时间点在渲染稍前一点，也就是说，先布局再渲染。

以上结论只能算作假设，因为缺少对照实验组。根据reflow确定的结论，元素`visibility:hidden`时候不会产生回流，因此，我又制作了一个对比demo，其中图片切换状态是`visibility`为`hidden/visibile`.

### 三、visibility对比试验

<figure class="wp-block-image">![visibility与回流测试结果截图 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201301/2013-01-17_163152.png)</figure>结果如下（回车测试）：

<figure class="wp-block-image">![position与回流测试结果 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201301/2013-01-17_163500.png)</figure>对比同等操作下的`position`结果：

可以发现，元素`position`属性下文档流脱离与否的变化是会产生回流的；visibility不会！而且，visibility隐藏无需重新布局，渲染时间也短。不过有点不解的是CSS样式时间长了很多（红色下划线标示）？求解！

### 四、元素隐藏方法与回流、布局、渲染时间

元素隐藏不可见有很多方法，如下3个方法是我比较常用的：

1. `position:absolute; top:-999em; `屏幕阅读器可识别。
2. `position:absolute; visibility:hidden;` 屏幕阅读器不可识别。
3. `display:none; `屏幕阅读器不可识别。

每个方法测试10次，并算出平均时间值。具体数据参见下表（欢迎质疑）：

|  | top:-999em方法 | visibility:hidden方法 | display:none方法 |
|---|---|---|---|
| 回流(ms) | 布局(ms) | 渲染(ms) | 回流(ms) | 布局(ms) | 渲染(ms) | 回流(ms) | 布局(ms) | 渲染(ms) |
| 第1次 | 0 | 34 | 69 | 0 | 28 | 78 | 1 | 28 | 73 |
| 第2次 | 2 | 30 | 81 | 1 | 26 | 72 | 1 | 31 | 81 |
| 第3次 | 1 | 37 | 82 | 0 | 30 | 80 | 0 | 33 | 77 |
| 第4次 | 2 | 23 | 72 | 1 | 28 | 72 | 1 | 36 | 82 |
| 第5次 | 1 | 37 | 71 | 0 | 30 | 73 | 0 | 31 | 80 |
| 第6次 | 1 | 26 | 76 | 0 | 31 | 78 | 1 | 25 | 67 |
| 第7次 | 0 | 29 | 72 | 0 | 30 | 74 | 0 | 27 | 74 |
| 第8次 | 1 | 28 | 72 | 1 | 29 | 74 | 0 | 36 | 90 |
| 第9次 | 2 | 31 | 78 | 0 | 31 | 72 | 0 | 33 | 79 |
| 第10次 | 0 | 32 | 65 | 1 | 27 | 75 | 1 | 26 | 78 |
| 平均 | 1.0 | 30.7 | 73.8 | 0.4 | 29.0 | 74.8 | 0.5 | 30.6 | 78.1 |

布局和渲染应该是一致的（都是元素区域不保留的隐藏方式）。至于渲染，貌似负值屏幕外absolute的方法要高一倍。

然后，0~2这种范围的数据是没有参考意义的，因为数值太小，这种小范围的波动是很正常的，除非测试数据有100+而不是只有10次。

然而，有个不解的疑问，元素纯粹absolute的时候会产生更强的回流<sup>⑤</sup>，平均每次回流都有6ms，这个很狗血的结果让我茫然了！

//zxx: ⑤ position:absolute;与position:absolute; left:0; 虽然布局位置一模一样，但是尼玛前者回流6ms，后者平均1ms, 着实诡异。

对比上面各种元素隐藏方法的数据，可以得出，在Opera浏览器下，随便哪种隐藏，都没有性能上的显著差异的。更多的可能要考虑到可访问性等因素了！

对于回流性能低下明显的IE浏览器，不知反应如何，估计reflow时间不是个位数，其实，更好测试的，不知有没有什么相关测试工具，望指明！