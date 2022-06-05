---
id: 409
title: 'CSS counter-increment计数器自动递增'
date: '2019-08-17T13:55:38+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/408-revision-v1/'
permalink: '/?p=409'
---

### 一、CSS计数器三角关系

CSS计数器只能跟`content`属性在一起的时候才有作用，而`content`属性貌似专门用在`before`/`after`伪元素上的。于是，就有了，“计数器↔伪元素↔content属性”的铁三角关系。

### 二、CSS计数器成员

CSS计数器的2个属性和1个方法，依次是：  
**1. counter-reset**  
**2. counter-increment**  
**3. counter()/counters()**

依次说来。  
**1. counter-reset**  
顾名思意，就是“计数器-重置”的意思。其实就是“班级命名”，主要作用就是给计数器起个名字。如果可能，顺便告诉下从哪个数字开始计数。默认是`0`, 注意，默认是`0`而不是`1`. 可能有同学回疑惑，尼玛网上的各种例子默认显示的第1个数字不都是`1`吗？那是因为受了`counter-increment`普照的影响，后面会详细讲解。

OK, 这里，我们先看两个简单的`counter-reset`的例子：

```
<pre class="wp-block-preformatted">.xxx { counter-reset: small-apple; } /* 计数器名称是'small-apple' */
```

```
<pre class="wp-block-preformatted">.xxx { counter-reset: small-apple 2; } /* 计数器名称是'small-apple', 并且默认起始值是2 */
```

<figure class="wp-block-image">![counter-reset的起始值](https://image.zhangxinxu.com/image/blog/201408/2014-08-25_154944.png)</figure>`counter-reset`的计数重置可以是负数，例如`-2`。也可以写成小数，例如`2.99`，不过就是IE和FireFox都不识别，认为是不合法数值，直接无视，当作默认值`0`来处理；不过Chrome不嫌贫嫉富，任何小数都是向下取整，如`2.99`当成`2`处理，于是王小二还是那个王小二。

到此为止？非也非也！`counter-reset`还有一手，就是多个计数器同时命名。例如，王小二和王小三同时登台：

```
<pre class="wp-block-preformatted">.xxx { counter-reset: wangxiaoer 2 wangxiaosan 3; }
```

直接空格分隔，譬如逗号什么的都不行。

<figure class="wp-block-image">![王小二和王小三](https://image.zhangxinxu.com/image/blog/201408/2014-08-25_155311.png)</figure>另外，`counter-reset`还可以设置为`none`和`inherit`. 干掉重置以及继承重置。你懂的，就不展开了。

**2. counter-increment**  
顾名思意，就是“计数器-递增”的意思。值为`counter-reset`的1个或多个关键字。后面可以跟随数字，表示每次计数的变化值。如果缺省，则使用默认变化值`1`（方便起见，下面的都使用默认值做说明）。

CSS的计数器的计数是有一套规则的，我将之形象地称为“普照规则”。具体来讲就是：**普照源(counter-reset)唯一，每普照(counter-increment)1次，普照源增加1次计数值。**

于是，我们可以解释上面提到的“默认值是`0`”的问题。通常CSS计数器应用的时候，我们都会使用`counter-increment`, 肯定要用这个，否则怎么递增呢！而且一般都是`1`次普照，正好`+1`，第一个计数的值就是`1`啦(0+1=1)！

下面，通过几个例子，给大家形象地展示下**普照规则**。

<figure class="wp-block-image">![计数起始值为counter-reset值+1](https://image.zhangxinxu.com/image/blog/201408/2014-08-25_145152.png)</figure>demo中，王小二的`counter-reset`值是`wangxiaoer 2`，但是，显示的计数不是小`2`而是小`3`，王小二变成了王小三！

Demo相关核心代码为：

```
<pre class="wp-block-preformatted">.counter { counter-reset: wangxiaoer 2; counter-increment: wangxiaoer; }
.counter:before { content: counter(wangxiaoer); }
```

```
<pre class="wp-block-preformatted"><p class="counter"></p>
```

这里`counter-increment`普照了`p`标签，`counter-reset`值增加，默认递增`1`，于是计数从设置的初始值`2`变成了`3`，`wangxiaoer`就是这里的计数器，自然伪元素content值`counter(wangxiaoer)`就是`3`.

**②** 当然，也可以普照自身，也就是`counter-increment`直接设置在伪元素上：

```
<pre class="wp-block-preformatted">.counter { counter-reset: wangxiaoer 2; }
.counter:before { content: counter(wangxiaoer); counter-increment: wangxiaoer; }
```

```
<pre class="wp-block-preformatted"><p class="counter"></p>
```

依然是1次普照，依旧全局的计数器`+1`，所以，显示的数值还是`3`(demo略).

**③** 趁热打铁。如果父元素和子元素都被`counter-increment`普照1遍，结果会如何呢？

很简单的，父元素1次普照，子元素1次普照，共两次普照，`counter-reset`设置的计数器值增加2次，计数起始值是2，于是现实的数字就是`4`啦！

<figure class="wp-block-image">![连续普照数值直接增加](https://image.zhangxinxu.com/image/blog/201408/2014-08-25_182835.png)</figure>Demo相关核心代码为：

```
<pre class="wp-block-preformatted">.counter { counter-reset: wangxiaoer 2; counter-increment: wangxiaoer; }
.counter:before { content: counter(wangxiaoer); counter-increment: wangxiaoer; }
```

```
<pre class="wp-block-preformatted"><p class="counter"></p>    // 显示的是4
```

总而言之，无论位置在何方，只要有`counter-increment`，对应的计数器的值就会变化，`counter()`只是输出而已！

**④** 理解了“普照规则”，则以我们通常的计数器递增效果也可以理解了。

考虑下面这两个问题：

1. 爸爸受到普照，且重置默认值`0`，爸爸有2个孩子。孩子自身都没有普照。两个孩子的计数值是？
2. 爸爸没有普照，重置默认值`0`，爸爸有2个孩子。孩子自身都接受普照。两个孩子的计数值是？

答案是：**1,1和1,2**!

哦？答案居然不一样，有什么差别呢？

很简单。什么爸爸，孩子你都不要关心。只要看被普照了几次。情况1就爸爸被普照，因此，计数器增加1次，此时两个孩子的`counter`自然都是`1`; 情况2，两个孩子被普照，普照2次，第1个孩子普照之时，计数器+1，也就是1；第2个孩子普照之时再+1，于是就是2. 于是，两个孩子的`counter`输出就是`1,2`.

<figure class="wp-block-image">![兄弟递增规则demo截图](https://image.zhangxinxu.com/image/blog/201408/2014-08-25_202003.png)</figure>上demo对应上面的第2个问题，其结果截图如下：

核心代码如下：

```
<pre class="wp-block-preformatted">.counter { counter-reset: wangxiaoer 2; }
.counter:before,
.counter:after { content: counter(wangxiaoer); counter-increment: wangxiaoer; }
```

```
<pre class="wp-block-preformatted"><p class="counter"></p>
```

计数器的数值变化遵循HTML渲染顺序，遇到一个`increment`计数器就变化，什么时候`counter`输出就输出此时的计数值。看懂了下图，您自然就会全然明白“普照规则”了。

<figure class="wp-block-image">![计数器深入理解](https://image.zhangxinxu.com/image/blog/201408/2014-08-25_233459.png)</figure>**counter-increment其他设定**  
**①** `counter-reset`可以一次命名两个计数器名称，`counter-increment`自然有与之呼应的设定，也是名称留空就可以了。

<figure class="wp-block-image">![2个counter-reset值同时](https://image.zhangxinxu.com/image/blog/201408/2014-08-25_151352.png)</figure>**②** 正如本节开始提到的，这变化的值不一定是`1`，我们可以灵活设置。例如：

```
<pre class="wp-block-preformatted">counter-increment: counter 2
```

<figure class="wp-block-image">![递增值大小变化变身](https://image.zhangxinxu.com/image/blog/201408/2014-08-25_234835.png)</figure>那就是偶数偶数的增加。例如下面这个变身：

还可以是负数，例如：

```
<pre class="wp-block-preformatted">counter-increment: counter -1
```

就有了递减排序效果啦！

**③** 值还可以是`none`或者`inherit`.

<figure class="wp-block-image">![counter/counters语法](https://image.zhangxinxu.com/image/blog/201408/2014-08-26_102609.png)</figure>**3. counter()/counters()**  
这是个方法，不是属性。类似CSS3中才`calc()`计算。这里作用很单纯显示计数。不过名称、用法有多个：

**①** 目前为止，我们看到的是最简单的用法：

```
<pre class="wp-block-preformatted">counter(name) /* name就是counter-reset的名称 */
```

**②** 那下面这个语法是什么意思呢？

```
<pre class="wp-block-preformatted">counter(name, style)
```

这里的`style`参数还有有些名堂的。其支持的关键字值就是`list-style-type`支持的那些值。作用是，我们递增递减可以不一定是数字，还可以是英文字母，或者罗马文等。

> **list-style-type**：disc | circle | square | decimal | lower-roman | upper-roman | lower-alpha | upper-alpha | none | armenian | cjk-ideographic | georgian | lower-greek | hebrew | hiragana | hiragana-iroha | katakana | katakana-iroha | lower-latin | upper-latin

<figure class="wp-block-image">![counter style属性截图](https://image.zhangxinxu.com/image/blog/201408/2014-08-26_105632.png)</figure>结果见下截图：

核心CSS代码为：

```
<pre class="wp-block-preformatted">content: counter(wangxiaoer, lower-roman); /* 以小写罗马数字格式表示当前计数器wangxiaoer的值 */
```

**③** counter还支持级联。也就是一个`content`属性值可以有多个`counter()`方法。

<figure class="wp-block-image">![counter级联并存demo截图](https://image.zhangxinxu.com/image/blog/201408/2014-08-26_111516.png)</figure>核心CSS/HTML如下：

```
<pre class="wp-block-preformatted">.counter { counter-reset: wangxiaoer 2 wangxiaosan 3; }
.counter:before { content: counter(wangxiaoer) '\A' counter(wangxiaosan); white-space: pre; }
```

```
<pre class="wp-block-preformatted"><p class="counter"></p>
```

上面CSS源代码使用`'\A'`使用`inline`水平元素换行

**④** 下面介绍下`counters()`方法。看似值多了个字母`s`, 但表意大变身。`counters`几乎可以说是**嵌套计数**的代名词。

我们平时的序号，不可能就只是`1,2,3,4,..`, 还会有诸如 `1.1,1.2,1.3,...`等的子序号。得，前者就是`counter()`干的事情，后者就是`counters()`干的事情。

基本用法为：

```
<pre class="wp-block-preformatted">counters(name, string); /* MDN上说，要想IE8兼容，这里逗号后面的空格要去掉，但是鄙人IE11的IE8模式看，无此问题 */
```

其中，`string`参数为字符串（需要引号包围的）（必须参数），表示子序号的连接字符串。例如`1.1`的`string`就是`'.'`, `1-1`就是`'-'`.

看上去很简单。但是，如果理解不是很深刻，日后在使用肯定会遇到麻烦——“咦？怎么没有子序列，明明语法正确的啊？”首先，记住这一句话，“普照源是唯一的”，所以，如果你在**只在**`body`标签上设置`counter-reset`，就算里面的子元素嵌套了祖宗十八代，还是不会有任何嵌套序号出现的！所以，要想实现嵌套，必须让每一个列表容器拥有一个“普照源”，通过子辈对父辈的`counter-reset`重置、配合`counters()`方法才能实现计数嵌套效果。

<figure class="wp-block-image">![计数器嵌套](https://image.zhangxinxu.com/image/blog/201408/2014-08-26_145749.png)</figure>也会遇到这样的麻烦——“咦，怎么子序列不按层级顺序来啊，命名语法正确啊？” 还是要记住这一句话：“一个容器里的普照源(reset)是唯一的”，所以，如果你不小心把计数显示和技术reset元素以兄弟元素形式放在一起（虽然HTML内容布局呈现是没有异常的），就很可能会出现计数序号乱入的情况。

会看到标红的部分的序号显示异常了！

<figure class="wp-block-image">![计数器的值乱入的截图示意](https://image.zhangxinxu.com/image/blog/201408/2014-08-26_150441.png)</figure>为何会出现这个问题，我们看下HTML（主要是注释）：

```
<pre class="wp-block-preformatted"><div class="reset">
    <div class="counter">我是王小二</div>
    <div class="reset"><-- 这里的reset与上面的counter是兄弟关系，而不是父子关系。虽然布局渲染上没有差异。但是，一个容器的reset的唯一的，一旦子元素出现reset，会改变整个容器的嵌套关系，于是，后面的“王小三”、“王小四”其实已经进入了2级嵌套，因此显示的是1-3和1-4 -->
        ...
    </div>
    <div class="counter">我是王小三</div>
    <div class="counter">我是王小四</div>
    <div class="reset">
        <div class="counter">我是王小四的大儿子</div>
    </div>
</div>
```

如果上面的注释没看明白，您可以跟前面没有问题的demo做下HTML结构对比，或许就会豁然开朗！

**⑤** `counters()`也是支持`style`自定义递增形式的。

```
<pre class="wp-block-preformatted">counters(name, string, style)
```

与`counter()`的`style`参数使用一致，不赘述。

### 四、CSS计数器与display:none挖挖挖

一个元素，如果设置了`counter-increment`, 但是其`display`的属性值是`none`或者含有`hidden`属性（针对支持浏览器），则此计数值是不会增加的。而`visibility:hidden`以及其他声明不会有此现象。

### 五、CSS计数器实际应用挖挖挖

相比传统的`ol`,`ul`列表计数，CSS计数器的优势就在于灵活与强大，不足就是IE6/IE7不支持。

普照规则第一条，普照源唯一。所以，我们可以在头尾放两个差距甚远的列表，然后，这些列表自动显示序号。而`ol/ul`只能写死`start`实现，很不灵活，一旦列表有删减，就嗝屁了。

由于计数器是伪元素控制显示的。因此，我们几乎可以应用各种CSS样式，各种定位等。所以，基本上，只要有有序序号呈现的地方，就能使用CSS计数器。

<figure class="wp-block-image">![首页广告位的序号](https://image.zhangxinxu.com/image/blog/201408/2014-08-26_153731.png)</figure>例如，电商首页的图片slide广告上的`1,2,3,4,...`序号；