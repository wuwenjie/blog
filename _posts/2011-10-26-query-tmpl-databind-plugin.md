---
id: 342
title: 'JQuery数据绑定插件 jquery.tmpl'
date: '2011-10-26T23:37:40+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=342'
permalink: /2011/10/query-tmpl-databind-plugin
categories:
    - Javascript
---

 在下面介绍一款jQuery的模板插件 tmpl，是由微软想jQuery官方贡献的模板插件，网址：<http://api.jquery.com/jquery.tmpl/>。

 Tmpl提供了几种tag：

1. ${}：等同于{{=}}，是输出变量，通过了html编码的。
2. {{html}}:输出变量html,但是没有html编码，适合输出html代码。
3. {{if }} {{else}}:提供了分支逻辑。
4. {{each}}:提供循环逻辑，$value访问迭代变量。