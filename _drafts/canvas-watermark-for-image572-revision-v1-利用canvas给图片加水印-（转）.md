---
id: 573
title: '利用canvas给图片加水印 （转）'
date: '2019-08-18T15:02:30+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/572-revision-v1/'
permalink: '/?p=573'
---

### 一、使用canvas在前端实现图片水印合成

如果仅仅是普通的合成，例如一个底图和一个PNG水印图片合成，直接使用`canvas`的`drawImage()`方法即可，语法如下：

```
<pre class="wp-block-preformatted">context.drawImage(img,x,y);
context.drawImage(img,x,y,width,height);
context.drawImage(img,sx,sy,swidth,sheight,x,y,width,height);
```

各个参数示意为：

| 参数 | 描述 |
|---|---|
| *img* | 用来被绘制的图像、画布或视频。 |
| *sx* | 可选。img被绘制区域的起始左上x坐标。 |
| *sy* | 可选。img被绘制区域的起始左上y坐标。 |
| *swidth* | 可选。img被绘制区域的宽度（如果没有后面的width或height参数，则可以伸展或缩小图像）。 |
| *sheight* | 可选。img被绘制区域的高度（如果没有后面的width或height参数，则可以伸展或缩小图像）。 |
| *x* | 画布上放置img的起始x坐标。 |
| *y* | 画布上放置img的起始y坐标。 |
| *width* | 可选。画布上放置img提供的宽度（可能会有图片剪裁效果）。 |
| *height* | 可选。画布上放置img提供的高度（可能会有图片剪裁效果）。 |

而PNG水印图片的合成，直接连续在使用`drawImage()`把对应的图片绘制到`canvas`画布上就可以，原理就是这么简单。

我们直接案例吧，您可以狠狠地点击这里：[JS canvas水印图片合成demo](http://www.zhangxinxu.com/study/201705/js-canvas-image-watermark-synthesis.html)

demo页面的功能如下：

1. 选择自己电脑中的图片；
2. 合成；

例如，我们点击“选择图片”按钮，如下截图：

<figure class="wp-block-image">![选择图片](https://image.zhangxinxu.com/image/blog/201705/2017-05-17_001002.png)</figure><figure class="wp-block-image">![图片选择示意](https://image.zhangxinxu.com/image/blog/201705/2017-05-17_001734.png)</figure>选择一张小于50K的图片，例如这张：

结果变成这样子，白色的YUX字样是后来合成上去的：

<figure class="wp-block-image">![合成后预览效果图](https://image.zhangxinxu.com/image/blog/201705/2017-05-17_001750.png)</figure>不要疑惑为什么不使用这张图片？

<figure class="wp-block-image">![](https://image.zhangxinxu.com/image/study/s/s128/mm1.jpg)</figure>因为图片有大片浅色区域，水印看不清楚。

<figure class="wp-block-image">![右键保存图片示意](https://image.zhangxinxu.com/image/blog/201705/2017-05-17_002105.png)</figure>此时，我们直接右键就可以保存这张合成的图片了：

<figure class="wp-block-image">![保存到桌面的图片信息展示](https://image.zhangxinxu.com/image/blog/201705/2017-05-17_002213.png)</figure>此demo有`3`处需要特别说明的技术点，一是本地选择图片转化成`base64`地址，相关代码如下：

```
<pre class="wp-block-preformatted">inputFile.addEventListener('change', function (event) {
    var reader = new FileReader();
    var file = event.target.files[0];
    reader.onload = function(e) {
      var base64 = e.target.result;
    };
    reader.readAsDataURL(file);
});
```

主要是借助HTML5 `FileReader`读取文件的base64信息；

二是把这个图片的base64地址绘制到canvas画布上，核心JS代码如下：

```
<pre class="wp-block-preformatted">var canvas = document.createElement('canvas');
var context = canvas.getContext('2d');

// 这是上传图像
var imgUpload = new Image();
imgUpload.onload = function () {
    // 绘制
    context.drawImage(imgUpload, 0, 0, 180, 180);
};
imgUpload.src = base64;
```

三是在已经绘制好了本地图片的画布上继续画水印图片，并借助canvas的`toDataURL()`方法把我们的`canvas`画布转换成`base64`无损PNG地址。假设水印图片DOM对象名称是`eleImgCover`，则：

```
<pre class="wp-block-preformatted">// 绘制
context.drawImage(imgUpload, 0, 0, 180, 180);
// 再次绘制水印
context.drawImage(eleImgCover, 0, 0, 180, 180);
// 此时canvas.toDataURL('image/png')的结果就是最终合成的base64图片信息

var finalResult = canvas.toDataURL('image/png');
```

更完整的代码参见demo演示页面。

### 二、使用canvas实现更为复杂的图片合成

具有透明背景的水印图片合成是`canvas`图片合成中最基本最简单的，如果遇到更为复杂的合成，例如各取`50%`透明度进行合成，或者经典的mix-blend-mode混合模式的，此时可能就需要借助算法来实现了。

原理为，使用HTML5 `canvas` `getImageData()`方法获取图片完整的像素点信息，通过已知我自己设计的混合算法，对多个图片的像素信息进行合成，合并，重计算，最后把新的图片像素信息通过`putImageData()`方法重新绘制到画布上，从而实现更为复杂的图片合成效果。

其中，`getImageData()`方法返回`ImageData`对象，该对象拷贝了画布指定矩形的像素数据。

`ImageData`中有个`data`属性，这个属性是个巨大的数组，而这个数组每四个值为一组，分别对应图片中的每个像素的RGBA值，值范围如下：  
R – 红色 (0-255)  
G – 绿色 (0-255)  
B – 蓝色 (0-255)  
A – alpha 通道 (0-255; 0 是透明的，255 是完全可见的)

只要对这些数字进行重新处理，再`putImageData()`重新放到画布上，图像的效果就会发生变化。

由于高级图像合成不是本文的重点，因此这里就点到为止，重在介绍原理和提供思路。