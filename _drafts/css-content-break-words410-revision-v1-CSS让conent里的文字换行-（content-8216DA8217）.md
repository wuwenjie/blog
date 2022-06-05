---
id: 411
title: 'CSS让conent里的文字换行 （content: &#8216;\D\A&#8217;;）'
date: '2019-08-17T13:59:57+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/410-revision-v1/'
permalink: '/?p=411'
---

### 借助Unicode字符，CSS实现换行

关键CSS代码就是下面：

```
<pre class="wp-block-preformatted">dd:after {
    content: '\D\A';
    white-space: pre;
}
```

上面`"\A"`就是神奇值所在。包含换行符的CR和LF字符<sup>①</sup>一般都是unicode字符，其可像其他unicode字符一样随便插。它们分别有000D和000A Unicode编码。这就意味着，如果偶们escape内容合理，其就可以像普通内容一样插插插！

//zxx: 上面这段上标为①处的“CR和LF字符”分别指回车(CR)和换行(LF). 回车CR-将光标移动到 当前行(?) 的开头；换行LF-将光标“垂直”移动到下一行（并不移动到下一行的开头，即不改变光标水平位置）。

上面的代码纯粹CSS2.1的东西，与CSS3没有直系血缘。

```
<pre class="wp-block-preformatted">dt:before {
    content: '\D\A';
    white-space: pre;
}
dt:first-child:before { content: normal; }
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><dl>
    <dt>提问：</dt><dd>为什么没有女朋友？</dd>
    <dt>回答：</dt><dd>女朋友是什么、可以吃吗？[抠鼻图片][抠鼻图片]</dd>
</dl>
```