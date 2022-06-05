---
id: 484
title: 'CSS3 transform对元素的影响'
date: '2019-08-17T19:05:21+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/483-revision-v1/'
permalink: '/?p=484'
---

## transform提升元素的垂直地位

<figure class="wp-block-image">![示意妹子](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm2.jpg)</figure>我们可能都知道这样一个规则，当遭遇元素`margin`负值重叠的时候，如果没有static以外的position属性值的话，后面的元素是会覆盖前面的元素的。例如下面，后面的妹子覆盖了前面的妹子：

```
<pre class="wp-block-preformatted"><img src="mm1"><img src="mm2" style="margin-left:-60px;">
```

在`transform`出现之前，这个规则一直很稳健；但是，自从`transform`降临，这个规则就变了。元素应用了`transform`属性之后，就会变得应用了`position:relative`一个尿性，原本应该被覆盖的元素会雄起，变成覆盖其他元素，修改为如下代码：

```
<pre class="wp-block-preformatted"><img src="mm1" style="-ms-transform:scale(1);transform:scale(1);"><img src="mm2" style="margin-left:-60px;">
```

<figure class="wp-block-image">![示意妹子](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure><figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm2.jpg)</figure>只要是支持transform元素的浏览器，包括IE9(`-ms-`), 都会提高普通元素的垂直地位，使其覆盖其他元素而不是被覆盖。

### transform限制position:fixed的跟随效果

我们应该都知道，`position:fixed`可以让元素不跟随浏览器的滚动条滚动，而且这种跟随效果连它的兄弟们`position:relative/absolute`都限制不了。但是，真是一物降一物，`position:fixed`固定效果却被小小的`transform`给干掉了，直接降级变成`position:absolute`的蛋疼表现。

例如下面示意代码：

```
<pre class="wp-block-preformatted"><p style="transform:scale(1);"><img src="mm1.jpg"style="position:fixed;" /></p>
```

结果，本来应该不跟着滚动条滚动的傲娇`fixed`元素，变成`absolute`一样的行为表现，比方说下面这个妹子，其`position`属性值1000%是`fixed`，但是，却大失所望跟着滚动条混了，归根结底就是父元素加了个小小的`transform`属性值。

<figure class="wp-block-image">![示意妹子](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>可以看到，当页面滚动时候，只有中间的妹子被滚走了：

<figure class="wp-block-image">![transform限制fixed效果Demo截图](https://image.zhangxinxu.com/image/blog/201801/2018-01-08_234801.png)</figure>注意，这个特性表现，目前只在Chrome浏览器/FireFox浏览器下有，IE浏览器，包括IE11, `fixed`还是`fixed`的表现。

### transform限制absolute的100%宽度大小

以前，我们设置`absolute`元素宽度100%, 则都会参照第一个非`static`值的`position`祖先元素计算，没有就`window`. 现在，诸位，需要把`transform`也考虑在内了。

结果，无论是IE9+，还是Chrome还是FireFox浏览器，所有绝对定位图片`100%`宽度，都是相对设置了`transform`的容器计算了，于是，上面的图片拉长到了西伯利亚；下面的图片被限制成了小胖墩。

<figure class="wp-block-image">![transform对absolute宽度100%限制~](https://image.zhangxinxu.com/image/blog/201801/2018-01-08_235623.jpg)</figure>在mac safari浏览器下，使用`transform: translate3d(0,0,0)` 可以避免文字在其他元素`transition`或`animation`时候，粗细渲染跟着变化的问题。  
`transform`属性还会创建层叠上下文，影响元素发生重叠时候的表现。