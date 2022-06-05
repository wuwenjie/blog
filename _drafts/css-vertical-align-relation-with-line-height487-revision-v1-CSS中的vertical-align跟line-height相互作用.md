---
id: 488
title: CSS中的vertical-align跟line-height相互作用
date: '2019-08-17T19:22:45+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/487-revision-v1/'
permalink: '/?p=488'
---

众所周知，`vertical-align`支持很多属性值，足足可以组成一个足球队了：

```
<pre class="wp-block-preformatted">/* 关键字值 */
vertical-align: baseline;
vertical-align: sub;
vertical-align: super;
vertical-align: text-top;
vertical-align: text-bottom;
vertical-align: middle;
vertical-align: top;
vertical-align: bottom;

/* <长度> 值 */
vertical-align: 10em;
vertical-align: 4px;

/* <百分比> 值 */
vertical-align: 10%;

/* 全局值 */
vertical-align: inherit;
vertical-align: initial;
vertical-align: unset;
```

其中，有个属性值暴露了`vertical-align`和`line-height`之间的基友关系，大家猜猜看是哪个属性值？

<figure class="wp-block-image">![想不明白](https://image.zhangxinxu.com/image/blog/201508/whats-s.png)</figure>哇塞，好厉害！居然被大家一眼就看出来了，没错，就是“百分比值”。

`vertical-align`的百分比值不是相对于字体大小或者其他什么属性计算的，而是相对于`line-height`计算的。举个简单的例子，如下CSS代码：

```
<pre class="wp-block-preformatted">{
  line-height: 30px;
  vertical-align: -10%;
}
```

实际上，等同于：

```
<pre class="wp-block-preformatted">{
  line-height: 30px;
  vertical-align: -3px;    /* = 30px * -10% */  
}
```

CSS属性何其多，偏偏跟`line-height`有一腿，这不是有基情那是什么？

//zxx: IE6/IE7浏览器下的vertical-align的百分比值不支持小数line-height

### 三、背地里无处不在的基友关系

//zxx: **注意**，`vertical-align`和`line-height`的地下基友关系从HTML5文档声明开始的，因此，以下探讨的现象，都是在页面为HTML5声明前提下，类似下面的doctype:

```
<pre class="wp-block-preformatted"><!doctype html>
<html>
```

另外，下面很多效果直接就是真实演示，因此，请使用现代浏览器观摩下面的内容。如果发现某些行为与描述不匹配，且浏览器正常，那可能是因为你访问的并不是[原出处](http://www.zhangxinxu.com/wordpress/?p=4925)。

**① 基本现象**

要八卦`vertical-align`和`line-height`之间的关系，我们不妨从一个极其简单的现象入手。假设，我们有一个`<div>`标签，然后，里面有一张`<img>`图片，我们的HTML代码就是这样子：

```
<pre class="wp-block-preformatted"><div><img src="mm1.jpg"></div>
```

然后，表现就是一张图片呈现，类似下面这样：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>恩，看上去很正常，一切都是理所当然。然而，如果我们给这个`<div>`元素增加一个背景色，例如淡蓝色：

```
<pre class="wp-block-preformatted"><div style="background-color:#e5edff;"><img src="mm1.jpg"></div>
```

则会是下面这样：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure><figure class="wp-block-image">![图片与空白](https://image.zhangxinxu.com/image/blog/201508/2015-08-29_085105.png)</figure>会发现图片下面有一段空白空间：

<figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/32.gif)</figure>想必大家都遇到过类似问题，不知大家有没有思考过，为什么图片下面有留有一段间隙呢？

实际上，这段空白间隙就是`vertical-align`和`line-height`携手搞的鬼！

首先，大家一定要意识到这么一点：**对于内联元素，vertical-align与line-height虽然看不见，但实际上「到处都是」！**

<figure class="wp-block-image">![到处都是](https://image.zhangxinxu.com/image/blog/201508/anywhere.jpg)</figure><figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/14.gif)</figure>因此，对于内联元素各种想得通或者想不通的行为表现，基本上都可以用`vertical-align`和`line-height`来解释，以及进行行为矫正，然而，要深入理解这些行为表现，还是需要狠花一番功夫的，因此，下面的内容，请确保你有半小时充足时间细细阅读，别的地方可是看不到的。

**② 幽灵空白节点**

**「幽灵空白节点」**这个概念我自己命名的，注意，是我个人YY出来的，是我自己便于理解某些行为特征提出的概念。规范可能有类似的概念，但名称并非这个。 W3C规范虽然有很多行为的解释和说明，但是，毕竟官方的东西，要求严谨正式，但是，也会有太干太涩的感觉。如果快速掌握和理解这些行为表现呢？就我个人而言，从两方面入手：**1.情感化认知**；**2. 具象化思维**。

例如，我称`vertical-align`和`line-height`为好基友（包括以前称浮动和绝对定位是兄弟），就是“情感化认知”；而这里的「幽灵空白节点」就是“具象化思维”。

那「幽灵空白节点」是个什么意思呢？

**在HTML5文档声明下，块状元素内部的内联元素的行为表现，就好像块状元素内部还有一个（更有可能两个-前后）看不见摸不着没有宽度没有实体的空白节点，这个假想又似乎存在的空白节点，我称之为“幽灵空白节点”。** //zxx: 自己捣腾的概念，不是权威，欢迎其他小伙伴反馈权威解释

抽象了这个概念，绝对定位与`text-align`的一些行为表现，以及这里的行为表现，就好理解了。

还是上面的图片下边缘留空隙的例子，实际上，这种行为表现，就跟图片前面或者后面有一个宽度为0的空格元素表现是一致的。但是，空格是透明的，为了便于大家理解，我就直接使用很明显的匿名inline box, 也就是字符代替。如下，大家会发现，图片下面的间隙，依旧是那个间隙。

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>zxx

下面要解释这个间隙就好解释了。下面，我们让新增的文本inline-block化，然后弄个白色背景，显示其占据的高度。

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>zxx

会发现，图片下面的间隙，依旧是那个间隙。但是，我们的理解就好理解了。回答下面几个问题，我们就知道表现的原因了：

1. `vertical-align`默认的对齐方式是？
2. 后面zxx文字的高度从何而来？

上面2个问题就很简单了：

1. `vertical-align`默认值是`baseline`, 也就是基线对齐。而基线是什么，基线就是字母X的下边缘。所以，妹子图片的下边缘就和后面zxx中的字母`x`下边缘对齐（见下图）。而字符`zxx`本身是有高度的，对吧，于是，图片下面就留空了。
2. 而`zxx`文字的高度是由行高决定的。

因此，简单的图片下面留白行为表现，本质上，就是`vertical-align`和`line-height`背地里搞基造成的。

知道了问题的原因，我们就可以对症下药，准确搞定图片下面我们不希望看到的间隙。怎么搞呢？一对基友，`vertical-align`和`line-height`我们随便搞定一个就可以了。

比方说`vertical-align`.

**1. 让vertical-align失效**  
图片默认是`inline`水平的，而`vertical-align`对块状水平的元素无感。因此，我们只要让图片`display`水平为`block`就可以了，我们可以直接设置`display`或者浮动、绝对定位等（如果布局允许）。例如：

```
<pre class="wp-block-preformatted">img { display: block; }
```

则妹子就会变这样：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>下面的空隙不见了。

**2. 使用其他vertical-align值**  
告别`baseline`, 取用其他属性值，比方说`bottom`/`middle`/`top`都是可以的。

vertical-align:bottom vertical-align:middle vertical-align:top

<figure class="wp-block-image">![](http://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>zxx

**3. 直接修改line-height值**  
下面的空隙高度，实际上是文字计算后的行高值和字母x下边缘的距离。因此，只要行高足够小，实际文字占据的高度的底部就会在x的上面，下面没有了高度区域支撑，自然，图片就会有容器底边贴合在一起了。比方说，我们设置行高5像素：

```
<pre class="wp-block-preformatted">div { line-height: 5px; }
```

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>zxx

**4. line-height为相对单位，font-size间接控制**  
如果`line-height`是相对单位，例如`line-height:1.6`或者`line-height:160%`之类，也可以使用`font-size`间接控制，比方说来个狠的，`font-size`设为大鸡蛋`0`, 本质上还是改变`line-height`值.

```
<pre class="wp-block-preformatted">div { font-size: 0; }
```

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>zxx

**③ 基本现象衍生：垂直居中**

由于「幽灵空白节点」的存在，因此，我们可以进一步衍生，实现其他更实用的效果，比方说任意尺寸的图片（或者内联块状化的多行文字）的垂直居中效果。就是借助本文的两位男主角，`vertical-align`和`line-height`。

你想啊，图片后面（前面）有个类似空格字符的节点，然后就能响应`line-height`形成高度，此时，图片再来个`vertical-align:middle`，当当当当，就可以和这个被行高撑高的「幽灵空白节点」(近似)垂直对齐了。

例如：

```
<pre class="wp-block-preformatted">div { line-height: 240px; }
img { vertical-align: middle; }
```

然后就会这样子：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>不过上面的效果并不是完全的垂直居中，只是近似（稍微仔细看可以看出来）。为什么只是近似呢？那是因为「幽灵空白节点」高度行高撑开，其垂直中心是字符content area的中心，而对于字符`x`而言，都是比绝对中心位置要下沉的（不同字体下沉幅度不一样），换句更易懂的描述就是`x`的中心位置都是在字符内容区域高度中心点的下方，而这上下的偏差就是这里图片上下间距的偏差。

<figure class="wp-block-image">![中线位置并不是字符内容的绝对居中位置](https://image.zhangxinxu.com/image/blog/201508/2015-08-31_130708.png)</figure>我特意把字符`x`使用大字号演示了下：

换句更简单的话说就是：middle中线位置(字符`x`的中心)并不是字符内容的绝对居中位置。两个位置的偏差就是图片近似居中的偏差。

<figure class="wp-block-image">![近似垂直居中演示](https://image.zhangxinxu.com/image/blog/201508/2015-08-29_214832-new.png)</figure>嘛嘛，单纯的文字还是太苍白了，截个图示意下吧：

因此，要想完全垂直居中，最先想到的方法就是让后面的“幽灵字符”也是`vertical-align:middle`，然而，呵呵，既然称之为“幽灵”就表示不会受非继承特性的属性影响，所以，根本没法设置`vertical-align:middle`，除非你自己创建一个显示的内联元素。

我们就没有办法了吗？当然不是，“幽灵字符”可以受具有继承特性的CSS属性影响，于是，我们可以通过其他东西来做调整，让字符的中线和字符内容中心线在一起，或者说在一个位置上就可以了。有人可能要疑问了，这能行吗？啊，是可以的。

怎么搞？很简单，`font-size:0`, 因此此时content area高度是0，各种乱七八糟的线都在高度为0的这条线上，绝对中心线和中线重合。自然全垂直居中：

```
<pre class="wp-block-preformatted">div { line-height: 240px; font-size: 0; }
img { vertical-align: middle; }
```

结果是：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s256/mm1.jpg)</figure>处女座的你，是不是看过去舒服多啦！？

这种通过`line-height`定高，元素`vertical-align:middle`垂直居中的方法不仅适用于现代浏览器，连IE7浏览器也是支持的

不过和其他浏览器再使用上还是有些需要注意的地方，就是，HTML不能这样：

```
<pre class="wp-block-preformatted"><del><div><img src="mm1.jpg"></div></del>
```

而是需要在图片标签结束处留下空格后者换行：

```
<pre class="wp-block-preformatted"><div><img src="mm1.jpg"><!-- 这里要折行或空格 -->
</div>
```

**④ 复杂现象**

```
<pre class="wp-block-preformatted">.justify-fix { display: inline-block; width: 128px; }
```

```
<pre class="wp-block-preformatted"><div style="text-align: justify;">
    <img src="img/mm1.jpg" width="128">
    <img src="img/mm1.jpg" width="128">
    <img src="img/mm1.jpg" width="128">
    <img src="img/mm1.jpg" width="128">
    <i class="justify-fix"></i>
    <i class="justify-fix"></i>
    <i class="justify-fix"></i>
</div>
```

为了节约空间，我就使用小图示意：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>同样的，在白色背景下，似乎看上去效果还不赖，但是，如果给`div`容器加个背景色~~

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![大大的空白间隙](https://image.zhangxinxu.com/image/blog/201508/2015-08-29_230514.png)</figure>会惊讶的发现，下面多了很大一块间隙（如下截图）：

为了便于大家看其究竟，我把占位`i`元素`outline`高亮下，于是，效果如下：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>结果会发现，上面巨大的空隙是由占位`i`元素上面和下面的间隙共同组成的。

下面问题来了：上面的间隙是如何产生的？下面的间隙是如何产生的？如果去除这些间隙呢？

很多时候，复杂问题是由简单问题组合而成的，实际上，这里的间隙现象的始作俑者和上面的简单现象一样，都是`vertical-align`和`line-height`搞基带来的不好的影响。

按照之前问题解决方法，我们可以直接来个`line-height:0`解决垂直间隙问题：

```
<pre class="wp-block-preformatted">div { line-height: 0; }
```

<figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/18.gif)</figure>结果图片和图片之间的间隙是没有了，但是，图片和最后的占位元素之间依然有个几像素的间距，，啊啊啊啊，这究竟是什么鬼？

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>简单现象的背后往往有大的学问，接下来是本文的高潮了，究其原因，要说到inline-block元素和基线baseline之间的一些纠缠的关系。

**⑤ inline-block和baseline**  
CSS2的可视化格式模型文档中有一么一段话：

> The baseline of an ‘inline-block’ is the baseline of its last line box in the normal flow, unless it has either no in-flow line boxes or if its ‘overflow’ property has a computed value other than ‘visible’, in which case the baseline is the bottom margin edge.

英文看得眼睛大，于是我中文直译了下：

> ‘inline-block’的基线是正常流中最后一个line box的基线, 除非，这个line box里面既没有line boxes或者本身’overflow’属性的计算值而不是’visible’, 这种情况下基线是margin底边缘。

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/blog/201001/2010-01-20_230801.png)</figure>如果大家没有足够精力去学习之，可以先看下面这张图：

由于上面的译文是直译的，理解起来还是有些拗口，我使用通俗的话描述就是：**一个inline-block元素，如果里面没有inline内联元素，或者overflow不是visible，则该元素的基线就是其margin底边缘，否则，其基线就是元素里面最后一行内联元素的基线。**

纳尼，还是没反应过来？

那我们看下面这个例子，应该就知道什么意思了。

两个同尺寸的`inline-block`水平元素，唯一区别就是一个空的，一个里面有字符，代码如下：

```
<pre class="wp-block-preformatted">.dib-baseline {
  display: inline-block; width: 150px; height: 150px;
  border: 1px solid #cad5eb; background-color: #f0f3f9;
}
```

```
<pre class="wp-block-preformatted"><span class="dib-baseline"></span>
<span class="dib-baseline">x-baseline</span>
```

结果，科科：

 x-baseline

会发现，明明尺寸、display水平都是一样的，结果呢，两个却不在一个水平线上对齐，为什么呢？哈哈，上面的规范已经说明了一切。第一个框框里面没有内联元素，因此，基线就是容器的margin下边缘，也就是下边框下面的位置；而第二个框框里面有字符，纯正的内联元素，因此，第二个框框就是这些字符的基线，也就是字母x的下边缘了。于是，我们就看到了框框1下边缘和框框2里面字符`x`底边对齐的好戏。框框2有个小彩蛋，点击可以toggle其`innerHTML`，会发现，如果框框2里面没文字，就和框框1举案齐眉了。

下面我们要做一件很有必要的事情，用来帮助我们理解上面复杂例子在`line-height`值为`0`后的表现，什么事情呢？哈，同境界模拟，我们也设置框框2的`line-height`值为`0`，于是，就会是下面这样的表现：

 x-baseline

知道框框2为何又下沉了一点吗？

<figure class="wp-block-image">![犹抱琵琶半遮面](https://image.zhangxinxu.com/image/blog/201508/2015-08-29_235428.png)</figure>因为字符实际占据的高度是由行高决定的，当行高变成0的时候，字符占据的高度也是`0`，此时，高度的起始位置就变成了字符content area的垂直中心位置，于是，文字就一半落在看看2的外面了。

由于文字字符上移了，自然基线位置(字母`x`的底边缘)也往上移动了，于是，两个框框的垂直落差就更大了。

OK，明白了上面的简单例子，也就能明白上面的复杂例子。紧接着，如果我们在最后一个占位的`<i>`元素后面新增同样的`x-baseline`字符，则：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure> x-baseline

大家是不是就可以明白原因所在啦！

<figure class="wp-block-image">![基线对齐造成的间隙](https://image.zhangxinxu.com/image/blog/201508/2015-08-30_000413.png)</figure>额~居然还有小伙伴皱眉头，那我再用文字解释下：  
现在行高`line-height`是`0`, 则最后的`x-baseline`的垂直中线就和上面一列的图片对齐，而基线呢，就在中线下面差不多半个x的高度地方，而这个高度落差就是最后图片和容器的间隙高度值，因为前面的`<i class="justify-fix">`是个空元素，基线是自身的底部，哈哈，造业啊！

OK，一旦知道了现象的本质，我们就能轻松对症下药了！要么改造占位`<i>`元素的基线、要么改造“幽灵空白节点”的基线位置、要么使用其他`vertical-align`对齐方式~

首先，来个最有意思的方法，对吧，**改造占位`<i>`元素的基线**。这个很简单，对吧，只要在空的`<i>`元素里面随便放几个字符就可以了，例如，里面有个`x`：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>*x*x-baseline

<figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/4.gif)</figure>会发现，间隙没有了！ 为什么呢？哈哈，因为`<i>`元素的基线和“幽灵空白节点”的基线位置现在一致了，没有了错位，自然就不会有间隙啦！

**改造“幽灵空白节点”的基线位置**，哈哈，使用`font-size`，字体足够小时，基线和中线会重合在一起，什么时候字体足够小呢，就是`0`. 于是，CSS代码(`line-height`如果是相对值，`line-height:0`也可以省掉)：

```
<pre class="wp-block-preformatted">div { font-size: 0; }
```

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>**使用其他`vertical-align`对齐方式**，就是让两端对齐的列表元素`vertical-align:top/bottom/...`之类。

```
<pre class="wp-block-preformatted">div { line-height: 0; }
.justify-fix { display: inline-block; width: 128px; vertical-align: top; }
```

最后的效果是：

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>恩恩，各种方法都完美解决了垂直间隙的问题，来，各个大大的赞！