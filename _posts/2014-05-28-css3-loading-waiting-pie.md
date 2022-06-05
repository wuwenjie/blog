---
id: 386
title: CSS3实现饼图扇形loading效果
date: '2014-05-28T13:03:32+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=386'
permalink: /2014/05/css3-loading-waiting-pie
categories:
    - 'HTML&amp;CSS'
---

##### 效果： 

<figure class="wp-block-image">![](https://www.zhangxinxu.com/study/201404/small-loading.gif)</figure>CSS代码：

```
<pre class="wp-block-preformatted">.wrap { width: 64px; height: 64px; position: relative; }
.outer { position: absolute; width: 100%; height: 100%; background: url(icon-spin-s.png) no-repeat; animation: spin 800ms infinite linear; }
.inner, .inner2 { position: absolute; width: 38px; height: 38px; border-radius: 40px; overflow: hidden; left: 13px; top: 13px; }
.inner { opacity: 1; background-color: #89abdd; animation: second-half-hide 1.6s steps(1, end) infinite; }
.inner2 { opacity: 0; background-color: #4b86db; animation: second-half-show 1.6s steps(1, end) infinite; }
.spiner, .filler, .masker { position: absolute; width: 50%; height: 100%; }
.spiner { border-radius: 40px 0 0 40px; background-color: #4b86db; transform-origin: right center; animation: spin 800ms infinite linear; left: 0; top: 0; }
.filler { border-radius: 0 40px 40px 0; background-color: #4b86db; animation: second-half-hide 800ms steps(1, end) infinite; left: 50%; top: 0; opacity: 1; }
.masker { border-radius: 40px 0 0 40px; background-color: #89abdd; animation: second-half-show 800ms steps(1, end) infinite; left: 0; top: 0; opacity: 0; }

.inner2 .spiner, .inner2 .filler { background-color: #89abdd; }
.inner2 .masker { background-color: #4b86db; }

@keyframes spin {
  0%   { transform: rotate(360deg); }
  100% { transform: rotate(0deg); }
}
@keyframes second-half-hide {
  0%        { opacity: 1; }
  50%, 100% { opacity: 0; }
}
@keyframes second-half-show {
  0%        { opacity: 0; }
  50%, 100% { opacity: 1; }
}
```

##### HTML代码：

```
<pre class="wp-block-preformatted"><div class="wrap">
    <div class="outer"></div>
    <div class="inner">
        <div class="spiner"></div>
        <div class="filler"></div>
        <div class="masker"></div>
    </div>
    <div class="inner2">
        <div class="spiner"></div>
        <div class="filler"></div>
        <div class="masker"></div>
    </div>
</div>
```

## 下图示意的就是鸡蛋饼上的鸡蛋从100%变小成0的过程。

<figure class="wp-block-image">![鸡蛋饼与蛋饼旋转效果](https://image.zhangxinxu.com/image/blog/201404/danbing-1.png)</figure>**1.** 这是没有干扰的蛋饼，你看到的是半个假饼和半个假蛋。

<figure class="wp-block-image">![鸡蛋饼与蛋饼旋转效果](https://image.zhangxinxu.com/image/blog/201404/danbing-2.png)</figure>**2.** 当我们煎饼动画转起的一瞬间，我们让假的饼子隐藏回家打酱油去。于是，从上面看，我们看到的就是满满一层的鸡蛋。

<figure class="wp-block-image">![鸡蛋饼与蛋饼旋转效果](https://image.zhangxinxu.com/image/blog/201404/danbing-3.png)</figure>**3.** 真鸡蛋转起，你会发现，半个真鸡蛋，由于逆时针旋转，露出了点空（左侧上部）。

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/blog/201404/2014-04-25_010442.png)</figure>demo对应效果类似(浅色看成鸡蛋)：

<figure class="wp-block-image">![鸡蛋饼与蛋饼旋转效果](https://image.zhangxinxu.com/image/blog/201404/danbing-4.png)</figure>**4.** 当真鸡蛋旋转了180度（半圈）的时候，真假鸡蛋正好重合在了一起，于是就是看到的就是蛋饼上半面鸡蛋。

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/blog/201404/2014-04-25_010930.png)</figure>demo对应效果类似：

<figure class="wp-block-image">![鸡蛋饼与蛋饼旋转效果](https://image.zhangxinxu.com/image/blog/201404/danbing-5.png)</figure>**5.** 此时，再继续旋转。我们让假鸡蛋隐藏，让之前打酱油的<del>加饼子</del>假饼子出现，覆盖部分真鸡蛋。我们就会看到，真鸡蛋面积越来越小了~

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/blog/201404/2014-04-25_011621.png)</figure>demo对应效果类似：

<figure class="wp-block-image">![鸡蛋饼与蛋饼旋转效果](https://image.zhangxinxu.com/image/blog/201404/danbing-6.png)</figure>**6.** 一直旋转到360度，其完全被假的饼子遮盖，一点鸡蛋都看不到了。完成了从全部都0的动画过程。这就是蛋饼转转转的基本原理。

**CSS3表示**  
可见，要实现我们想要的蛋饼效果，我们需要这些东西：

1. 圆形的蛋饼子 – 对应下面类名为`inner`元素
2. 旋转的半面真鸡蛋 – 对应下面类名为`spiner`的元素
3. 不动的半面蛋饼子，前半程隐藏，后半程出现 – 对应下面类名为`masker`的元素
4. 不动的半面假鸡蛋，前半程出现，后半程隐藏 – 对应下面类名为`filler`的元素

```
<pre class="wp-block-preformatted"><div class="inner">
    <div class="spiner"></div>
    <div class="filler"></div>
    <div class="masker"></div>
</div>
```

1. `inner`主要实现圆以及背景色；
2. `spiner`主要实现半圆的360度逆时针旋转，其背景色有别于父元素的背景色；
3. `filler`半圆，定位在右侧，与旋转元素同样背景色；后面的180度隐藏；
4. `masker`半圆，定位在左侧，与大背景色色值相同；旋转前180度隐藏，之后显示遮盖；

其中，`360`度旋转CSS代码如下：

```
<pre class="wp-block-preformatted">@keyframes spin {
  0%   { transform: rotate(360deg); }
  100% { transform: rotate(0deg); }
}
```

因为是逆时针，所以是从`360deg`到`0deg`.

前半程出现，后半程隐藏，可以借助`animation` `step`相关的`timing function`实现，代码如下：

```
<pre class="wp-block-preformatted">@keyframes second-half-hide {
  0%        { opacity: 1; }
  50%, 100% { opacity: 0; }
}
```

后半程显示则是：

```
<pre class="wp-block-preformatted">@keyframes second-half-show {
  0%        { opacity: 0; }
  50%, 100% { opacity: 1; }
}
```

于是，我们只要加个动画时间，以及无限执行就OK啦~~

```
<pre class="wp-block-preformatted">.spiner { transform-origin: right center; animation: spin .8s infinite linear; }
.filler { animation: second-half-hide .8s steps(1, end) infinite; }
.masker { animation: second-half-show .8s steps(1, end) infinite; }
```

其他细节都是定位什么的，很基础的知识，就不啰嗦啦~~

**饼其实还没有做好**  
啊，捣鼓了这么久还没有结束啊？

没错。仔细查看gif动画，你会发现，蛋饼它是从全盘都`0`再到整个`360`度覆盖的。

而，上午捣鼓的动画只是从`360`度无死角覆盖到`0`覆盖。一旦覆盖结束，就又要走360度开始，不连贯，怎么破？

我是这么处理的：  
再覆盖一个蛋饼从`0`度到`360`度展示的动画。与一直捣鼓的动画前后半程分别展示就可以了。

于是，最终有如下HTML:

```
<pre class="wp-block-preformatted"><div class="inner">
    <div class="spiner"></div>
    <div class="filler"></div>
    <div class="masker"></div>
</div>
<div class="inner2">
    <div class="spiner"></div>
    <div class="filler"></div>
    <div class="masker"></div>
</div>
```

`inner`和`inner2`也使用的前后半程隐藏的动画，动画时间正好是一个周期的`2`倍。

```
<pre class="wp-block-preformatted">.inner { opacity: 1; animation: second-half-hide 1.6s steps(1, end) infinite; }
.inner2 { opacity: 0; animation: second-half-show 1.6s steps(1, end) infinite; }
```