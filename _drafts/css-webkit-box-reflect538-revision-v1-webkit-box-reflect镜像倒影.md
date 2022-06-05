---
id: 539
title: '-webkit-box-reflect镜像倒影'
date: '2019-08-17T23:52:44+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/538-revision-v1/'
permalink: '/?p=539'
---

目前为止我们已经探讨了很多CSS3中的新功能和新特征。除了上面这些，实际上还有很多CSS新属性并未包含进CSS3官方标准中，像谷歌浏览器或火狐浏览器等都会利用CSS的浏览器引擎前缀(Vendor Prefix)来实现很多自定义的CSS功能。`-webkit-box-reflect`属性就是以谷歌浏览器为代表的Webkit渲染引擎独有的特征。`-webkit-box-reflect`的作用是让图片出现倒影。

### 实现倒影的基本语法

实现倒影的基本语法非常的直观，假设我们想给下面的图片增加倒影效果，可以这样写：

```
<pre class="wp-block-preformatted">img {
    -webkit-box-reflect: below;
}
```

<figure class="wp-block-image">![basic-reflection](https://www.html5tricks.com/wp-content/uploads/2014/05/04221de89182ee2be1134a363f4e8550.jpg)</figure>上面这个例子中倒影出现了图片的下方，但实际上我们也可以将倒影安置在左侧、右侧或上侧。

#### 倒影偏移量

Offset属性值用来定义图片和倒影影像之间的间距。参考下面的代码：

```
<pre class="wp-block-preformatted">img {
    -webkit-box-reflect: below 10px;
}
```

上面的代码中，我们使图片和倒影影像之间相聚`10px`；

<figure class="wp-block-image">![offset-reflection](https://www.html5tricks.com/wp-content/uploads/2014/05/9b503cbfe239d4a50e1f34ca3de89e11.jpg)</figure>### 给倒影增加消隐效果

在现实生活中，倒影的出现通常是上半部比较清晰，下面半部逐渐消隐。为了在CSS中实现这种效果，我们需要运用CSS3渐变色(Gradients)功能，就像下面这样：

```
<pre class="wp-block-preformatted">-webkit-box-reflect: below 0px -webkit-gradient(linear, left top, left bottom, from(transparent), to(rgba(250, 250, 250, 0.1)));
```

这段代码就能达到这样的效果：

<figure class="wp-block-image">![reflection](https://www.html5tricks.com/wp-content/uploads/2014/05/40f3468cd695958c102d657febf859cc.jpg)</figure>我们还可以使用`color-stop`来控制色彩过渡，让倒影更加漂亮：

```
<pre class="wp-block-preformatted">img {
	-webkit-box-reflect: below 0px -webkit-gradient(linear, left top, left bottom, from(transparent), color-stop(70%, transparent) , to(rgba(250, 250, 250, 0.1)));
}
```

<figure class="wp-block-image">![colorstop-reflection](https://www.html5tricks.com/wp-content/uploads/2014/05/8306d8d0fbcc3bc4c1a6a1725339b3d8.jpg)</figure>### 火狐浏览器中倒影的实现

目前只有Webkit浏览器(谷歌浏览器和Safari浏览器)实现`box-reflect`属性。为了在火狐浏览器中也实现倒影功能，我们需要寻找另外的途径：使用`-moz-element()`方法。这个方法能够复制指定网页元素的内容。让我们来看看下面的例子：

我们把图片包裹着一个ID是`someid`的`<div>`里。

并且，为了存放倒影影像，我们将使用`:before`伪元素，就像下面：

```
<pre class="wp-block-preformatted">#someid {
   position: relative;
   /* 给倒影留下空间 */
   margin-bottom: 120px;
}

#someid:before {
   content:""; /* needed or nothing will be shown */

   background: -moz-linear-gradient(top, white, white 30%, rgba(255,255,255,0.9) 65%, rgba(255,255,255,0.7)) 0px 0px, 
               -moz-element(#someid) 0px -127px no-repeat;

   -moz-transform: scaleY(-1); /* flip the image vertically */
   position:relative;
   height:140px;
   width: 360px; /* 需要 > image width + margin + shadow */
   top: 247px;
   left:0px;
}
```

这里的`-moz-transform`是一个负值，作用就是让复制过来的图形上下颠倒，达到倒影的效果。为了让`:before`伪元素跟原始图形相配合，我们需要移动它的位置。这里的背景偏移量 (-127px)是`:before`伪元素高(140px) – (图片的高 (247px) + div的border (20px))。需要注意的是，火狐浏览器版的倒影实现只能用在页面的背景是真实背景。背景色要和`:before`伪元素使用的渐变色的颜色一致。

因为所有的属性都是来实现倒影的，而且这些属性都有火狐浏览器独有的前缀，和Webkit的倒影不冲突，所以在代码在可以把两个版本倒影方法都写上，保证两种浏览器里都有效果。

# 视频倒影

官方文档中说当倒影的实体内容变化时，倒影的内容也会相应更新。因此，这种技术用在视频是有特殊的效果。

# CSS倒影技术在火狐浏览器中的小问题

有时，火狐浏览器生成的渐变色图形会比背景元素稍微小一些，导致有一些间隙线出现。为了避免这种情况出现，在`:before`伪元素的顶部和底部加`1px`的padding，并且分别设置背景的裁剪方式和原点：

```
<pre class="wp-block-preformatted">padding: 1px 0px;
background-origin: border-box, content-box;
background-clip: border-box, content-box;
```