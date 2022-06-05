---
id: 373
title: 'clearfix为什么用display:table,而不用display:block'
date: '2019-08-17T12:24:27+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/372-revision-v1/'
permalink: '/?p=373'
---

我们都知道clearfix一般这么写：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="css" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.clearfix:before,.clearfix:after{
    content:"";
    display:table;
}
.clearfix:after{clear:both;}
```

但是为嘛用 display：table哪？ 我用display:block好像也是显示很正常，也能清除浮动。

曾经也搜索过这个问题，感觉都讲得糊里糊涂的还是我水平太低了，反正我是看不懂他们想要表达啥意思，也不给例子，好了，废话不多说， 看结果吧：

See the Pen [Clearfix](https://codepen.io/wenjie/pen/mwgVWY/) by wenjie ([@wenjie](https://codepen.io/wenjie)) on [CodePen](https://codepen.io/).

网上讲得一堆什么使父容器形成BFC，什么防止margin塌陷。但是不给例子，我怎么搞都不会塌陷。 我本来是蓝色那一块那样测试的，看到没，我用：block，margin很正常, 并没有塌陷啊？ 后来才发现，其实margin要在一个 没有浮动的标签上，才会发生塌陷。所以 display:table就是为了解决这个 问题的。

两个都浮动的时候，并不会发生塌陷，不管你怎么测都测不出来 用block代替table有何问题。

还有为什么 要写：before呢？我去掉也是正常清除浮动啊， 一样的，也是防止margin-top的塌陷。也是要在没有浮动的标签上的时候才能测到。

其实这个对于塌陷这个词 我是存在疑问的。它并没有塌陷，它的margin还是确确实实存在的啊，只是针对外面的容器的margin了。 塌陷的意思 不就是不存在或者变小了吗？

最新浏览器（我用的是FF54测试），已经可以直接用  **display: flow-root;** 来清除浮动了，其效果 跟我们用的 display:table一致。