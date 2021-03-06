---
id: 721
title: 高度不固定的图片、多行文字的水平垂直居中
date: '2019-08-22T16:02:06+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/256-revision-v1/'
permalink: '/?p=721'
---

### 本文综述

想必写css的都知道如何让单行文字在高度固定的容器内垂直居中，但是您知道或者想过让行数不固定的文字在高度固定的容器内垂直居中呢？本文将会告诉你如何实现多行文字的垂直居中显示。

关于图片垂直居中显示，想必很多写css的人都研究过，或者说是搜寻过方法。淘宝团队似乎提供了一种不错的方法，用`font-size`解决IE下垂直居中的问题，是个好方法吗？是的，方法是不错，可是问题也很多：不支持`img`外标签的浮动，致使多图片排列时需添加额外标签；需要记住一些比例参数，上手较难；原理艰深，兼容性不稳定等。但是在我看来，这个方法不足够好！本文将提供两种更为新颖的方法，代码简洁，原理简单，上手容易，兼容性强，出错率低的方法。

css是如此的精深，我相信后来人会有更加绝妙的方法。但是，本文提供的几种实现图片垂直居中方法，一定是目前最实用的几种方法。

### 一、大小不固定，多行文字的垂直居中

#### ① 单行文字

可能很多人都知道如何让单行文字垂直居中显示，就是使用`line-height`，将`line-height`值与外部标签盒子的高度值设置成一致就可以了。例如如下css代码：

```
<pre class="wp-block-preformatted">height:3em; line-height:3em; ……
```

显示结果如下图：

<figure class="wp-block-image">![单行文本垂直居中对齐-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/200908/2009-08-27_223115.png)</figure>#### ② 多行文字

如何实现父容器高度固定，文字可能一行，两行或更多行的垂直居中对齐呢？  
实现的关键是把文字当图片处理。用一个`<span>`标签将所有的文字封装起来，设置文字与图片相同的`display`属性值（`inline-block`属性），然后用处理图片垂直居中的方式处理文字的垂直居中即可。  
核心css代码如下，  
外部`<div>`标签：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">div{
display:table-cell; 
width:550px; 
height:1.14em; 
padding:0 0.1em; 
border:4px solid #beceeb; 
color:#069; 
font-size:10em; 
vertical-align:middle;
}
```

内部`<span>`标签：

```
<pre class="wp-block-preformatted">span{display:inline-block; font-size:0.1em; vertical-align:middle;}
```

下面这张是实例演示页面的效果截图：

<figure class="wp-block-image">![行数不固定文字垂直居中对齐](http://image.zhangxinxu.com/image/blog/200908/2009-08-27_224624.png)</figure>有几点简要说明：

1. 此例子用`em`做单位，如果您对`em`单位了解不够，把握不来的话，可以使用`px`做单位，值要换；
2. 外部`div`不能使用浮动；
3. 外部`div`高度和文字大小比例`1.14`为宜；
4. 内部标签的`vertical-align:middle`可以省略，但是外部`div`高度和文字大小比例要修改，我自己试了一下，高度比字体`1.5`左右的样子；
5. 系统原因，我没能够在IE8下测试。

实现的最终效果和JavaScript演示，您可以狠狠地[点击这里](http://www.zhangxinxu.com/study/200908/img-text-vertical-align.html)去看看。

### 二、大小不固定，图片的水平垂直居中

#### ① 透明gif图片+背景定位的方法

这里利用了`background-position:center`实现图片居中显示。这是个很实用也是很聪明的办法，对于维护控制成本都很不错。微软必应图片搜索的图片排列就是使用的这种方法。  
方法的原理很简单，使用一个透明的gif图片做覆盖层，高宽拉伸至所需要的大小，然后给这个gif图片一个`background-position:center`声明。而`background-image`建议写在页面上，因为实际项目中，这肯定是个动态的URL地址，css文件似乎不支持动态URL地址。下面就是实例演示页面的效果截图。

<figure class="wp-block-image">![透明图片背景居中定位效果](https://image.zhangxinxu.com/image/blog/201712/2017-12-31_001005.png)</figure>核心HTML代码为：

```
<pre class="wp-block-preformatted"><img src="../image/pixel.gif" style="background-image:url(mm1.jpg);" />
```

其中此img标签已经设置了高宽和背景居中定位的属性。具体实现可以可以狠狠地[点击这里](http://www.zhangxinxu.com/study/200908/img-text-vertical-align.html)进行参照。

**② display:table-cell和文字大小控制居中**

这个方法可以说就是本文综述部分提到的淘宝团队想出的图片垂直居中的方法。但是本文展示的这个方法的css代码又不是淘宝工程师们的原版代码，我对其做了一定的修改，把里面没有用可以剔除的hack都去掉了。

css代码部分：  
`<img>`外部`<div>`标签：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">div {
display:table-cell;
<del> width:1em; height:1em; </del>
<del>font-size:144px;</del> 
width:144px; 
height:144px; 
font-size:118px; 
border:1px solid #beceeb; 
text-align:center; vertical-align:middle;
}/*这里的大小是根据高宽上限128像素图片设置的*/
```

img标签：

```
<pre class="wp-block-preformatted">img{vertical-align:middle;}
```

**需要说明的：**  
1.原版的代码中有两个hack，一个是针对文字大小的，另外一个是针对`block`属性的；后经过我测试推敲后证实这两个hack都是多余的，而`*display:block`更是多余，因为IE6,IE7根本就不认识`display:table-cell`是谁！  
2.这个通过文字大小控制IE下图片垂直居中是个很不错的方法，要比使用`position:relative`这类高消耗的css方法要好多了。但是这个方法不足之处在于：不支持`<img>`外标签的浮动，所以当多图片显示时需要再在外面嵌套一层标签——资源消耗多了！  
3.高度：文字大小=1.14，这个比例我一直记着，但是后来我将这个比例的概念淡化了，原因在于多次遇到其他比例实现效果的情况。例如上面，就是1:1实现的。

这里附上效果截图：

<figure class="wp-block-image">![table-cell下的水平垂直居中效果截图](https://image.zhangxinxu.com/image/blog/201712/2017-12-31_001256.png)</figure>#### ③ display:inline-block和文字大小控制居中

此方法的灵感来自于Google picasa相册的图片显示，代码相当简洁，是个成本很低，效果惊人的方法。适用于多图显示的情况。基本上用裸标签就实现了想要达到的效果。一般而言，图片阵列排列显示的时候，外面都有一个a标签的，起到链接的作用。而本处的方法就只要这一个`<a>`标签就足以实现图片垂直且居中的显示效果。其关键是将`<a>`标签默认的`inline`属性值设置为inline-block，这样`<a>`标签既支持宽度，又支持`vertical-align:middle`，配合`<img>`的`vertical-align:middle`就可以实现图片的水平垂直居中显示了。  
css代码：  
`<a>`标签：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">a{
display:inline-block; 
width:1.2em;
font-size:128px; 
text-align:center; 
vertical-align:middle;
}
```

img标签：

```
<pre class="wp-block-preformatted">img{vertical-align:middle;}
```

就这么简单，就实现了图片的垂直居中效果。下截图为证：

<figure class="wp-block-image">![精简方法实现的水平垂直居中对齐](https://image.zhangxinxu.com/image/blog/201712/2017-12-31_001420.png)</figure>另外补充说明的：`<img>`外的标签需是`<a>`标签或`<span>`这类`inline`水平的标签，`<div>`标签也可以，但是css代码多些: display:inline-block;\*display:inline; zoom:1; IE下要先变成`inline`属性，再转变成类似于`inline-block`的属性，原因讲起来又是一篇长长的文章，放着。

#### ④ 透明图片拉伸对齐实现垂直居中显示

这个方法是自认为相当不错的一个方法，说实话，想到这个方法是自然而然的，经过试验发现真的很管用。对比淘宝团队的方法，发现优势很多，基本上淘宝的`table-cell`+`font-size`方法会带来的些问题都避免了。  
非常简单，非常易懂，出错率低，上手方便，兼容性上佳！

**原理简述：**  
一句话，将要显示的图片与一张透明的高度100%，宽度1像素的透明图片`vertical-align:middle`对齐。  
其核心原理其实与利用`font-size`大小实现IE下图片垂直居中是一致的。将`font-size`设置得很大，目的是撑开IE下默认文字空间的高度，其性质类似于空格，然后通过`vertical-align:middle`属性让图片与这个高高的空白空格空间垂直居中对齐；而这里将这个看不见的文字空间实例成一张透明的gif图片，高度可以轻松设置为外部标签的高度，宽度压缩至最小，然后通过`vertical-align:middle`对齐，就实现效果了，由于影响布局的差不多就`vertical-align:middle`这一个属性，所以出现兼容性问题的可能性比`table-cell`的方法要低得多。

**需要注意的：**  
1.`<img>`外容器宽度要大于要显示的图片的最大宽度`+1`像素；  
2.`<img>`外容器的字体大小设为`0px`，也可以不设，但是两个`<img>`标签要连着写，避免空格；

以下是核心的一些代码：  
HTML部分：

```
<pre class="wp-block-preformatted"><li>
    <img src="http://image.zhangxinxu.com/image/study/s/s128/mm1.jpg" /><img src="../image/pixel.gif" />
</li>
```

css部分：

```
<pre class="wp-block-preformatted">li{height:128px; width:150px; padding:13px 0; float:left; margin-right:10px; border:1px solid #beceeb; text-align:center;<del> font-size:0;</del>}
<del>li .alpha_img{height:100%; width:1px; vertical-align:middle;}</del>
li .alpha_img{height:100%; width:0; vertical-align:middle;}
li .show_img{vertical-align:middle;}
```

其实，只要有上面绿色部分的css代码就足够了。  
简单的超乎想象。

要显示的图片后面跟了一个高度撑满容器，<del>只有1px宽</del>，宽度为0即可，透明的图片。这两个图片分别`vertical-align:middle`，就实现了要显示的图片与这个拉伸的透明图片的居中对齐了，由于透明图片是透明的，不可见的，<del>宽度也只有1像素</del>宽度也为0，所以看上去就是要显示的图片相对于容器垂直居中了。

另外，其实这里不一定是透明的图片，使用任意的图片都可以，所以您其实说不定可以减少一次http请求，使用任意的图片将其拉伸至高度为容器高，<del>宽度1像素</del>宽度0就可以了。

<del>甚至，使用`<span>`标签，`<div>`标签也可以实现同样的效果。只要将`<span>`标签或`<span>`标签转换成`inline-block`表现或类似于`inline-block`表现就可以了。</del>  
我个人更推荐使用行内元素，例如`<span>`，`<i>`标签等，只要应用类似下面的CSS：

```
<pre class="wp-block-preformatted">span{display:inline-block; height:100%; width:0; vertical-align:middle;}
```

相比图片而言，多了个`display:inline-block`，但是会少一次链接请求。效果图和上面的一样的，完美的水平垂直居中。原理也与图片一样，相信很容易理解的。

最后，说句实在话，我知道这应该不算什么新颖的方法，在我之前想到这种方法的人肯定不在少数，但是留迹于网上的却没有，我是没有发现过。我不清楚是这些大牛的公司要求技术保密，还是自身不愿分享研究成果。我相信不是这样的，主要还是他们很忙碌，没有时间写这些东西。像我这样，”闲暇”的很，花十几个小时写一篇文章的估计不多，呵呵。

**补充于2009-11-03 15:32**  
正如我经常挂在嘴边的，css博大精深，你需要花费精力去观察它，研究它。这里，我再提供一种我刚刚试验出来的一种新方法，实现大小不固定的图片水平垂直居中，综合来讲，比上面所有提供的方法还要优秀，且没有hack，兼容性上佳（支持IE6、IE7、Firefox、chrome、Safari浏览器-IE8未测（**补充：**我后来又测试了一下，结果在IE8浏览器和Opera浏览器下是不垂直居中的，所有此方法还是有待商榷的）。下为在IE7下的效果截图：

<figure class="wp-block-image">![新方法实现垂直居中](https://image.zhangxinxu.com/image/blog/201712/2017-12-31_002346.png)</figure>此方法原理是，在IE下使用`font-size`使图片垂直居中显示，Firefox，chrome等现代浏览器使用`line-height`配合`<img>`本身的`vertical-align`属性使垂直居中显示，由于两者不冲突，所以没有hack就实现的效果。css代码简洁明了，关键是HTML代码非常清晰，一层外标签，里面就是img标签，我想很难再找出比这个方法更出色的让图片水平垂直居中的方法了。好吧，其实这个方法是有一点不完美的，就是opera浏览器下图片无法垂直居中显示，幸好国内而言，使用opera浏览器的比例很低，所以综合来讲这个方法是个非常优秀的图片水平垂直居中的方法。

以下为css代码部分：

```
<pre class="wp-block-preformatted"><del>.zxx_ul_image li{float:left; width:150px; height:150px; text-align:center; line-height:150px; font-size:125px;}
.zxx_ul_image li img{vertical-align:middle;}</del>

.zxx_ul_image li{float:left; width:150px; height:150px; text-align:center; line-height:150px; *font-size:125px;}
.zxx_ul_image li:after{content:' '; vertical-align:middle;}
.zxx_ul_image li img{vertical-align:middle;}
```