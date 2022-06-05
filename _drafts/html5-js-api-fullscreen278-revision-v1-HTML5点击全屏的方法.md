---
id: 279
title: HTML5点击全屏的方法
date: '2019-08-16T19:58:37+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/278-revision-v1/'
permalink: '/?p=279'
---

### 一、人人网上……

<figure class="wp-block-image">![人人网上新出现的图片处理图标 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201210/2012-10-08_194122.png)</figure>今天，在人人网上看同学晒得照片的时候，发现了这个按钮：

这个按钮是？我移了好几遍，没有`title`提示。图形也不具有代表性，一个圈圈意思是？——圈人？？是不是啊？？不敢点，点了不知道会发生什么，浏览器会不会关掉！！我思想斗争做了很久，终于弱弱地点了一下~~

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/emtion/sweat.png)</figure><figure class="wp-block-image">![全屏显示的提示 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201210/2012-10-08_195501.png)</figure>草骆驼，居然是——全屏显示的提示：

人人兄，使用新技术固然值得提倡，这个圈圈也不加个提示——让人知道点了之后是全屏啊！！否则，俺们脆弱的小心脏根本承受不起啊——屏幕立马黑了个通透——还以为盖茨发现我的系统是盗版的黑了我的屏呢！！！

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/emtion/surprise.png)</figure>好吧，玩完两把三国杀，情绪稳定了，回到正题。人人网这个全屏效果就是使用的HTML5的全屏API，使用其实很easy的！对于全屏API，年初的时候就有耳闻，但未做测试与了解，现在，人人君已经用在实际项目中了，显然，有必要了解一番，可以在自己的项目中耍耍酷，过过HTML5的瘾。

### 二、相关文章以及一些技术点

搜了下，介绍的文章还不少，您可以参考：

- 武方博原创的[html5实现全屏的api方法](http://www.wufangbo.com/html5-quanping/)一文。文章前面属于备忘性质的内容，没看头；不过，最后的心得很不错，实际应用经验，值得一看。
- heero翻译的“[\[译\]原生全屏Javascript API](http://heeroluo.net/Article/Detail/97)”。去年时候内容，稍微old的点，多理论，有值得注意的知识点，能了解API的大概。
- sitePoint上的”[How to Use the HTML5 Full-Screen API](http://www.sitepoint.com/html5-full-screen-api/)“, 较新的文章吗，跟实际应用走的也很近，有demo，更值得参考。

如果您正好处于节后综合症发病期，懒得点击上面链接，也可以看下面我精简出来的一些实用的东西：

1. 全屏效果JavaScript实现
2. 全屏效果`click`, `mousedwon`, `mouseup`事件才能触发
3. 全屏方法为：`全屏元素.requestFullScreen()`; 目前不同的浏览器需要不同的前缀，如`mozRequestFullScreen`或`webkitRequestFullScreen`
4. 取消全屏为：`全屏元素.cancelFullScreen()`; 同样，不同浏览器需要添加私有前缀，如`mozCancelFullScreen`或`webkitCancelFullScreen`.
5. 可以通过`document.fullScreen`判断浏览器是否处于全屏状态。注意：FireFox和Chrome写法上有不同，Webkit内核浏览器需要增加个`is`, 如`document.webkitIsFullScreen`; 而Firefox则是`document.mozFullScreen`.
6. `:full-screen{}`用在CSS代码中，可以控制全屏元素（及其子元素）全屏状态时的样式。同样，不同浏览器不同前缀。如`:-moz-full-screen{}`或`:- webkit-full-screen{}`。**更新于2019-05-05**现在该伪类已经变成`:fullscreen`，没有短横线，也无需私有前缀。如果想要设置默认黑色背景层的样式，可以使用伪元素`::backdrop`。
7. 一个页面如果有多个全屏元素，在CSS控制的时候，可以使用类似`#element:full-screen{}`的选择器分别控制。

如果以上密密麻麻的文字看得你头大眼花，换个轻松的浏览，您可以狠狠地点击这里：[HTML5 full-screen全屏API测试demo](http://www.zhangxinxu.com/study/201210/html5-full-screen-api.html)

点击demo页面中的美女照片，即可触发全屏浏览提示。

该demo几乎均遵循了上面概要的一些技术tips, 完整代码demo页面上均有，不难懂的，大家有兴趣可以看看。

如果您想在实际项目中使用HTML5全屏API，demo页面中出现的这个方法必不可少（可以兼容以后N多年的HTML5发展）：

```
<pre class="wp-block-preformatted">var runPrefixMethod = function(element, method) {
    var usablePrefixMethod;
    ["webkit", "moz", "ms", "o", ""].forEach(function(prefix) {
        if (usablePrefixMethod) return;
        if (prefix === "") {
            // 无前缀，方法首字母小写
            method = method.slice(0,1).toLowerCase() + method.slice(1);
        }
        
        var typePrefixMethod = typeof element[prefix + method];
        if (typePrefixMethod + "" !== "undefined") {
            if (typePrefixMethod === "function") {
                usablePrefixMethod = element[prefix + method]();
            } else {
                usablePrefixMethod = element[prefix + method];
            }
        }
    });
    
    return usablePrefixMethod;
};
```

既是方法执行（如果是方法），又是属性判断（是否支持属性），很实用，可以直接拷贝粘贴使用。

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/emtion/smile.png)</figure>OK，这里所说的东西，其实上面几篇参考文章都有说过，没意思！现在目光转移到下面，呼吸一些新鲜的空气~~

### 三、FireFox/Chrome中的显示差异

**表象的差异：**  
表象的差异就是是否支持全屏提示的差异了，FireFox浏览器以前是个大框框，现在UI和则简约的多：

<figure class="wp-block-image">![Firefox下全屏效果](https://image.zhangxinxu.com/image/blog/201801/2018-01-02_020902.png)</figure><figure class="wp-block-image">![Chrome浏览器下是否允许全屏的提示](https://image.zhangxinxu.com/image/blog/201801/2018-01-02_020657.png)</figure>而Chrome浏览器下是个实色背景（参见下截图）：

浏览器生产商的喜好，没什么好说的。

<figure class="wp-block-image">![FireFox浏览器下图片外部的鼠标手形 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201210/2012-10-08_205529.png)</figure><figure class="wp-block-image">![Chrome浏览器图片外部的鼠标手形截图 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201210/2012-10-08_205636.png)</figure>**更细致的差异：**  
如果您观察够仔细，您可能会发现，在全屏状态下，FireFox浏览器下点击界面的任意位置，都会回到正常状态；但是Chrome浏览器下，只有点击图片才能回到正常状态。下面两张图是同一位置，鼠标手形的差异对比：

**深层次的原因：**  
虽然，demo页面中，背景都是全屏的黑色，图片效果一样，高度60%放大显示，垂直且水平居中，但是，其背后实现的机制却是很不一样的。

<figure class="wp-block-image">![FireFox浏览器中默认对全屏元素的CSS设置 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201210/2012-10-08_210718.png)</figure>FireFox浏览器对全屏元素进行的一些CSS属性的强设置，打开FireBug，查看系统默认样式，您会看到：

也就是，元素宽高100%显示，黑色背景，固定定位——这也是为什么FireFox浏览器下点击屏幕任意位置会退出全屏——因为绑定了事件的全屏元素满屏显示了。

背景色非强设置，通过如下CSS，我们就可以进行修改：

```
<pre class="wp-block-preformatted">:-moz-full-screen {
    background-color: #fff;
}
```

结果，就是下图这个样子，黑色背景变成白色背景了：

<figure class="wp-block-image">![FireFox浏览器下全屏背景白色的显示](https://image.zhangxinxu.com/image/blog/201801/2018-01-02_021218.png)</figure><figure class="wp-block-image">![Chrome浏览器下全屏元素的默认CSS设置 张鑫旭-鑫空间-鑫生活](http://image.zhangxinxu.com/image/blog/201210/2012-10-08_211425.png)</figure>而Chrome浏览器下，虽然默认对全屏元素也有CSS设置，但是寥寥：

好吧，基本上就是两个酱油CSS设置，背景色还是白色的。

现在问题来了？为何两个浏览器默认的全屏CSS样式不一样，但是，都是黑色背景，图片垂直水平居中呢！！！

**解释：**FireFox浏览器下的黑色背景就是全屏元素，其中图片的居中对齐是通过CSS控制的（:after伪类生成元素+vertical-align:middle实现），例如去掉`text-align:center`图片就不水平居中了；而Chrome的黑色背景属于系统的东西，其全屏元素似乎被浏览器劫持，默认状况下永远屏幕居中显示（去掉`text-align:center`图片依然水平居中）——脱离了常规CSS理解——如默认的`display:block`状态似乎变成了`display:inline-block`等。

然而，我们是有办法让Chrome表现得跟FireFox浏览器一样地，就是增加一样FireFox浏览器默认有的样式，如：

```
<pre class="wp-block-preformatted">:-webkit-full-screen {
    background-color: black;
    position: fixed;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;	
}
```

于是，这样子，即使在Chrome浏览器下，点击屏幕的任意位置，全屏状态都会退出！