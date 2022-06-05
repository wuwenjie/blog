---
id: 476
title: css背景图background-position百分比的理解
date: '2019-08-17T18:50:46+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/475-revision-v1/'
permalink: '/?p=476'
---

`<position>`值支持1~4个值，可以是具体数值，也可以是百分比，也可以是`left`, `top`, `right`, `center`, `bottom`关键字。可参考下图经典示意：

<figure class="wp-block-image">![position类型图片示意](https://image.zhangxinxu.com/image/blog/201503/position_type.png)</figure>如果偏移没有相应的关键字，则会被认为是`center`, 因此，`background-position:top center`可以直接写作`background-position:top`。

如果同时出现3个值或4个值，则表示可以指定偏移的相对位置，这是CSS3新特性，现代浏览器下才支持。包括：Chrome 25+, Firefox 13+, IE 9+, Opera 10.5+, Safari/iOS 7+ .

例如：

```
<pre class="wp-block-preformatted">background-position: right 40px bottom 20px;
```

<figure class="wp-block-image">![IE9下background-position 4值效果截图](https://image.zhangxinxu.com/image/blog/201503/2015-03-16_003327.png)</figure>哇哦，IE9都支持，应该可以愉快地使用了吧！抱歉，我要泼点冷水。貌似Android的支持有些糟糕，据说：Android 4.4才支持，Android 4.0~4.2都不支持。因此，我们在实现此类效果的时候，可能还需要留一手，如下，使用`calc`计算：

```
<pre class="wp-block-preformatted">background-position: calc(100% - 40px) calc(100% -20px);
```

<figure class="wp-block-image">![IE9 background-position calc奔溃](https://image.zhangxinxu.com/image/blog/201503/2015-03-16_003717.png)</figure>使用`calc`计算总体来说兼容性好一点，但是，IE9浏览器下，根据caniuse的说法，会让IE9浏览器奔溃。

因此，实际使用还需要根据场景分别或hack处理。

**百分比单位**  
`background-position`中的百分比单位是个很有意思的东西。其表现与CSS中其他的百分比单位表现都不一样。

例如，一个图片：

```
<pre class="wp-block-preformatted">img { position: absolute; left: 100%; }
```

一定是在父容器外部。但是，在`<position>`值中，却是不一样的表现。

<figure><iframe allowfullscreen="true" src="https://www.zhangxinxu.com/study/201503/position-percent.html" width="100%"></iframe></figure>我们可以看下这个iframe示意：

其中，上面的妹子是`background-position`百分比，下面的50%透明的妹子是`left`百分比，可以看出两者的定位差异。

这就是为何`background-position:100% 100%`是定位在容器右下角的原因。

那`<position>`值对应的容器坐标位置该如何计算呢？

实际上是有一个公式的：

```
<pre class="wp-block-preformatted">positionX = (容器的宽度-图片的宽度) * percentX;
positionY = (容器的高度-图片的高度) * percentY;
```

因此，当`background-position:100% 100%`时候，实际定位值就是容器尺寸和图片尺寸的差异，于是，就有了右下角定位效果。

有个这个公式，我们也能理解百分比负值的一些表现了，比方说你觉得下面两行CSS对应图片的表现是？

```
<pre class="wp-block-preformatted">background-position: -50% -50%;

object-position: -50% -50%
```

<figure class="wp-block-image">![IE9下background-position 4值效果截图](https://image.zhangxinxu.com/image/blog/201503/2015-03-16_003327.png)</figure>是不是觉得应该是定位在容器的左上角，同时有部分图片超出容器左上角隐藏不可见，就类似上面这个截图：

<figure class="wp-block-image">![负值百分比定位](https://image.zhangxinxu.com/image/blog/201503/2015-03-16_010003.png)</figure>接近于下面CSS的效果：

```
<pre class="wp-block-preformatted">background-position: 40px 10px;
```

深受传统百分比定位迷惑的我们可能一时间会想不通，明明是个负值百分比定位，怎么会是一个正值效果呢？这不科学啊！

但是，我们套用`<position>`百分比值计算公式，就豁然开朗了！

因为图片的尺寸大于容器尺寸，所以：

- (容器的宽度-图片的宽度) \* -50% 的结果是个**正值**；
- (容器的高度-图片的高度) \* -50% 的结果也是个**正值**;

所以…… 恩！相信大家都应该懂了！