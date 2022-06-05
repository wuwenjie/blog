---
id: 740
title: 两张图详解解元素位置宽度的属性（offsetHeight/ClientHeight/ScrollHeight/getBoundingClientRect）
date: '2019-08-23T11:36:56+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/716-revision-v1'
permalink: '/?p=740'
---

- offsetWidth/offsetHeight 返回值包含**content + padding + border**，效果与 e.getBoundingClientRect()相同
- clientWidth/clientHeight 返回值只包含**content + padding**，如果有滚动条，也**不包含滚动条**
- scrollWidth/scrollHeight 返回值包含**content + padding + 溢出内容的尺寸**

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2019/08/495870-20151105021225852-1970846349.gif)</figure>getBoundingClientRect （IE67的left、top会少2px,并且没有width、height属性）可以用来获取元素位置， 比如我经常用来获取滚动之后元素距离顶端的距离为0来判断 是否滚动到元素这个位置，然后把头部设置为fixed，比如div.getBoundingClientRect().top。 当然上面的属性也可以做到比如： **页面元素距离浏览器工作区顶端的距离 (**div.getBoundingClientRect().top**)** = div.offsetTop – (document.documentElement.scrollTop || document.body.scrollTop)

<figure class="wp-block-image">![å&#133;&#131;ç´ å°ºå¯¸](https://github.com/qiu-deqing/FE-interview/raw/master/img/element-size.png)<figcaption>、</figcaption></figure><figure class="wp-block-embed-wordpress wp-block-embed is-type-wp-embed is-provider-杰的记事本"><div class="wp-block-embed__wrapper">https://javascript.shop/2017/03/%e5%95%86%e5%93%81%e6%b7%bb%e5%8a%a0%e5%88%b0%e8%b4%ad%e7%89%a9%e8%bd%a6%e5%8a%a8%e7%94%bbgetboundingclientrect/ </div><figcaption>实例</figcaption></figure>