---
id: 473
title: 'HTML meta标签实现定时刷新或跳转'
date: '2015-09-02T18:44:28+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=473'
permalink: /2015/09/html-meta-refresh-redirect
categories:
    - 'HTML&amp;CSS'
---

几乎所有的网页头部都有`<meta>`源信息。除了我们常用的定义编码、关键字(name=”keywords”)、描述(name=”description”)(for SEO)，还可以定义视区大小、缩放比例等(for 移动端)，如下：

```
<pre class="wp-block-preformatted"><meta name="viewport" content="width=device-width,initial-scale=1.0">
```

以及，定义网页的过期时间，Cookie的过期时间等等。

就是我们网页平时跳转，还可以使用`<meta>`实现，下面几个典型代码示例：

1. &lt;meta http-equiv=”refresh” content=”5″&gt;这个表示当前页面每5秒钟刷一下，刷一下~
2. &lt;meta http-equiv=”refresh” content=”2; url=’/'”&gt;这个表示当前页面2秒后跳到首页~
3. &lt;meta http-equiv=”refresh” content=”0; url=’http://www.qq.com/'”&gt;页面直接跳转到腾讯网~

所以，当我们下次遇到“登录成功，正在跳转到您之前访问页面……”的时候，可以使用`<meta>`的这个`refresh`刷新，跳转功能，可以说是成本最低的。

### 为何meta跳转不火呢？

大家可以看到，`meta`跳转，使用方便，不用写JS，不用会后台代码，定时跳转刷新什么的玩得照样很溜，而且兼容性好，为啥总感觉不温不火，很少看见有人提及呢？

新晋的小伙伴不知有没有听过这么一个词，叫做“万恶的IE6年代”。

据说，当年，这一批老旧的浏览器，问题很多，其中就有对`meta`2个小小的不友好。我也是听说，不一定准确。坊间是这么传闻的：

- 时间设为0的跳转，有时候页面会闪一下；
- 跳转到其他页面，浏览器后退按钮是不能用的；

但是啊，现在是什么年代啊，监狱风云都拍到第二季了，这些老问题，我觉得就可以忽略不计了。

不妨大胆试试`meta`跳转，好好利用下浏览器的原生特性，说不定就会发现比什么JS跳转之流用得更开心。