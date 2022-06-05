---
id: 385
title: css实现文字颜色渐变
date: '2019-08-17T13:02:23+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/384-revision-v1/'
permalink: '/?p=385'
---

基础样式：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.gradient-text{text-align: left;text-indent:30px;line-height: 50px;font-size:40px;font-weight:bolder; position: relative; }
```

## 第一种方法，使用 background-cli、 text-fill-color：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.gradient-text-one{  
    background-image:-webkit-linear-gradient(bottom,red,#fd8403,yellow); 
    -webkit-background-clip:text; 
    -webkit-text-fill-color:transparent; 
}
```

说明 ：

background: -webkit-linear-gradient(…) 为文本元素提供渐变背景。  
webkit-text-fill-color: transparent 使用透明颜色填充文本。  
webkit-background-clip: text 用文本剪辑背景，用渐变背景作为颜色填充文本。

## **第二种方法，使用 mask-image**：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.gradient-text-two{
   color:red;
}
.gradient-text-two[data-content]::after{
    content:attr(data-content);
    display: block;
    position:absolute;
    color:yellow;
    left:0;
    top:0;
    z-index:2;
    -webkit-mask-image:-webkit-gradient(linear, 0 0, 0 bottom, from(yellow), to(rgba(0, 0, 255, 0)));
}
```

说明：

mask-image 和 background-image 一样，不仅可以取值是 图片路径，也可以是渐变色。

## **第三种方法，使用 linearGradient、fill：**

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.gradient-text-three{
    fill:url(#SVGID_1_);
    font-size:40px;
    font-weight:bolder;
}
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><svg viewBoxs="0 0 500 300" class="svgBox">
    <defs>
        <linearGradient id="SVGID_1_" gradientUnits="userSpaceOnUse" x1="0" y1="10" x2="0" y2="50">
            <stop  offset="0" style="stop-color:yellow"/>
            <stop  offset="0.5" style="stop-color:#fd8403"/>
            <stop  offset="1" style="stop-color:red"/>
        </linearGradient>
    </defs>
    <text text-anchor="middle" class="gradient-text-three" x="110px" y="30%">花信年华</text>
</svg>
```

说明：

在SVG中，有两种主要的渐变类型：

线性渐变（linearGradient）  
放射性渐变（radialGradient）  
SVG中的渐变不仅可以用于填充图形元素，还可以填充文本元素

dom示例:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <title>CSS3渐变字体</title>
    <link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css">
    <script src="https://cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
    <script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
    <style type="text/css">
        *{margin:0;padding:0;}
        body,html{width:100%;height:100%;}
        .wrapper{width:80%;margin:0 auto;margin-top:30px;}
        .gradient-text{text-align: left;text-indent:30px;line-height: 50px;font-size:40px;font-weight:bolder; position: relative; }
        .gradient-text-one{  
            background-image:-webkit-linear-gradient(bottom,red,#fd8403,yellow); 
            -webkit-background-clip:text; 
            -webkit-text-fill-color:transparent; 
        }
        .gradient-text-two{
            color:red;
        }
        .gradient-text-two[data-content]::after{
            content:attr(data-content);
            display: block;
            position:absolute;
            color:yellow;
            left:0;
            top:0;
            z-index:2;
            -webkit-mask-image:-webkit-gradient(linear, 0 0, 0 bottom, from(yellow), to(rgba(0, 0, 255, 0)));
        }
        .gradient-text-three{
            fill:url(#SVGID_1_);
            font-size:40px;
            font-weight:bolder;
        }
    </style>
</head>
<body>
    <section class="wrapper">
        <div class="panel panel-info">
            <div class="panel-heading">
                <h3 class="panel-title">方法1. background-clip + text-fill-color</h3>
            </div>
            <div class="panel-body">
                <h3 class="gradient-text gradient-text-one">花样年华</h3>
            </div>
        </div>
        <div class="panel panel-warning">
            <div class="panel-heading">
                <h3 class="panel-title">方法2. mask-image</h3>
            </div>
            <div class="panel-body">
                <h3 class="gradient-text gradient-text-two" data-content="豆蔻年华">豆蔻年华</h3>
            </div>
        </div>
        <div class="panel panel-danger">
            
            <div class="panel-heading">
                <h3 class="panel-title">方法3. svg linearGradient</h3>
            </div>
 
            <div class="panel-body">
                <svg viewBoxs="0 0 500 300" class="svgBox">
                    <defs>
                        <linearGradient id="SVGID_1_" gradientUnits="userSpaceOnUse" x1="0" y1="10" x2="0" y2="50">
                            <stop  offset="0" style="stop-color:yellow"/>
                            <stop  offset="0.5" style="stop-color:#fd8403"/>
                            <stop  offset="1" style="stop-color:red"/>
                        </linearGradient>
                    </defs>
                    <text text-anchor="middle" class="gradient-text-three" x="110px" y="30%">花信年华</text>
                </svg>
            </div>
 
        </div>
    </section>
</body>
</html>
```

效果：

<figure class="wp-block-image">![](//upload-images.jianshu.io/upload_images/13133049-d1956d234cfbc139.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/470/format/webp)</figure></body></html>