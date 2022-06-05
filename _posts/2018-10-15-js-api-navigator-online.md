---
id: 621
title: 判断用户是否断网navigator.onLine
date: '2018-10-15T19:03:12+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=621'
permalink: /2018/10/js-api-navigator-online
categories:
    - Javascript
---

很多时候 web 网页上需要判断或监听浏览器(系统)的网络状态。

Navigator onLine 属性  
onLine 属性是一个只读的布尔值，声明了系统是否处于脱机模式，如果系统属于脱机状态，则返回 false，否则返回 true。

注：在 IE 4+ 中，用户可以在浏览器中选择脱机工作，当脱机工作被选后，系统就进入了脱机状态，内容将从缓存进行读取。

语法：navigator.onLine

所有主要浏览器都支持 onLine 属性

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0"/>
    <title>网络状态</title>
    <style type="text/css">
    </style>
    <!-- JQuery CDN -->
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"
            integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
            crossorigin="anonymous"></script>
    <script type="text/javascript">
        $(function () {
            $("button").click(function () {
                if (navigator.onLine) {
                    console.log("online");
                    $("div").append("网络正常\t");
                } else {
                    console.log("offline");
                    $("div").append("网络离线\t");
                }
            });
        });
    </script>
</head>
<body>
<button>获取网络状态</button>
<div></div>
</body>
</html>
```

<figure class="wp-block-image">![](https://img-blog.csdnimg.cn/20181106150115462.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmdteDE5OTMzMjg=,size_16,color_FFFFFF,t_70)</figure>PC 端亲测结果：

> 1）当将电脑网络禁用，或者直接拔掉网线，点击按钮时，navigator.onLine 返回 false
> 
> 2）当浏览器能正常上网，点击按钮时，navigator.onLine 返回 true
> 
> 3）当手动修改电脑 IP 地址，使 ip 异常浏览器无法上网时，点击按钮，navigator.onLine 返回 taue，

手机端亲测结果：

1）当关闭 WIFI 与 流量时，点击按钮时，navigator.onLine 返回 false

2）当打开 WIFI 或者 流量时，点击按钮，navigator.onLine 返回 true

## 扩展

\[HTML5 API中Network Information API\]：(<https://github.com/AurelioDeRosa/HTML5-API-demos>)：

我们可以通过事件的件的监听，去实时获取到网络状态的变化   
 window.addEventListener(‘offline’, function(e) {alert(‘offline’);})   
 window.addEventListener(‘online’, function(e) {alert(‘online“’);})

拓展\[HTML5 API中Network Information API\]：(https://github.com/AurelioDeRosa/HTML5-API-demos)：

connection  
connection也是navigator的一个属性，他包含了一些当前连接的基本信息，其中connection.type值的就是当前的网络类型。其值包含了一下几个：

当网络类型发生变化时，同样的也是可以通过事件监听的方式来实时获取到网络类型。代码实现如下：   
 var connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;   
 var type = connection.type;   
 function updateConnectionStatus() {   
 alert(“网络由 ” + type + ” 变化为 ” + connection.type);   
 }   
 //监听网络类型发生变化   
 connection.addEventListener(‘typechange’, updateConnectionStatus);

</body></html>