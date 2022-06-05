---
id: 730
title: min-aspect-ratio和max-aspect-ratio宽高比自适应
date: '2019-08-22T21:48:33+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/674-revision-v1/'
permalink: '/?p=730'
---

首先，移动端的适配，还是要先做好的，不管你是使用`rem`布局，还是使用`media`进行适配布局（只是单纯的宽度上），布局好了，这里只是宽度自适应了，如果是流式布局的话，这样就已经足够了，对于我们这里的单屏布局，就略显不足了。

所以这里就要用的：`device-aspect-ratio`和`aspect-ratio`了。

一个一个的来说吧。

##  device-aspect-ratio

> device-aspect-ratio 定义输出设备的屏幕可见宽度与高度的比率。

前缀是`device`，也能证明，这个属性是按照设备的尺寸来的，为什么单独说这个呢，因为不管是浏览器还是`APP`，所有的承载H5页面的容器，都有自己的`header`头以及最上面的手机时间功能显示的区域，一般这些内容，会占去`120px`（双倍屏上，这个数据可能不准，只是想说，会被这些占用设备可视区域的可用高度，如果你是在浏览器全屏的话，就当我没说这些啦）的高度。

所以，对于前端的开发者来开发单屏的页面的话，这个属性其实是不怎么会用到的。

同时，`device-aspect-ratio`还有两位两个兄弟属性，`max-device-aspect-ratio`和`min-device-aspect-ratio`，他们的兼容性在移动端，是可以不用去考虑的，绝大部分的移动端设备，都是支持的，在我看来，是可以直接放心使用的。

##  aspect-ratio

> aspect-ratio 定义输出设备中的页面可见区域宽度与高度的比率

可视区域，这个对于我们来说，才是真正需要的。

直接就是上代码吧：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@media screen and (min-aspect-ratio: 9/16) {
    // 如果宽高比小于9:16的话，显示这个内容
 }

@media screen and (max-aspect-ratio: 9/16) {
    // 如果宽高比大于9:16的话，显示这个内容
 }

@media screen and (aspect-ratio: 9/16) {
    // 如果宽高比是9:16的话，显示这个内容
}
```

`aspect-ratio`的取值：`width/height`，即宽度与高度的对比

一般情况下，在`PC`端的是：`width > height`，在移动端的时候，`width &lt; height`，所以，其实如何在移动端判断是否为横屏，也可以按照这个方式来判断。 如果直接设置：`aspect-ratio:9/16`，那么就表示，只要在`width/height = 9/16`的情况下，才会被触发，但是同一个手机，在APP和浏览器（甚至不同的浏览器），这个宽高比是不同的，如果单纯的设置一个固定的值，还是很难稳定的实现某些功能的，所以也就提供了：`max-aspect-ratio`和`min-aspect-ratio`。 `max-aspect-ratio : 9/16` 表示如果当前页面的`width/height &lt;= 9/16`的话会被执行，`max`表现大于的时候，与`max-width`（表示最大的限制值）理论上是一样的概念，宽高比不大于这个值的时候。 同样的道理，`min-aspect-ratio : 9/16`表示，当宽高比大于等于这个值的时候，就会被执行。

> 这里有一点要注意，只要设置了max-aspect-ratio或者min-aspect-ratio，那么aspect-ratio就无效了，因为max-aspect-ratio或者min-aspect-ratio真实的表现是“小于等于”和“大于等于”。

所以，如果你只是要监听一个比例的变化，而且，你需要使用到：`max-aspect-ratio`，`min-aspect-ratio`，`aspect-ratio`这三个属性的话，那么就要按照本小节的示例代码，把`aspect-ratio`写在最后面。

同样的，如果我们想要使用`min-aspect-ratio`来设置不同的尺寸的，那么也要注意一点，要把大比例的写在后面，就比如：`1/1`，`3/4`，`9/16`这三个比例的话，代码的实现部分就要：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@media screen and (min-aspect-ratio: 9/16) {
    // 只要宽高比大于等于9/16，就会执行
}

@media screen and (min-aspect-ratio: 3/4) {
    // 只要宽高比大于等于3/4，就会执行
}

@media screen and (min-aspect-ratio: 1/1) {
    // 只要宽高比大于等于1/1，就会执行
}
```

既然`min-aspect-ratio`的区域是大于等于，当前的设备比例已经固定（假设为cur），我需要设置三个比例A(3) &gt; B(2) &gt; C(1)。再加上CSS的层叠覆盖理论，后面定义的会覆盖前面的。

那么就会出现：如果cur &gt; C，那么cur &gt; B ,cur &gt; A ,这个时候，如果C的CSS放在了最后面，那么无聊如何也不会执行到A和B了。

所以，要把大值（涵盖区域广的）放到最底部，这个也可也称之为小区域理论。

3 – 正无穷 2 – 正无穷 1 – 正无穷

虽然在数学上来说，这个差距微乎其微，但是真实的情况确实是，CSS文件中，定义的顺序为： C ，B，A，也就是我们前面的一段代码的定义顺序。（这个如果真的理解不了，其实也可以不理解，写好了，试一下就OK了啊~）（再加一个理解方法，取属性的前缀min，越小的越先定义）

但同时也有一点注意，如果需要使用多次`min-aspect-ratio`的话，那就不要再使用`max-aspect-ratio`了，肯定会冲突的。

同理，max-aspect-ratio的情况也是相同的，只是定义的顺序与min-aspect-ratio相反，max-aspect-ratio的定义顺序是，max前缀，越大的值越先定义，所以如果上述的一段代码使用max-aspect-ratio来实现的话就是：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@media screen and (max-aspect-ratio: 1/1) {
    // 只要宽高比小于等于1/1，就会执行
}

@media screen and (max-aspect-ratio: 3/4) {
    // 只要宽高比小于等于3/4，就会执行
}

@media screen and (max-aspect-ratio: 9/16) {
    // 只要宽高比小于等于9/16，就会执行
}
```