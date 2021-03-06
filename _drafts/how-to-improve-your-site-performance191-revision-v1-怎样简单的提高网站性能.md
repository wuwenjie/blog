---
id: 264
title: 怎样简单的提高网站性能
date: '2019-08-16T19:19:36+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/191-revision-v1/'
permalink: '/?p=264'
---

### 小小性能改变，大大影响发生

用户的耐心不是线性的。第1秒的时候基本上没有人会放弃这个站点。但是，1秒开外之后，如果没有适当的反馈的话（例如浏览器标头显示页面标题），用户开始以一个加速的比率离开。到3~4秒的时候，一般的站点会一半的潜在用户。当然，具体的阈值根据网站、用户行为和意图以及其他因素不同而有所不同……但万变不离其宗。

### 瓶颈

快速测试：当HTML载入浏览器之后，用户等待你页面加载的时间百分比是多少？如果你不是做web开发的，或是经常混迹于性能社区的话答案可能会让你大跌眼镜。一般超过90%，用户花在等待上的时间的90%实在页面HTML载入到浏览器之后。为什么会这样呢？

### 获取HTML仅仅是个开始

大肆分析浏览器是如何工作有点超出范围了，不过总的来说，浏览器解析HTML是有序地发现的资源（如脚本，样式和图片）、请求，然后要么解析，要么执行或者就是适时显示。

但是这些资源并不是一次性获取的。相反，浏览器通过页面只能向服务器打开有限数量的连接，通过建立TCP和HTTP连接和一些不可避免的延迟，发送的请求和响应的字节通过网络传回来。

因此，一般而言，浏览器和服务器之间的双向运输的代价是昂贵的。HTML的结果标记，资源的数目和顺序是性能上绝对关键的因素。

在我们关心假期网站访问量之前，我们花个几分钟看看web开发者和网站站长关于网站性能所犯的7大错误，以及如何避免和纠正的一些建议。

### 1. 太多的HTTP请求

这是绝对多数网页性能问题的症结所在，许多有效的解决该问题的WPO技术是完全不同的方法，下面就是一些：

**合并脚本和样式**简单地将脚本文件们合并成一个。对于样式，可以直接把所有.css文件合并成一个。人工维护需要很大成本，但目前有自动化的解决方案。

**合并图片为Sprites**CSS Spriting已经变成主流技术。其做法是将很多个公共图片合并为一个大的图片文件，然后你通过CSS控制位置让图片需要的地方显示。于是，告别N多图片，现在只有一个。

事先提醒一句，这个技术的维护是很折腾的，因为即使是个很小的更改也要更新整个图片以及CSS，甚至是HTML。幸运的是，CSS spriting自动化工具如[SpriteMe](http://spriteme.org/), [Compass](http://compass-style.org/help/tutorials/spriting/), [Yottaa](http://www.yottaa.com/web-performance-optimization)

**使用尽量少的图片**在一个页面上行使用太多图片是个老大难问题了，其历史可以追溯到古老的`img`标签。一般有两类解决方法。其一是使用CSS代替图片文件(background-color, border, buttons, hover效果等)，另外则是对小图使用”[data URIs](http://jonraasch.com/blog/css-data-uris-in-all-browsers)“

当图片对于页面是必需的情况下我们可以考虑图像的分页，例如电子商务的目录。

另外的解决方法可能就有些强硬了：就是让设计师或是产品所有者创建简单的不需要很多图片的页面。

### 2. 客户端最低限度处理

很多站点不能很好地运用客户端的能力，而把所有的工作都交给服务器。举个简单的例子，如表单验证：把表单数据发送给服务器，在服务器端验证，然后返回错误信息。oh, my GAGA, 这可不是一般的低效啊。

**客户端的验证**相反，验证用户输入的信息应该在页面内，就在输入发生的地方。由于安全的原因，网页应用程序也应该在服务器端验证。web安全准则之一即是不能相信用户的输入。所以，客户端的验证是出于性能和交互的原因，而服务器端的验证是出于安全原因。

**使用网络标准和MVC分离**使用web标准对于创建易维护，可访问，易证明的站点是很关键的。其也性能最佳化最好的基础。使用现代网络标准鼓励内容(HTML)，样式(CSS)和行为(JavaScript)分离。

换句话说，历史悠久的`"MVC"[[Model/View/Controller]`设计模式正在你的网站代码中发挥着作用。

把HTML（实为DOM）当作`module`，CSS作为`view`，JavaScript作为`controller`。这种分离会使代码更高效，更利于维护，也使得很多优化技术的应用更为可行。

**演示代码放到客户端**上面提到的表单验证的例子，很多场景要求客户端做的更多。图表和形状——任何形式耐看的可视化数据——曾经必须依靠服务器端。

黄鹤一去不复返。现在它就是把数据从服务器端推送到客户端（例如JSON格式），然后使用CSS和JavaScript在浏览器中创建漂亮的图形，图表，可视化内容。这种方法避免了很多与服务器的交互，可以说，服务器做了更有意义的事情。

因为仅仅是推送数据，因此，节约了服务器的CPU，缩短了等待时间，并利用未充分利用的资源提供给每个客户端。有不少很赞的工具可以数据的可视化处理，包括：[Processing](http://processingjs.org/), [D3](http://mbostock.github.com/d3/) 和 [Flot](http://code.google.com/p/flot/)

**利用Ajax技术**只让页面需要的一小部分去响应用户的操作，可以让你的网站或web程序变得更加的互动和高效。这有多种方法（例如获取HTML或脚本或数据）。如果你不需要的话，你可以不要刷新整个页面！如果你还未加入Ajax的大家庭，[这本书](http://www.manning.com/crane/)虽然有些年头了但是概述是相当棒的！

### 3. 低并行请求数

获取一个脚本，然后解析它，执行它，再获取下一个，如此往复。然后使用所有可用的链接，从同一个服务器下载一些图片。它们一旦下载完毕，然后获取其他。听上去有效？事实并非如此。用户的带宽往往不是限制的主要因素，相反，是没有考虑到浏览器的行为低效的标记。

你可以通过一些举措让所有浏览器一次可以发出更多的请求，这对于延迟很有效果。

**使用浏览器相应域分区**一些老的单依旧流行的浏览器，如IE7，有个称为“域分区”的东西。具体来讲就是使用指向同一服务器但不同的域名来提高每次页面的请求数目。例如`img1.foo.com`和`img2.foo.com`要比单纯使用`img.foo.com`两倍高效下载。注意，对于新兴浏览器，这个技术不适合，因为你需要承担DNS成本而实际并未带来什么好处。WPO大师Steve Souders对这个权衡做了很好的解释工作，点击[这里](http://www.stevesouders.com/blog/2009/05/12/sharding-dominant-domains/)。

**使用智能脚本加载**现在的脚本下载器激增，这些可以最优化多个脚本下载的性能。这些脚本下载器通常都是采用异步下载，避免默认的脚本阻塞等问题。关于脚本下载，可以多多参考《高性能网站进阶指南》一书，这里不多啰嗦。

### 4. 没有使用浏览器缓存或本地存储

显然，最快的获取资源的方法就是从本地缓存中获取了。

**使用正确的header**为静态资源设置长时间缓存头，尤其是这些资源被多个页面调用的时候，这是一个很好的提高性能的方法。因为明确的客户端缓存失效是不可能的，更新缓存内容的方法一般是对其名字进行处理。

还有另外一种技术，如果你手动做的话代价较高，如果自动化（例如通过脚本构建）就很迅速。这个方法就是使用`"Expires"`头。由于经常更新内容，使用`"Last-Modified"`响应头，以便在浏览器中触发条件`"If-Modified-Since"`请求。条件请求要明显慢于本地缓存查找，但远远高于一个完整的返回。

这儿有个相当不错的[HTTP缓存综述](http://www.mnot.net/cache_docs/)。

**使用本地存储(local storage)**一个WPO新利器就是HTML5中的local storage。对于支持的浏览器，其存储要比cookie精确很多很多，而且跟cookie相比，其请求无压力。

### 5. 第三方插件

第三方组件很多时候就是网页性能祸根。

**避免第三方插件**必要的插件有时候还是需要的，但是，为了避免最后搞得像瘟疫一样，避免使用之。

**使用异步实现**对于尝试使用的插件，最好采用异步实现。以避免其糟糕的性能拖累你整个页面的交互体验。

**性能测量（停止使用低性能的）**如题。

### 6. 太多的字节数

有不少方法可以让响应的尺寸更小。

**压缩**一个很明显也很重要的方案就是引入压缩(gzip)。客户端轻微的解压性能损失通常通过较少延迟，和较少字节缓解。在服务器端，预压缩静态资源有助于较小CPU的开销。像Apache的mod\_deflate中的服务器端解决方案，压缩动态内容，以确保压缩的内容发送到客户端并可以处理它（像请求头表示的`"Accept - Encoding:GZIP, DEFLATE"`）。

需要注意的是和缓存相结合的压缩。确保使用`"Vary: Accept-Encoding"`头，以便缓存可以响应合适的请求内容。

其他技术包括：

- 更彻底的内容编辑
- 图像优化(http://www.smushit.com/ysmush.it/)
- JavaScript和CSS重构和最小化
- 客户端代码重用
- 分页
- Ajax
- cookie的域（图片等静态资源）

### 7. 未使用全球网络

另一个常见错误就是忽略地理位置。如果您的网站是在纽约市的数据中心托管，在加利福尼亚州的用户和波士顿的用户（更不用说亚洲）有一个巨大差异的延迟。传统DNS服务内容扮演的是边缘角色。使用云服务提供商发布内容至更多的地点，使用户总是从他们附近的服务器获取，这比DNS更好。

像Yottaa尖端的网络性能优化服务，可以跨多个云服务提供商的路由请求您的网页，其内容分发到世界各地的用户，代表了不同地域用户优化的下一代解决方案。

性能问题，特别是在假期前后。在黑色星期五，网络星期一之前，衡量你的网站的性能，然后改进它。