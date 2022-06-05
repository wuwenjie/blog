---
id: 598
title: 'CSS3 animation steps介绍'
date: '2018-07-03T18:07:06+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=598'
permalink: /2018/07/css3-animation-steps
categories:
    - 'HTML&amp;CSS'
---

`steps()`有一定的学习难度，总是搞不清楚，最主要就是`start`和`end`傻傻分不清楚。我这里自我挑战下，看看能不能说清楚。

常见`steps()`用法举例：

```
<pre class="wp-block-preformatted">steps(5, end);
steps(2, start);
```

用`steps()`语法表示就是：

```
<pre class="wp-block-preformatted">steps(number, position)
```

其中：**number**数值。这个很好理解，表示把我们的动画分成了多少段。

假设有如下CSS3动画`keyframes`，定义了一段从`0~100px`的位移：

```
<pre class="wp-block-preformatted">@keyframes move {
    0% { left: 0; }
    100% { left: 100px; }
}
```

假设我们的`number`值是5，则相当于把这段移动距离分成了5段，如下示意图：

<figure class="wp-block-image">![steps分段示意图](https://image.zhangxinxu.com/image/blog/201806/2018-06-11_214140.png)</figure>**position**关键字。表示动画是从时间段的开头连续还是末尾连续。支持`start`和`end`两个关键字，含义分别如下：

- `start`：表示直接开始。
- `end`：表示戛然而止。是默认值。

#### 为什么position非常难理解？

我认为两个原因：

1. `steps()`属于timing function，也就是时间函数，时间这个东西是虚的，看不见，摸不着，联想乏力，所以认知成本高。这也是为什么那么多人都不珍惜时间的原因——无法感知。
2. CSS规范中对于`start`和`end`的定义是基于数学函数来的，函数这东西，多少人的噩梦，因为过于抽象，与现实难以关联，所以，如果我们盯着定义去理解`start`和`end`，那就是死胡同，不归路，就算现在弄懂了，过段时间再遇到，得了，全忘光光了，函数图哪个是哪个，鬼才记得。下面这张图就出自[规范文档](https://www.w3.org/TR/css-timing-1/#step-timing-functions)：按照规范图再细化解释就是：
    - `start`：表示直接开始。也就是时间才开始，就已经执行了一个距离段。于是，动画执行的5个分段点是下面这5个，起始点被忽略，因为时间一开始直接就到了第二个点：
    - `end`：表示戛然而止。也就是时间一结束，当前距离位移就停止。于是，动画执行的5个分段点是下面这5个，结束点被忽略，因为等要执行结束点的时候已经没时间了：

#### 基于现实感知重新理解position！

万物具有相对性。例如，苍蝇眼中的人类动作都是慢动作，但是人类眼中的苍蝇却非常敏捷。

同样的，`start`和`end`这里的开始和结束是相对于时间而言的，但是，如果站在人类可感知的具体事物而言，`start`和`end`却是相反的含义。

所以，我们可以这么理解：

- `start`：表示结束。分段结束的时候，时间才开始走。于是，动画执行的5个分段点是后5个点：
- `end`：表示开始。分段开始的时候，时间跟着一起走。于是，动画执行的5个分段点是前5个点：

#### 记住position参数的含义

牢记这么一句话：**一切都是反的！start不是开始，而是结束；end不是结束，而是开始。**

### step-start和step-end

`step-start`和`step-end`是`steps()`功能符简化关键字，注意，是`step-*`，`step`，后面没有`s`。

其中，`step-start`等同于`steps(1, start)`，`step-end`等同于`steps(1, end)`或者`steps(1)`。

`step-start`和`step-end`用中文短句解读就是：一步到位和延迟到位，在实际项目中有什么作用呢？

对于只有`0%,100%`或`from, to`两个关键时间帧的动画，`step-start`和`step-end`是没有任何需要使用的理由的。

如果是非等分，无法过渡的阶梯动画，则有使用价值，例如下面这个基于`box-shadow`实现的打点动画效果：

订单提交中

实现核心代码如下（此打点方法最多排纯CSS打点动画第3位，更多见[此文](http://www.zhangxinxu.com/wordpress/2014/12/css3-animation-dotting-loading/)介绍和指引）：

```
<pre class="wp-block-preformatted"><button>订单提交中<span class="dotting"></span></button>
```

```
<pre class="wp-block-preformatted">.dotting {
    display: inline-block; min-width: 2px; min-height: 2px;
    margin-right: 8px;
    box-shadow: 2px 0, 6px 0, 10px 0;
    animation: dot 4s infinite step-start both;
}
@keyframes dot {
    25% { box-shadow: none; }          /* 0个点 */
    50% { box-shadow: 2px 0; }         /* 1个点 */
    75% { box-shadow: 2px 0, 6px 0; }  /* 2个点 */ 
}
```

### steps()与填充模式animation-fill-mode

`animation-fill-mode`有时候也会影响`steps()`的断点表现，例如下面这个语句：

```
<pre class="wp-block-preformatted">animation: move 5s forwards steps(5, end);
```

动画只执行一次，因为没有设置`infinite`无限循环，而`forwards`虽然表示“前”，但同样和现实表现是反的，也就是动画结束时候元素保持动画关键帧最后的状态。于是，下面6个分段点都会执行，整个动画停止在第6个分段点上。

<figure class="wp-block-image">![forwards执行最后一个分段点](https://image.zhangxinxu.com/image/blog/201806/2018-06-11_233301.png)</figure>这显然不是我们想要的，怎么处理呢？

可以消减分段个数和动画运动的跨度，调整如下：

```
<pre class="wp-block-preformatted">@keyframes move {
    0% { left: 0; }
    100% { left: 80px; }
}
```

也就是原来终点`100px`改成`80px`，同时CSS调用改成：

```
<pre class="wp-block-preformatted">animation: move 5s forwards steps(4, end);
```

也就是原来`steps(5, end)`改成`steps(4, end)`，最后`100%`这一帧交给`forwards`即可！