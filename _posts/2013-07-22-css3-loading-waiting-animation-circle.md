---
id: 336
title: CSS3实现loading效果转圈圈
date: '2013-07-22T23:18:11+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=336'
permalink: /2013/07/css3-loading-waiting-animation-circle
categories:
    - 'HTML&amp;CSS'
---

### CSS3实现之box-shadow绘制

<figure class="wp-block-image">![放大后的效果图](https://image.zhangxinxu.com/image/blog/201305/2013-05-29_104052.png)</figure>8个点，有3个大小有规律变大，这样旋转的时候就有方向感和真实性。

8个点实际对应于`box-shadow`的8个投影，图片大小标准`32px*32px`，考虑到点的八卦布局以及大小限制，因此，我们的容器本身只有`3px*3px`，于是，有如下的实现代码：

```
<pre class="wp-block-preformatted">.loading {
    width: 3px; height:3px;
    border-radius: 100%;                      /* 圆角 */
    box-shadow: 0 -10px 0 1px #333,           /* 上, 1px 扩展 */
                10px 0px #333,                /* 右 */    
                0 10px #333,                  /* 下 */
                -10px 0 #333,                 /* 左 */
                              
                -7px -7px 0 .5px #333,        /* 左上, 0.5px扩展 */
                7px -7px 0 1.5px #333,        /* 右上, 1.5px扩展 */                    
                7px 7px #333,                 /* 右下 */
                -7px 7px #333;                /* 左下 */
}
```

如果您的浏览器IE9+或Chrome一伙，同时不是在RSS中阅读本文，就会看到上面代码的效果，就是下面这个静止状态的loading圈圈效果：

CSS3 `box-shadow`有四个值，分别表示“右偏移、下偏移、模糊大小、扩展大小”。因此，那3个稍大的圈圈就是利用了第4个参数——“扩展”来实现的。分别扩展`0.5px`, `1px`以及`1.5px`.

以上就是CSS3 `box-shadow`的绘制，相比一开始的炫炫的彩色效果，这个要通俗易懂的多，大家都能轻松把玩实现的效果，只要定好位置，一切都很简单。

一般开源的移动框架中，无限旋转的CSS都是使用`spin`作为类名以及动画关键字的，大家可以约定俗成的使用，无形中有利于前端大环境的建设。

CSS代码如下，目前在PC上，FireFox/IE10/Opera浏览器中animation动画都已经不需要私有前缀了（如果您不放心，可以自己加上），于是，如下CSS3代码：

```
<pre class="wp-block-preformatted">.spin {
    animation: spin 1s steps(8) infinite;
}
@keyframes spin {
    from {transform: rotate(0deg);}
      to {transform: rotate(360deg);}
}
```

HTML代码为：

```
<pre class="wp-block-preformatted"><div class="loading spin"></div>
```