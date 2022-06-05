---
id: 664
title: Canvas两点连线及多点连线
date: '2019-08-19T00:07:33+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/663-revision-v1/'
permalink: '/?p=664'
---

使用html5 Canvas绘制直线所需的`CanvasRenderingContext2D`对象的主要属性和方法(有”()”者为方法)如下：

| 属性或方法 | 基本描述 |
|---|---|
| strokeStyle | 用于设置画笔绘制路径的颜色、渐变和模式。该属性的值可以是一个表示css颜色值的字符串。如果你的绘制需求比较复杂，该属性的值还可以是一个[CanvasGradient](https://codeplayer.vip/p/j7scr)对象或者[CanvasPattern](https://codeplayer.vip/p/j7scv)对象 |
| globalAlpha | 定义绘制内容的透明度，取值在0.0(完全透明)和1.0(完全不透明)之间，默认值为1.0。 |
| lineWidth | 定义绘制线条的宽度。默认值是1.0，并且这个属性必须大于0.0。较宽的线条在路径上居中，每边各有线条宽的一半。 |
| lineCap | 指定线条两端的线帽如何绘制。合法的值是 “butt”、”round”和”square”。默认值是”butt”。你可以点击这里[查看lineCap的具体示例](https://codeplayer.vip/p/j7scj)，以帮助你更好地理解`lineCap`。 |
| beginPath() | 开始一个新的绘制路径。**每次绘制新的路径之前记得调用该方法。**它将重置内存中现有的路径。 |
| moveTo(int x, int y) | 移动画笔到指定的坐标点`(x,y)`，该点就是新的子路径的起始点 |
| lineTo(int x, int y) | 使用直线连接当前端点和指定的坐标点`(x,y)` |
| stroke(int x, int y) | 沿着绘制路径的坐标点顺序绘制直线 |
| closePath() | 如果当前的绘制路径是打开的，则关闭掉该绘制路径。**此外，调用该方法时，它会尝试用直线连接当前端点与起始端点来关闭路径，但如果图形已经关闭(比如先调用了stroke())或者只有一个点，它会什么都不做。** |

在Canvas的图形绘制过程中，几乎都是先按照一定顺序先定下几个坐标点，也就是所谓的绘制路径，然后再根据我们的需要将这些坐标点用指定的方式连接起来，就形成了我们所需要的图形。当我们了解了`CanvasRenderingContext2D`对象的上述API后，那么绘制线条就显得非常简单了。

### 使用canvas绘制基本的直线

现在，我们就使用canvas来绘制最基本的直线。<a>复制</a>全屏全选*JavaScript:*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<html>
<head>
<meta charset="UTF-8">
<title>HTML5 Canvas绘制线条入门示例</title>
</head>
<body>

<!-- 添加canvas标签，并加上红色边框以便于在页面上查看 -->
<canvas id="myCanvas" width="400px" height="300px" style="border: 1px solid red;">
您的浏览器不支持canvas标签。
</canvas>

<script type="text/javascript">
//获取Canvas对象(画布)
var canvas = document.getElementById("myCanvas");
//简单地检测当前浏览器是否支持Canvas对象，以免在一些不支持html5的浏览器中提示语法错误
if(canvas.getContext){	
	//获取对应的CanvasRenderingContext2D对象(画笔)
	var ctx = canvas.getContext("2d");
	
	//注意，Canvas的坐标系是：Canvas画布的左上角为原点(0,0)，向右为横坐标，向下为纵坐标，单位是像素(px)。
	
	//开始一个新的绘制路径
	ctx.beginPath();
	//定义直线的起点坐标为(10,10)
	ctx.moveTo(10, 10);
	//定义直线的终点坐标为(50,10)
	ctx.lineTo(50, 10);
	//沿着坐标点顺序的路径绘制直线
	ctx.stroke();
	//关闭当前的绘制路径
	ctx.closePath();
}
</script>
</body>
</html>
```

显示效果如下：[运行代码](https://codeplayer.vip/diy.php?f=html5-canvas-draw-line-demo-1)

<figure class="wp-block-image">![使用canvas绘制最基本的直线](https://cdn.codeplayer.vip/old/canvas-line-1.png!cp)</figure>### 使用canvas绘制带颜色的直线

大家都知道，在现实世界中，画笔也是多种多样的，并且具有各种不同的颜色。同样的，Canvas的画笔`CanvasRenderingContext2D`对象也同样可以具有你所需要的各种颜色。在上面的代码示例中，我们没有指定颜色的话，Canvas的画笔就默认为最常见的黑色。

现在我们再次使用Canvas的画笔绘制一条蓝色的直线(基于页面简洁考虑，下面只给出关键的JavaScript代码，请同时参考上面完整的代码示例)。<a>复制</a>全屏全选*JavaScript:*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<html>
<head>
<meta charset="UTF-8">
<title>HTML5 Canvas绘制线条入门示例</title>
</head>
<body>

<!-- 添加canvas标签，并加上红色边框以便于在页面上查看 -->
<canvas id="myCanvas" width="400px" height="300px" style="border: 1px solid red;">
您的浏览器不支持canvas标签。
</canvas>

<script type="text/javascript">
//获取Canvas对象(画布)
var canvas = document.getElementById("myCanvas");
//简单地检测当前浏览器是否支持Canvas对象，以免在一些不支持html5的浏览器中提示语法错误
if(canvas.getContext){	
	//获取对应的CanvasRenderingContext2D对象(画笔)
	var ctx = canvas.getContext("2d");	
	
	//开始一个新的绘制路径
	ctx.beginPath();
	//定义直线的起点坐标为(10,10)
	ctx.moveTo(10, 10);
	//定义直线的终点坐标为(50,10)
	ctx.lineTo(50, 10);
	//沿着坐标点顺序的路径绘制直线
	ctx.stroke();
	//关闭当前的绘制路径
	ctx.closePath();

	//绘制一条带颜色的直线
	ctx.moveTo(10, 30);
	ctx.lineTo(50, 30);
	//支持css颜色值的各种表现形式，例如："blue"、"#0000ff"、"#00f"、"rgb(0,0,255)"、"rgba(0,0,255,1)"
	//颜色等各种设置，必须在最终的绘制函数stroke()之前调用
	ctx.strokeStyle = "blue"; 
	ctx.stroke();
	//关闭当前的绘制路径
	ctx.closePath();
}
</script>
</body>
</html>
```

对应的显示效果如下图：[运行代码](https://codeplayer.vip/diy.php?f=html5-canvas-draw-line-demo-2)

<figure class="wp-block-image">![使用canvas绘制的蓝色直线](https://cdn.codeplayer.vip/old/canvas-blue-line.png!cp)</figure>### 使用canvas绘制基本的折线

当我们掌握了Canvas绘制直线之后，绘制折线等其他形式的线条就简单多了。我们只需要多绘制几个路径中间点，并依次将它们连接起来即可。<a>复制</a>全屏全选*JavaScript:*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><script type="text/javascript">
//获取Canvas对象(画布)
var canvas = document.getElementById("myCanvas");
//简单地检测当前浏览器是否支持Canvas对象，以免在一些不支持html5的浏览器中提示语法错误
if(canvas.getContext){	
	//获取对应的CanvasRenderingContext2D对象(画笔)
	var ctx = canvas.getContext("2d");	
	
	//开始一个新的绘制路径
	ctx.beginPath();
	//设置线条颜色为蓝色
	ctx.strokeStyle = "blue";
	//设置路径起点坐标
	ctx.moveTo(20, 50);
	//定义中间点坐标1
	ctx.lineTo(60, 50);
	//定义中间点坐标2
	ctx.lineTo(60, 90);
	//定义中间点坐标3(这是最后一个中间点，也就是终点)
	ctx.lineTo(100, 90);
	//按照绘制路径顺序连接各个坐标点
	ctx.stroke();
	//关闭绘制路径
	ctx.closePath();
}
</script>
```

对应的显示效果如下图：[运行代码](https://codeplayer.vip/diy.php?f=html5-canvas-draw-line-demo-3)

<figure class="wp-block-image">![使用Canvas绘制的折线效果](https://cdn.codeplayer.vip/old/canvas-blue-broken-line.png!cp)</figure>掌握上述内容后，相信大家对使用Canvas绘制线条有一些基本的了解了吧。由于对线条的宽度、透明度等控制只是设置单个属性即可，请参考上面的相关API，这里就不再赘述了。

*强烈注意：*在绘制图形路径时，一定要先调用`beginPath()`。`beginPath()`方法将会清空内存中之前的绘制路径信息。如果不这样做，对于绘制单个图形可能没什么影响，但是在绘制多个图形时(例如上面示例的两条直线)，将会导致路径绘制或者颜色填充等操作出现任何意料之外的结果。

此外，对于`closePath()`方法，初学者一定要稍加注意，尤其是上面API表格中`closePath()`方法描述中的红色文字。在上面绘制折线的代码示例中，我们先调用了`stroke()`，再调用了`closePath()`。其实在调用`stroke()`方法时，折线就已经绘制好了，当前的绘制路径也就被关闭掉了，所以再调用`closePath()`方法时，它就不会使用直线连接当前端点和起始端点(也就是说，这里的`closePath()`是可有可无的，不过为了保持良好的习惯，还是建议写上)。如果我们交换一下`stroke()`和`closePath()`的调用顺序，则情况完全不一样了。由于`closePath()`先调用，此时绘制路径并没有关闭，那么`closePath()`将会用直线连接当前端点和起始端点。

交换`stroke()`和`closePath()`调用顺序后的示例代码如下：<a>复制</a>全屏全选*JavaScript:*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><script type="text/javascript">
//获取Canvas对象(画布)
var canvas = document.getElementById("myCanvas");
//简单地检测当前浏览器是否支持Canvas对象，以免在一些不支持html5的浏览器中提示语法错误
if(canvas.getContext){	
	//获取对应的CanvasRenderingContext2D对象(画笔)
	var ctx = canvas.getContext("2d");	
	
	//开始一个新的绘制路径
	ctx.beginPath();
	//设置线条颜色为蓝色
	ctx.strokeStyle = "blue";
	//设置路径起点坐标
	ctx.moveTo(20, 50);
	//定义中间点坐标1
	ctx.lineTo(60, 50);
	//定义中间点坐标2
	ctx.lineTo(60, 90);
	//定义中间点坐标3(这是最后一个中间点，也就是终点)
	ctx.lineTo(100, 90);
	//先关闭绘制路径。注意，此时将会使用直线连接当前端点和起始端点。
	ctx.closePath();
	//最后，按照绘制路径顺序连接各个坐标点
	ctx.stroke();
}
</script>
```

交换调用顺序后，对应的显示效果如下：[运行代码](https://codeplayer.vip/diy.php?f=html5-canvas-draw-line-demo-4)

<figure class="wp-block-image">![交换调用顺序后的canvas绘制效果](https://cdn.codeplayer.vip/old/canvas-blue-broken-line-2.png!cp)</figure></body></html>