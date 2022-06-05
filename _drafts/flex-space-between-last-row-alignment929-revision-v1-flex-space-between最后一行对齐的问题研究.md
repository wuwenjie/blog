---
id: 946
title: 'flex space-between最后一行对齐的问题研究'
date: '2020-01-03T01:00:09+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2020/01/929-revision-v1'
permalink: '/?p=946'
---

## 背景

常见的一个图文列表设计，通常是这样的，两端顶着容器，中间的间距平均分，如下图的某东商品列表的设计：

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2020/01/products-1024x558.png)</figure>列表是这样自适应的，当视窗足够放多少个商品就放多少个，然后各个商品项目之间的间距平均分。由于每个人的视窗都可能不同，因此所看到的间距或者每一行的个数都会不同。

## 方案

想到这种设计，我们学过flex就知道，非常像flex的justify-content: space-between的效果，因此我们自然这样实现：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="css" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.flex {
  list-style: none;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.demo1 > .flex__item {
  flex-basis: 200px;
  margin-top: 1rem;
  text-align: center;
  box-sizing: border-box;
}

.demo1 > .flex__item img {
  width: 100%;
}
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><ul class="flex demo1">
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
</ul>
```

demo： [https://jsbin.com/yulevutodo/edit?html,css,output ](https://jsbin.com/yulevutodo/edit?html,css,output)

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2020/01/wrong.png)</figure>我们看到效果，最后一行不正确，应该向左对齐才对，详细比较过多种方案，个人觉得还是增加空白项这种方案最佳，就是往后面多加几个空白项，你至少要放入 最大屏能显示的个数减去1个就行了，当然放得更多也是显示正常的，但是没有必要。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><ul class="flex demo1">
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>
  <li class="flex__item">
    <img src="http://via.placeholder.com/100" />
    <p>
      图片
    </p>
  </li>  <!-- placeholder max column * placeholder - 1--> 
        <li class="flex__item placeholder"></li>
        <li class="flex__item placeholder"></li>
        <li class="flex__item placeholder"></li>
        <li class="flex__item placeholder"></li>
</ul>
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="css" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.flex {
  list-style: none;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

.demo1 > .flex__item {
  flex-basis: 200px;
  margin-top: 1rem;
  text-align: center;
  box-sizing: border-box;
}

.demo1 > .flex__item img {
  width: 100%;
}

.placeholder {
  visibility: hidden !important;
  height: 0 !important;
  border: 0 !important;
  padding: 0 !important;
  margin: 0 !important;
}
```

demo: <https://jsbin.com/koyoxupivo/edit?html,css,output>

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2020/01/correct.png)</figure>## 方案研究过程

一看到这种设计，我们真的就会自然而然想到了flex的 justify-content: space-between; 但由于最后一行的对齐问题，让我们头疼。那就不用 justify-content: space-between吧，改用默认的justify-content: flex-start试试，那么靠右的间距就得计算了，如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="css" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">     .list2 > .flex__item:nth-of-type(2n + 1) {
        margin-right: calc((100% - 200px * 2) / 1);
      }
      .list3 > .flex__item:not(:nth-of-type(3n)) {
        margin-right: calc((100% - 200px * 3) / 2);
      }
      .list4 > .flex__item:not(:nth-of-type(4n)) {
        margin-right: calc((100% - 200px * 4) / 3);
      }
```

一行放两个项目时用.list2, 放3个项目时用.list3,放4个项目时用.list4等等等，仅仅这种只是做到了间距自适应，项目固定死了，我们想通过media去控制，虽然可以，但是显然比上面复杂多了。如果说外框容器是定死的比如1000px，那么每行的项目数目也是固定的，那可能还稍微好一些。

我们接着想，还不如直接用以前的display: inline-block 或者 float:left去实现呢，但是其实本质上跟 flex-start还是一样的做法。

因此我觉得就只有放空项目方案是最佳的了，维护起来也方便。比如未来项目宽度优200px改成了100px，我们直接把200改成100再检查一下空项目是否放的足够。如果宽度也是自适应了，比如宽度占32%，那把200px改成32%即可，100/32 = 3，因此任何视窗大小下，每行都是3个了，我们空项目只要放 3-1=2个即可。

## 还有更好的方法吗

毕竟放了空项目，虽然说可以用vue/react等直接循环空项目出来，但还是感觉html还是有点脏。还有更好的方法吗（除了用grid，因为grid的兼容性暂时还不行）？希望告知，感谢！

## 研究草稿

这个是我研究过程的草稿，比较乱：<https://jsbin.com/qobuqakeri/edit?html,css,output>