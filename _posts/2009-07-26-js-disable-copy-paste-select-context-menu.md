---
id: 126
title: 禁止复制，剪切，粘贴，选中，右键等功能的写法
date: '2009-07-26T21:29:56+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=126'
permalink: /2009/07/js-disable-copy-paste-select-context-menu
categories:
    - Javascript
---

**1、 oncontextmenu=”window.event.returnValue=false” 将彻底屏蔽鼠标右键特效，但是快捷键未被屏蔽**

**2、&lt;table border oncontextmenu=return(false)&gt; &lt;td&gt;no &lt;/table&gt; 用于Table**

**3.、 &lt;body onselectstart=”return false”&gt; 取消选取、防止复制 javascript技巧**

**4.、onpaste=”return false” 不准粘贴技巧**

**5、oncopy=”return false;” oncut=”return false;” 防止复制的javascirpt特效**

通常在TextBox中会防止复制粘贴等操作，均可使用上述语句，尽管写在VS2005中时，有波浪线标注，但是依然可行。。。。