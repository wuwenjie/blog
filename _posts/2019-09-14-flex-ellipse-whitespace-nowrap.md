---
id: 800
title: 'flex下省略号的问题及whitespace nowrap'
date: '2019-09-14T21:55:33+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=800'
permalink: /2019/09/flex-ellipse-whitespace-nowrap
categories:
    - 'HTML&amp;CSS'
---

 最近在搞微信小程序，发现flex下使用省略号是没有效果的，而且还会打乱预期的结构，查询statckoverflow知道需要在父级设置min-width:0; 但是在我的尝试下，依然不行，原来在上层父级就是flex那一级别也需要设置 min-width:0; 就是可以理解为 省略号标签的flex-item以及所在的flex父级 都需要设置min-width:0;

预览地址：<https://jsbin.com/vimipezogi/edit?html,css,output>

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="css" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.flex {
	display: flex;
	align-items: center;
	align-content: center;
	justify-content: space-between;
	min-width: 0; /* 这里也要设置 */
}
.flex__item {
	min-width: 0;  /* 这里需要设置 */
}
.flex__item--fixed {
	flex: 0 0 auto;
}
.fs--ellip {
	overflow: hidden;
	white-space: nowrap;
	text-overflow: ellipsis;
}


.home-shop {
  max-width: 750px;
}
.home-shop__img {
		width: 98px;
		height: 98px;
	}
.home-shop__main {
  padding: 0 36px 0 18px;
}
.home-shop__fans {
  padding: 16px 16px 16px 26px;
  border-left: 2px solid #eee;
}
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div class="flex box home-shop">
		<div class="flex">
			<img class="flex__item--fixed home-shop__img" src="https://javascript.shop/apple-touch-icon.png" />
			<div class="flex__item home-shop__main">
				<div class="fsz28 fs--ellip">杰的社区商城很长很长的文字很长很长的文字杰的社区商城很长很长的文字很长很长的文字杰的社区商城很长很长的文字很长很长的文字杰的社区商城很长很长的文字很长很长的文字</div>
				<div class="fsz22 color-9">社区小商城</div>
			</div>
		</div>
		<div class="flex flex__item--fixed">
			<div class="text-c home-shop__fans">
				<div class="fsz28 color-cyan bold">568</div>
				<div class="fsz22 color-9">粉丝数</div>
			</div>
			<div class="text-c home-shop__hot">
				<div class="fsz28 color-o bold">1898</div>
				<div class="fsz22 color-9">购买指数</div>
			</div>
		</div>
	</div>
```