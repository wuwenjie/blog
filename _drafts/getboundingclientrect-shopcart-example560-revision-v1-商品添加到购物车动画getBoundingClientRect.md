---
id: 561
title: 商品添加到购物车动画getBoundingClientRect
date: '2019-08-18T10:57:42+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/560-revision-v1/'
permalink: '/?p=561'
---

# 前言

之前用饿了么印象最深刻的是联动菜单和小球飞入购物车动画，所以想看看别人是怎么实现的，但是看了很多仿饿了么的demo都是实现了一个完整的大的项目，要找到那个小模块很麻烦，所以自己将联动菜单和动画提取出来写了一个demo，方便学习。

目的只是想突出功能所以界面细节很relax，大家也relax的看看吧~

# 效果图

<figure class="wp-block-image">![](https://user-gold-cdn.xitu.io/2018/4/27/163067422afcd228?imageslim)</figure># 实现功能

## 1. 联动菜单

### 1.1 用户点击左侧导航栏会跳转到相应的内容

这个很简单，给导航栏的每一个元素加一个点击事件，其实也可以通过a标签的锚点来实现

```
<pre class="wp-block-preformatted"><li v-for="(item, index) in navs" 
    :key="index" 
    :class="{click: selector==index}"
    @click="toHash(item, index)">
    {{item}}
</li>
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">// 点击右侧导航栏
toHash(item, index) {
    this.selector = index;
    window.location.hash = item; 
    
    // 导航栏向上滚动相应距离，一个li的高度为54px
    this.$refs.left.scrollTop = (index > 7 ? index-7 : 0)*54;
}
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>### 1.2 用户滑动右侧的内容左侧的导航栏会响应式改变

右侧内容监听一个scroll事件，当触发滑动事件的时候获取粘性定位在顶部的标题，根据标题使导航栏定位到相应的li

```
<pre class="wp-block-preformatted">var obj = element.getBoundingClientRect();
```

上述api返回一个对象obj，该对象有left、top等属性，可以根据该属性获得element元素在页面的位置

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>```
<pre class="wp-block-preformatted">listScroll() {
    // 为了达到联动效果，右侧滑动则改变左侧导航栏样式
    var titles = document.getElementsByClassName('goodTitle');
    for(var i = 0; i < titles.length; i++) {
        var style = titles[i].getBoundingClientRect();
        if(style.top == 107) {
            this.toHash(titles[i].innerHTML, i);
        }
    }
}
```

<figure class="wp-block-image">[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)</figure>### 1.3 标题栏粘性定位

```
<pre class="wp-block-preformatted">#el {
    position: sticky;
    top: 0;
}
```

该元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。这也实现了内容区标题栏始终在顶部的效果。关于粘性定位更多的可以[看这里](https://link.juejin.im/?target=https%3A%2F%2Fwww.cnblogs.com%2Fcoco1s%2Fp%2F6402723.html)

## 2. 按钮缓慢弹出

当我们点击添加按钮的时候其他的内容会缓慢弹出，这个是靠css的动画实现的。

我们先将个数减少按钮和被选中物品个数num的left设为48px，使其被隐藏。点击添加按钮时选择物品个数大于0则让left变为0达到一个缓慢弹出的动画效果。

```
<pre class="wp-block-preformatted"> <div :class="{pop: true, mov: item.num>0}">
    <!---->
 </div>
```

```
<pre class="wp-block-preformatted">.pop {
    display: inline-block;
    position: relative;
    left: 48px;
    opacity: 0;
    transition: all ease .5s;
}
.mov {
    left: 0;
    opacity: 1;
}
```

## 3.小球飞入购物车动画

先准备n个小球，为什么不是一个呢？因为如果用户连续点击添加可能会出现小球不够的情况，所以需要多个小球。

```
<pre class="wp-block-preformatted"><!-- 运动的小球 -->
<div id="points">
    <div class="pointOuter pointPre">
        <div class="point-inner"></div>
    </div>  
    <!--其他n个小球-->
</div>
```

令小球为绝对定位这样可以改变它的left和top。

动画实现思路：用户点击添加时将一个小球的位置设置为被点击元素的位置，且获取目的地位置（购物车位置），当小球抛出时使其运动方式按照贝塞尔曲线过渡。

```
<pre class="wp-block-preformatted">increase(index1, index2, event) { 
    
    // some code...
    
    // 小球动画 
    var top = event.clientY, // 小球降落起点
        left = event.clientX,
        endTop = window.innerHeight - 30,  // 小球降落终点
        endLeft = 20; 

    // // 小球到达起点并去掉小球的display: none;
    var outer = $('#points .pointPre').first().removeClass("pointPre").css({
        left: left + 'px',
        top: top + 'px'
    });
    var inner = outer.find(".point-inner"); 

    setTimeout(function() { 
        // 将jquery对象转换为DOM对象
        outer[0].style.webkitTransform = 'translate3d(0,' + (endTop - top) + 'px,0)';
        inner[0].style.webkitTransform = 'translate3d(' + (endLeft - left) + 'px,0,0)';
        
        // 小球运动完毕恢复到原点
        setTimeout(function() {
            outer.removeAttr("style").addClass("pointPre");
            inner.removeAttr("style");
        }, 1000);  //这里的延迟值和小球的运动时间相关
    }, 1); 
}
```

#### 注意点：

- 嵌套的setTimeout中的时间之所以设置为1s，是因为css中规定的小球运动时间为1s，所以在小球1s运动完以后会令它恢复到原来的位置，你想想，小球一共就只有那么几个，如果不恢复的话下次用户点击了小球就不够啊…

# 总结

以上是饿了么购物车模块主页面的几个主要技术点。上面的DOM操作可以改成使用vue的动画组件 transition 进行优化，感觉会更好，我在项目中使用了transition组件进行优化，代码更加简洁。