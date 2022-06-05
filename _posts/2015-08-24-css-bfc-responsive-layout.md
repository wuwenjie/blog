---
id: 470
title: 'CSS BFC实现多栏自适应布局'
date: '2015-08-24T18:38:33+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=470'
permalink: /2015/08/css-bfc-responsive-layout
categories:
    - 'HTML&amp;CSS'
---

### 一、开篇之言

要说web上实现两栏自适应布局的方法，一双手都数不过来。不知大家有没有细想过，为什么这些方法可以实现自适应布局呢？

本文就将深入探讨下流体特性和BFC特性下的两栏自适应布局，还是针对传统布局。一些现代布局，如弹性盒子模型布局(Flexbox Layout)，格栅布局(Grid Layout)不在本文探讨之类。

> 有些人看了个标题，以及看了前面一两段，发现，都是我知道的概念嘛，什么流动性，什么BFC~~于是，就悻悻离开了。这就是我们常说的浮躁，保持一颗谦逊的心，细细阅读，你会发现，其中一定有你所没有关注过的地方，所谓三人行必有我师。没错，这句话就是写给你看的，同时也是自我内省与监督。

### 二、块状元素的流体特性与自适应布局

**流体特性**  
块状水平元素，如`div`元素（下同），在默认情况下（非浮动、绝对定位等），水平方向会自动填满外部的容器；如果有`margin-left/margin-right`, `padding-left/padding-right`, `border-left-width/border-right-width`等，实际内容区域会响应变窄。

一图胜千言，一例胜千图。可参考下面例子，感受下`div`元素的流体特性：

<figure><iframe height="520" loading="lazy" src="https://www.zhangxinxu.com/study/201502/div-flow.html" width="510"></iframe></figure>图片宽度一直`width:100%`，依次点击3个按钮，结果随着`margin`, `padding`, `border`的出现，其可用宽度自动跟着减小，形成了自适应效果。就像放在容器中的水流一样，内容区域会随着`margin`, `padding`, `border`的出现自动填满剩余空间，这就是块状元素的流体特性。

**流体特性**  
下面，我们稍微做一个调整，`div`距离容器左侧`margin` `150`像素，里面的图片同样`100%`自适应内容区域。HTML如下：

```
<pre class="wp-block-preformatted">.flow-box {
    width: 500px; background-color: #eee; overflow:auto; resize:horizontal;
}
.flow-content {
    margin-left: 150px;
}
```

```
<pre class="wp-block-preformatted"><div class="flow-box">
    <div class="flow-content"><img src="mm1.jpg" width="100%" height="190"></div>
</div>
```

<figure><iframe height="215" loading="lazy" src="https://www.zhangxinxu.com/study/201502/margin-left.html" width="100%"></iframe></figure>图片右下角有两道斜杠，我们可以resize拉伸（现代浏览器，且非移动访问），会发现，左侧永远150像素留白，而图片随着容器宽度变化而自适应变化了。

此时，我们需要好好利用左侧150像素的留白间距，岂不是就可以实现两栏自适应效果！？

为了不影响原本的流体特性，我们可以使用破坏性属性，如浮动(float:left)，或者绝对定位(position:absolute)。

我们直接HTML如下调整即可：

```
<pre class="wp-block-preformatted"><div class="flow-box">
    <img src="mm1.jpg" width="128" style="float:left;">
    <div class="flow-content"><img src="mm1.jpg" width="100%" height="190"></div>
</div>
```

```
<pre class="wp-block-preformatted"><div class="flow-box">
    <img src="mm1.jpg" width="128" style="position:absolute;">
    <div class="flow-content"><img src="mm1.jpg" width="100%" height="190"></div>
</div>
```

<figure><iframe height="215" loading="lazy" src="https://www.zhangxinxu.com/study/201502/float-flow.html" width="100%"></iframe></figure><figure><iframe height="215" loading="lazy" src="https://www.zhangxinxu.com/study/201502/absolute-flow.html" width="100%"></iframe></figure>结果分别如下：

当然，你可以左侧有多个浮动，或者左浮动+右浮动。于是，我们不仅可以实现两栏自适应效果，多栏自适应效果也不在话下。

然而，利用块状元素流体特性实现的自适应布局有个不足，就是，我们需要知道浮动或绝对定位内容的尺寸。然后，流体内容才能有对应的`margin`或`padding`或`border`值进行位置修正。于是，问题来了，我们没法单纯使用一个公用的类名，类似`.clearfix`这样，整站通用。因为不同自适应场景的留白距离是不一样的。

此时，我们可以利用块状元素的BFC特定实现更强大更智能的多栏自适应布局（本文重点）。

### 三、元素的BFC特性与自适应布局

**1. BFC元素简介与基本表现**  
BFC全称”Block Formatting Context”, 中文为“块级格式化上下文”。啪啦啪啦特性什么的，一言难尽，大家可以自行去查找，我这里不详述，免得乱了主次，总之，记住这么一句话：BFC元素特性表现原则就是，内部子元素再怎么翻江倒海，翻云覆雨都不会影响外部的元素。所以，避免margin穿透啊，清除浮动什么的也好理解了。

<figure class="wp-block-image">![抱诈（和谐）与钓鱼](https://image.zhangxinxu.com/image/blog/201502/inner-outer.png)</figure>什么时候会触发BFC呢？常见的如下：

- `float`的值不为`none`。
- `overflow`的值为`auto`,`scroll`或`hidden`。
- `display`的值为`table-cell`, `table-caption`, `inline-block`中的任何一个。
- `position`的值不为`relative`和`static`。

BFC特性很多，而我们这里，只关心一个，和`float`元素做相邻兄弟时候的表现。

<figure><iframe height="180" loading="lazy" src="https://www.zhangxinxu.com/study/201502/flow-to-bfc.html" width="100%"></iframe></figure>如果是上面介绍的流体特性`div`, 当其和浮动元素当兄弟的时候，是覆盖的关系（可以脑补下文字环绕图片效果）。但是，元素BFC化后，本着“里面惊天抱诈（和谐）炸成鬼，外面依然泰然钓大鱼”的原则，自然是不会与浮动重叠的（你想啊，要是出来个`clear:both`还不跟外面浮动干上一架啊），因此，块状相邻，点击下面按钮感受下。

<figure class="wp-block-image">![BFC与浮动边缘对齐~](https://image.zhangxinxu.com/image/blog/201502/2015-02-10_003132.png)</figure>会发现，普通流体元素BFC后，为了和浮动元素不产生任何交集，顺着浮动边缘形成自己的封闭上下文。如下截图：

同时，**元素原本的流体特性依然保留了**。哈，这个很重要，也就是，虽然不与浮动交集，自动退避浮动元素宽度的距离，但，本身作为普通元素的流动性依然存在，反映在布局上就是自动填满除去浮动内容以外的剩余空间。哟，这不就是自适应布局嘛！！

**2. BFC自适应布局模块间的间距**  
然而，模块过于亲密接触，可能不是我们想要的。一般而言，我们需要一点间距。

说到间距，我们的第一反应就是`margin`. 于是，我们给BFC元素增加一个`margin-left:20px`, CSS代码如下：

```
<pre class="wp-block-preformatted">.float-left {
    float: left;
}
.follow-content {
    margin-left: 20px;
    background-color: #cad5eb;
    overflow: hidden;
}
```

<figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/110.gif)</figure><figure class="wp-block-image">![margin-left:20px无效截图](https://image.zhangxinxu.com/image/blog/201502/2015-02-12_001810.png)</figure>结果……纳尼~ 怎么还是像狗屁膏药贴在一起啊？？

您可以狠狠地点击这里：[BFC元素增加一个margin无效demo](http://www.zhangxinxu.com/study/201502/flow-to-bfc-margin.html)

实际上，这里的**margin并不是无效，而是值不够大**，鞭长莫及啊！

<figure class="wp-block-image">![朕真是鞭长莫及](https://image.zhangxinxu.com/image/blog/201502/bianchangmoji.jpg)</figure>左侧浮动的图片就好比上面Gif图片中男孩的胳膊，妹子就是BFC元素，结果两人紧密接触。然后，`margin-left`就是妹子的胳膊个脚，虽然也甩出去了，可惜长度有限，于是，毫无影响。

如果按照上面的解释，那我们把`margin-left:20px`改成`margin-left:150px`就应该有间距了？ 一试便知！

```
<pre class="wp-block-preformatted">.float-left {
    float: left;
}
.follow-content {
    margin-left: 150px;
    background-color: #cad5eb;
    overflow: hidden;
}
```

<figure class="wp-block-image">![margin值很大收获的间距](https://image.zhangxinxu.com/image/blog/201502/2015-02-12_003900.png)</figure>结果，当当当当：

**注意：**我这里举`margin`这个例子，不是让大家这样使用，只是为了让大家可以深入理解BFC元素与浮动元素混排的特性表现。实际开发，我们完全没有必要对BFC元素设置`margin`, 因为又回到了流体布局，明明是固定的15像素间距，但是，每个布局都要写一个不同的`margin`值，完全没有重用价值。

但是，间距部分的高潮来了！

我们可以使用浮动元素的`margin-right`或者`padding-right`轻松实现间距效果。间距是`20`像素，直接：

```
<pre class="wp-block-preformatted">.float-left {
    float: left;
    margin-right: 20px;
}
```

与浮动元素的宽度是多少没有任何关系。不仅如此，我们还可以使用BFC元素的`padding-left`撑开间距（虽然`margin-left`作用鸡肋）。

于是，我们可能就会有：

```
<pre class="wp-block-preformatted">.l { float: left; }
.ovh { overflow: hidden; }
```

**3. 与纯流体特性布局的优势**  
BFC自适应布局优势我总结了下面2点：

1. 自适应内容由于封闭，更健壮，容错性强。比方说，内部`clear:both`不会与兄弟`float`产生矛盾。而纯流体布局，`clear:both`会让后面内容无法和`float`元素在一个水平上，产生布局问题。
2. 自适应内容自动填满浮动以为区域，无需关心浮动元素宽度，可以整站大规模应用。而纯流体布局，需要大小不确定的`margin`/`padding`等值撑开合适间距，无法CSS组件化。

如下效果，图片能大能小，布局依然良好：

<figure><iframe height="216" loading="lazy" src="https://www.zhangxinxu.com/study/201502/flow-to-bfc-animation.html" width="400"></iframe></figure>而CSS代码就是非常简单的：

```
<pre class="wp-block-preformatted">.float-left {
    float: left; margin-right: 20px; 
}
.bfc-content {
    overflow: hidden; background-color: #beceeb;
}
```

可以说，有了BFC自适应布局，基本上没有了纯流体特性布局存在的价值。然而，只是理论上如此。如果，BFC自适应布局真那么吊炸天，那为何并没有口口相传呢？

那我们就得进一步深入理解了。

**4. BFC元素家族与自适应布局面面观**  
理论上，任何BFC元素和浮动搞基的时候，都可以实现自动填充的自适应布局。

但是，由于绝大多数的触发BFC的属性自身有一些古怪的特性，所以，实际操作的时候，能兼顾流体特性和BFC特性来实现无敌自适应布局的属性并不多。下面我们牵驴遛马一个一个瞅瞅（类似行为仅出1个代表示意，你懂的，如`float:left/right`）：

1. **float:left** 浮动元素本身BFC化，然而浮动元素有破坏性和包裹性，失去了元素本身的流体自适应性，因此，无法用来实现自动填满容器的自适应布局。不过，其因兼容性还算良好，与堆积木这种现实认知匹配，上手简单，因此在旧时代被大肆使用，也就是常说的“浮动布局”，也算阴差阳错开创了自己的一套布局。
2. **position:absolute** 这个脱离文档流有些严重，过于清高，不跟普通小伙伴玩耍，我就不说什么了……
3. **overflow:hidden** 这个超棒的哦！不像浮动和绝对定位，玩得有点过。也就是溢出剪裁什么的，本身还是个很普通的元素。因此，块状元素的流体特性保存相当完好，附上BFC的独立区域特性，可谓如虎添翼，宇宙无敌！哈无诶瓦(However), 就跟清除浮动：.clearfix { overflow: hidden; \_zoom: 1; }一样。由于很多场景我们是不能`overflow:hidden`的，因此，无法作为一个通用CSS类整站大规模使用。因此，`float+overflow`的自适应布局，我们可以在局部（你确定不会有什么被剪裁的情况下）很happy地使用。
4. **display:inline-block** CSS届最伟大的声明之一，但是，在这里，就有些捉襟见肘了。`display:inline-block`会让元素尺寸包裹收缩，完全就不是我们想要的`block`水平的流动特性。唉，只能是一声叹气一枪毙掉的命！然而，峰回路转，世事难料。大家应该知道，IE6/IE7浏览器下，`block`水平的元素设置`display:inline-block`元素还是`block`水平，也就是还是会自适应容器的可用宽度显示。于是，我们就阴差阳错得到一个比`overflow:hidden`更牛逼的声明，即BFC特性加身，又流体特性保留。.float-left { float: left; } .bfc-content { display: inline-block; }当然，`*zoom: 1`也是类似效果，不过只适用于低级的IE浏览器，如IE7~
5. **display:table-cell** 让元素表现得像单元格一样，IE8+以上浏览器才支持。跟`display:inline-block`一样，会跟随内部元素的宽度显示，看样子也是不合适的命。但是，单元格有个非常神奇的特性，就是你宽度值设置地再大，大到西伯利亚，实际宽度也不会超过表格容器的宽度。  
    因此，如果我们把`display:table-cell`这个BFC元素宽度设置很大，比方说3000像素。那其实就跟`block`水平元素自动适应容器空间效果一模一样了。除非你的容器宽度超过3000像素，实际上，一般web页面不会有3000像素宽的模块的。所以，要是你实在不放心，设个`9999`像素值好了！.float-left { float: left; } .bfc-content { display: table-cell; width: 9999px; }看上去，好像还不错。但是，还是有两点制约，一是IE8+以上浏览器兼容，有些苦逼的团队还要管IE6；二是应付连续英文字符换行有些吃力（可以嵌套`table-layout:fixed`解决）。但是，总体来看，适用的场景要比`overflow:hidden`广博很多。
6. **display:table-row** 对`width`无感，无法自适应剩余容器空间。
7. **display:table-caption** 一无是处……还有其他声明也都是一无是处，我就不全部展开了……

**总结：**我们对BFC声明家族大致过了一遍，能担任自适应布局重任的也就是：

1. `overflow:auto/hidden` IE7+
2. `display:inline-block` IE6/IE7
3. `display:table-cell` IE8+

由于overflow有剪裁和出现滚动条等隐患，不适合作为整站通用类，于是，最后，类似清除浮动的通用类语句：

```
<pre class="wp-block-preformatted">.clearfix {
    *zoom: 1;
}
.clearfix:after {
    content: ''; display: table; clear: both;
}
```

两栏或多栏自适应布局的通用类语句是（`block`水平标签，需配合浮动）：

```
<pre class="wp-block-preformatted">.cell {
    display: table-cell; width: 9999px;
    *display: inline-block; *width: auto;
}
```

当然，由于和浮动元素合作，清除浮动还是要的，于是，就有了`.fix` + `.l/.r` + `.cell`的无敌组合，可以多栏，也可以无限嵌套。

如果是局部，且确认安全；或有连续英文字符换行的隐患，你也可以使用`.fix` + `.l/.r` + `.ovh`的无敌组合，可以多栏，也可以无限嵌套。