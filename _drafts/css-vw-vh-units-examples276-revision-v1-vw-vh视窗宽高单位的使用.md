---
id: 277
title: 'vw, vh视窗宽高单位的使用'
date: '2019-08-16T19:55:19+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/276-revision-v1/'
permalink: '/?p=277'
---

### 一、移动端经常用到

<figure class="wp-block-image">![视区相关单位vh, vw截图](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_152155.png)</figure>不过“我看见你”和“我触碰你”是不一样的。正好，机缘巧合，最近又与这两个单位想见。大致琢磨了下，貌似vh这个单位可以实现我以前曾希望实现的的整体高度自适应布局。想到这里，自己不由得小兴奋了下，于是决定抽时间研究研究（虽然最近整iPad忙得屁股尿流~~）。

然而…… //zxx: 先卖个关子，一点一点唠叨来~~

vw, vh这个可用来实现动态布局的单位到底潜力如何？我不想直接吐露；请跟随我的学习印记以及心理历程一起去寻找答案吧~~

### 二、需要提前知道的兼容性

`vw`, `vh`, `vmin(vm)`这几个视区相关单位，在2012年9月23号这天的兼容性为：Chrome 20+, IE9+ 以及Safari 6支持！

<figure class="wp-block-image">![视区相关单位兼容性表 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_161025.png)</figure>著名的CSS属性可用性查询网站[caniuse](http://caniuse.com/)给出了具体的兼容性表，点击[这里](http://caniuse.com/viewport-units)查看。

因此，本文后面要展示的N个demo，就没有必要再低版本的IE浏览器上查看了~~

### 三、明确含义

看到上图黄色背景标示的文字（“视窗”用“视区”一词代替更恰当）：

> vw 相对于视窗的宽度：视窗宽度是100vw

我的第一反应是：如果视区宽度是`100vm`, 则`1vm`是视区宽度的`1/100`, 也就是`1%`，类似于`width: 1%`. 但是，这里多次出现的“视窗”是纳尼意思？

是浏览器内部宽度大小(`window.innerWidth`)？是整个浏览器的宽度大小(`window.outerWidth`)？还是显示器的宽度大小(`screen.width`)？我疑惑了！

每当我疑惑的时候，我不是去找个“我觉得应该是”的解释，而是，新建个HTML页面，像学生时代做生物实验般，多条件对比验证之。

<figure class="wp-block-image">![vw单位所谓视区指什么的demo IE9下截图 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_160411.png)</figure>下图为在IE9浏览器下默认打开的效果：

显然，这里的“视区”不可能是浏览器外部的宽度，计算值不匹配。

<figure class="wp-block-image">![浏览器宽度改变IE9浏览器下的图片宽度 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_161812.png)</figure>我们改变浏览器的宽度，然后会看到：

至此，真相大白，**“视区”所指为浏览器内部的可视区域大小**，即`window.innerWidth/window.innerHeight`大小，不包含任务栏标题栏以及底部工具栏的浏览器区域大小。

<figure class="wp-block-image">![修改vw大小后的效果截图 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_162422.png)</figure>修改`vw`对应宽度值，图片的尺寸大小可以进一步验证上述结论：

**注：**一般情况下，Chrome浏览器浏览器内外宽度是一样的（因为浏览器左右无边框）；加上浏览器大小变小时图片尺寸不渲染的bug，因此，上demo最佳测试浏览器是IE9. // zxx: 不容易啊，IE系终于勃起了一把~~

### 四、承上启下

视区相关单位`vw`, `vh`目前浏览器的支持算是比较弱的，因此，基本上不可能从现有的站点上找到相关的实际应用。因此，我没事的时候，脑子里就要盘算，该单位可用在何处呢？如果跟其他CSS3的属性配合使用呢？

首先，很容易想到的是，宽度的自适应布局，例如，两栏1:3的宽度自适应布局：

```
<pre class="wp-block-preformatted">.sidebar {
    width: 25vw;
    float: left;
}

.main {
    width: 75vm;
    float: right;
}
```

但是，block水平元素本身具有自适应性，加上这里的`vm`相比`%`并没有什么优势。因此，`vw`单位用做宽度自适应的布局，完全是吃力不讨好得显摆！

我们需要想的是其他一些只能`vw`, `vh`才能完成的应用场景，这就是下面依次要展示的内容~~

### 五、场景之：元素的尺寸限制

<figure class="wp-block-image">![图片永远在一屏内显示 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_165434.png)</figure>我们应该都做过或见过这样的交互：点击下图，弹框查看原始大图；或者一屏内（不能有滚动条）大图幻灯片浏览。这类需求让人头疼的地方之一就是原始大图的尺寸限制问题——因为很有可能图片过大，尼玛一屏显示器区域不够放，我们需要对其进行缩放处理。例如：[点击这里查看](http://www.xiaomishu.com/shop/C19F03N13653/slides/?pid=853569&t=1)（无论浏览器尺寸多小，图片永远在一屏内显示）。

这类限制的实现，在当下，需要获得图片的原始大小，以及浏览器内部尺寸，算大小，算比例等，算是比较折腾的。

但是，`vw`, `vh`等单位本身就是浏览器视区大小相关单位，直接使用其做限制，岂不省了N多JS代码？？

```
<pre class="wp-block-preformatted">img { max-height: 90vh; }
```

<figure class="wp-block-image">![FireFox 15.0.1下点击图片缩略图 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_200637.png)</figure><figure class="wp-block-image">![弹框大图片使用vh限制高度无效的模样 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_201052.png)</figure>例如，在暂未支持`vh`单位的FireFox 15浏览器下，点击缩略图，会看到高高的图片完全溢出在屏幕之外（没有被限制住 – 父容器没有固定高度值，因此`90%`打酱油）：

<figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/37.gif)</figure>连弹框一起被废掉了！

<figure class="wp-block-image">![IE9浏览器下使用vh高度限制成功截图 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_201439.png)</figure>而支持`vh`单位的IE9浏览器呢~~当当当当，见下面截图：

<figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/131.gif)</figure>IE浏览器这回终于是“媳妇熬成婆”了，不容易，大家鼓掌……

相关图片限制CSS如下：

```
<pre class="wp-block-preformatted">.vw_vh_img {
    max-width: 90vw;
    max-height: 90vh;
}
```

注：demo页面使用的弹框脚本就是之前“[seajs使用示例及spm合并压缩工具](http://www.zhangxinxu.com/wordpress/?p=2476)”一文中展示的最终脚本。

### 六、视区覆盖以及边界定位

既然`vw`, `vh`是视区相关单位，我就想到是不是可以利用这个特性实现精确的视区大小覆盖以及视区边界的定位。

拿视区覆盖举例，如果我定义一个元素的高宽如下：

```
<pre class="wp-block-preformatted">.element {
    width: 100vw;
    height: 100vh;
}
```

然后，再将其定位到视区左上角，岂不是可以实现视区的完整覆盖；我立马想到了弹出框的半透明覆盖层。您可以狠狠地点击这里：[vw, vh视区完全覆盖与纯CSS弹框](http://www.zhangxinxu.com/study/201209/vw-vh-css-custom-dialog.html)

<figure class="wp-block-image">![](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_204852.png)</figure><figure class="wp-block-image">![vh, vw单位下的半透明覆盖层的完整覆盖 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_204933.png)</figure>建议在Chrome20+浏览器下查看效果（因为有range控件），点击demo页面按钮，则半透明覆盖层显现了——完整覆盖：

**吐槽：**

1. 如果您在FireFox浏览器下查看本demo，会发现，FireFox浏览器下的黑色半透明也是完整覆盖的（图略~），为何？其目前是不支持`vw`, `vh`单位的啊！？  
    原因就在于，覆盖层为固定定位(fixed)元素（绝对定位(absolute)元素也如此）。本demo中，其高宽100%的效果就跟设置`width: 100vw; height: 100vh;`是一模一样的。I am a little disappointed!
2. OK，看[上面demo](http://www.zhangxinxu.com/study/201209/vw-vh-css-custom-dialog.html)标题可以发现，本demo最重要的知识点其实并不在于`vw`, `vh`这两个单位的介绍；而是展示了如果使用纯CSS实现弹框的水平与垂直居中效果（IE6也是可以支持的，不过写法需要变变~以后有机会详细介绍）。拖动range控件，可以看到图片尺寸无论怎样变，弹框总是居中的——纯CSS实现，没有JavaScript的计算与定位，您有兴趣可以研究研究的~~

正如上面所提到的，某些情况下，`vw`, `vh`所产生的效果与百分比`%`单位无异，尤其对于`absolute/fixed`定位属性的元素，例如下面这个边界定位的例子：

您可以狠狠地点击这里：[vh, vw等同百分比单位测试demo](http://www.zhangxinxu.com/study/201209/units-vh-vw-position-fixed.html)

//zxx: 我这里更多地是为了演示，其实要实现效果，最简单就是`bottom:0`.

关键CSS代码如下：

```
<pre class="wp-block-preformatted">{
    height: 30px;
    margin-top: -30px;

    position: fixed;
    top: 100%;
    top: 100vh;
    left: 5%;
    left: 5vw;
    right: 5%;
    right: 5vw;
}
```

代码含义很简单，支持`vh`, `vw`单位的使用之（因为在后面声明）；不支持的就是要百分比`%`单位。

<figure class="wp-block-image">![IE7浏览器下底部工具条固定效果 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_212141.png)</figure>然后各个浏览器测试发现，效果是一模一样的（不支持position: fixed的IE6就当它不存在吧），固定在视区底部，不随滚动条滚动的空白工具栏：

说实话，原本第一眼看到单位`vw`, `vh`的时候，觉得这个单位，说不定会引发目前布局方式的大变革——水平方向流体布局！！

<figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/38.gif)</figure>在制作高宽限制demo的时候，我还觉得，应该是可以的。尼玛，当我做覆盖以及定位这两个demo的时候，心一下子凉下去了：

1. `vw`, `vh`用在宽度自适应上没有价值——`%`可以实现之~~
2. 现在又：`vw`, `vh`用在`absolute/fixed`定位属性元素上没有价值——`%`可以实现之~~

`vw`, `vh`这两个视区相关动态单位似乎应用前景一下子黯淡了很多，潜力似乎也就那样——想来想去，得出一个结论：**vw, vh视区大小相关单位只适用于非定位元素的高度相关属性上！** //zxx: 高度相关属性如 – height/min-height/max-height/line-height/padding-top/padding-bottom等

<figure class="wp-block-image">![](https://mat1.gtimg.com/www/mb/images/face/32.gif)</figure>于是，我下面所设想的应用场景就会脱离宽度，脱离绝对定位元素，会是什么呢？？

### 七、场景之：Office Word效果

我们可以把web页面做得像Office文档那样，一屏正好一页；拖动滚动条，我们可以一直往下看到最后一页。

如果只借助CSS，这种效果绝对定位是实现不了的。因为其top值是动态的(100%, 200%, 300% …)，必须借助JavaScript才能实现。而使用`vh`单位，既能捕获浏览器可视区域高度，又不脱离文档流，真是实现Office Word效果最佳利器！

您可以狠狠地点击这里：[vh单位模拟office word效果demo](http://www.zhangxinxu.com/study/201209/css3-vh-vw-office-word-effect.html)

建议使用Chrome20+浏览器查看demo，IE9浏览器下背景图片的vh单位定位似乎有bug!

<figure class="wp-block-image">![高度vh模拟office word的界面效果 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-23_225650.png)</figure>您会看到大致如下的效果（下图为手动改小浏览器高度后的效果，实际效果更佳）：

相关CSS代码如下：

```
<pre class="wp-block-preformatted">page {
    display: block;
    height: 98vh;
    width: 69.3vh;
    margin: 1vh auto;
    padding: 12vh;
    border: 1px solid #646464;
    box-shadow: 0 0 15px rgba(0,0,0,.75);
    box-sizing: border-box;
    background: url(office/bl.png) no-repeat 8vh 88vh,
                url(office/br.png) no-repeat 59vh 88vh,
                url(office/tl.png) no-repeat 8vh 8vh,
                url(office/tr.png) no-repeat 59vh 8vh;
    background-color: white;
    position: relative;
}
page:after {
    content: attr(data-page);
    color: graytext;
    font-size: 12px;
    text-align: center;
    bottom: 4vh;
    position: absolute;
    left: 10vh;
    right: 10vh;
}
```

HTML代码如下：

```
<pre class="wp-block-preformatted"><page></page>
<page></page>
<page></page>
```

JavaScript代码为创建data-page属性值，如下：

```
<pre class="wp-block-preformatted">var elePages = document.querySelectorAll("page"), lenPage = elePages.length;
for (var i=0; i<lenPage; i+=1) {
    elePages[i].setAttribute("data-page", "第 "+ (i+1) +" 页，共 "+ lenPage +" 页");    
} 
```

**说明：**

1. 本demo应用诸多CSS3属性，部分HTML特性，以及高级点的JavaScript API, 因此，一些老的浏览器显然是不支持的，应用在对外的实际项目是不切实际的。不过，用来做个演示文档，或是分享展示工具之类，还是很OK的！
2. demo页面的宽高按照标准纸张的`21:29.7`的比例制定，因此，所有单位值都是使用的`vh`单位。
3. 本demo `<page>`元素还可以设置`float:left`或`inline-block`两端/居中对齐等，让一屏的水平方向显示多个page页面，就如实际的office word一样。

### 八、场景之：水平时间轴

水平方向上的流体布局，正在琢磨折腾中，有不少技术难点，稍等几天……

<figure class="wp-block-image">![水平时间轴上的水平布局实现 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201209/2012-09-24_002633.png)</figure>下图为demo雏形截图，其中，左上角第一个已经成型的垂直布局显然要调整成水平方向型的，具体如何操作，请等我再好好想想，您也可以一同思考！

可以实现等比例图形；

配合font-size可以实现基于`vw`的自动缩放式网页布局；

### 九、结语

视区相关单位除了文章多次提到的`vw`, `vh`，还有个`vmin`(vm – 据说有的浏览器font-size: vm支持)，表示视区宽度或高度较小的那个。由于我实在想不出可以使用`vmin`的场景，因此，未具体介绍。  
个人觉的很重要，特别是移动平台，可以实现Orientation后内容自动auto-fit的效果，如iBook阅读PDF。