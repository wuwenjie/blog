---
id: 725
title: flex常见页面结构Layout示例
date: '2015-08-20T20:51:29+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=725'
permalink: /2015/08/flex-web-layout-examples
categories:
    - 'HTML&amp;CSS'
---

常见页面结构大概有：左右分栏、上下分栏。

犹如上下分栏式，我们可以这样：

<figure class="wp-block-image">![](http://www.deathghost.cn//article/2018-10/1539876345613.jpg)<figcaption>flex布局-上下分栏</figcaption></figure>```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div class="layout-01">
    <header>Header</header>
    <main>Content</main>
    <footer>Footer</footer>
</div>
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.layout-01{width:640px;height:400px;background-color:#f9f9f9;display:flex;flex-direction: column;margin:10px auto;}
.layout-01 header{height:100px;border-bottom:2px #b2dfff solid;text-align:center;}
.layout-01 footer{height:100px;border-top:2px #b2dfff solid;text-align:center;}
.layout-01 main{flex:1;text-align:center;}
```

应用语法：**display:flex**与**flex-direction:column**；

我们要对其模块做弹性布局首先第一父级需要display与flex-direction。

**flex**用于设置检索盒模型内子元素的空间分配，它是**flex-grow**、**flex-shrink** 和 **flex-basis** 属性的简写属性。

flex-direction是控制盒内元素方向的。其语法：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">flex-direction: row|row-reverse|column|column-reverse|initial|inherit;
```

该篇文章主要应用到**row**与**column**属性，即**水平**方位与**垂直**方位的显示。

接下来几个图例围绕这两个属性进行页面整体结构布局。

上下结构，中间左右分栏式web结构：

<figure class="wp-block-image">![](http://www.deathghost.cn//article/2018-10/1539877272869.jpg)<figcaption>flex布局</figcaption></figure>```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div class="layout-02">
    <header>Header</header>
    <main>
        <aside>Aside</aside>
        <section class="content">Content</section>
    </main>
    <footer>Footer</footer>
</div>
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.layout-02{width:640px;height:400px;background-color:#f9f9f9;display:flex;flex-direction: column;margin:10px auto;}
.layout-02 header{height:100px;border-bottom:2px #b2dfff solid;text-align:center;}
.layout-02 footer{height:100px;border-top:2px #b2dfff solid;text-align:center;}
.layout-02 main{flex:1;text-align:center;display: flex; flex-direction: row;}
.layout-02 main aside{width:150px;border-right:2px #b2dfff solid; }
.layout-02 main .content{padding:10px;flex:1;}
```

这里我们可以看出，flex方法可以进行多次嵌套使用。

<figure class="wp-block-image">![flex](http://www.deathghost.cn//article/2018-10/1539877562409.jpg)<figcaption>flex布局</figcaption></figure>```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div class="layout-03">
    <header>Header</header>
    <main>
        <aside>Aside</aside>
        <section class="content">Content</section>
        <aside>Aside</aside>
    </main>
    <footer>Footer</footer>
</div>
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.layout-03{width:640px;height:400px;background-color:#f9f9f9;display:flex;flex-direction: column;margin:10px auto;}
.layout-03 header{height:100px;border-bottom:2px #b2dfff solid;text-align:center;}
.layout-03 footer{height:100px;border-top:2px #b2dfff solid;text-align:center;}
.layout-03 main{flex:1;text-align:center;display: flex; flex-direction: row;}
.layout-03 main aside{width:150px;border-right:2px #b2dfff solid; }
.layout-03 main aside:last-of-type{width:150px;border-left:2px #b2dfff solid;border-right:none;}
.layout-03 main .content{padding:10px;flex:1;}
```

只需要对响应式内容设置**flex:1**即可。

<figure class="wp-block-image">![flex布局](http://www.deathghost.cn//article/2018-10/1539877714791.jpg)<figcaption>flex布局</figcaption></figure>基本是就是这样子的啦，至于结构内板块内容细节布局，有些我们也是可以运用此方法，好比左右图文列表

<figure class="wp-block-image">![flex布局](http://www.deathghost.cn//article/2018-10/1539877847874.jpg)<figcaption>flex布局</figcaption></figure>```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><ul>
    <li>
        <a href="#" title="描述">
            <div class="pic">
                <img src="http://www.deathghost.cn//article/2018-08/1533745610054.jpg" alt="" srcset="">
            </div>
            <div class="text">
                <h2>图片说明</h2>
                <p>描述性文本信息...</p>
            </div>
        </a>
    </li>
    <li>
        <a href="#" title="描述">
            <div class="pic">
                <img src="http://www.deathghost.cn//article/2018-09/1537006566016.jpg" alt="" srcset="">
            </div>
            <div class="text">
                <h2>图片说明</h2>
                <p>描述性文本信息...</p>
            </div>
        </a>
    </li>
</ul>
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">ul{margin:0;padding:0;list-style: none;width:640px;margin:0 auto;}
ul li{border-bottom:1px #d2d2d2 solid;background-color:#f9f9f9;padding:20px;}
ul li:last-of-type{border:none;}
ul li a{display:flex;align-items:center;}
ul li .pic{width:150px;height:150px;border:1px #d2d2d2 solid;overflow:hidden;}
ul li .pic img{width:auto;max-width:100%;height:100%;}
ul li .text{margin-left:10px;}
```

以上css样式只是临时性编写，实际项目中，大家可以将公共的class提取出来，避免重复书写，不论是用css原始写法或sass又或less编写，都很方便。