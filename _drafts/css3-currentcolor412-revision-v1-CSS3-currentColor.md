---
id: 413
title: 'CSS3 currentColor'
date: '2019-08-17T14:05:56+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/412-revision-v1/'
permalink: '/?p=413'
---

CSS variables are always a hot topic when “the future of CSS” is discussed. They are actually [coming](https://css-tricks.com/native-css-variables-closer-to-reality/)natively, but using them in production (without a CSS [preprocessor](https://css-tricks.com/video-screencasts/88-intro-to-compass-sass/) framework) is a long way off. However there is a feature that actually has some browser support now that has a CSS variable feel to it, and that’s the `currentColor` value.

It works like this:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">div { 
  color: red; 
  border: 5px solid currentColor;
  box-shadow: 0 0 5px solid currentColor;
}
```

I learned about this from a panel at SXSW 2011 on CSS3 with [Stephanie](http://w3conversions.com/) &amp; [Greg](http://blog.assortedgarbage.com/) Rewis, [Estelle Weyl](http://www.standardista.com/), and [Christopher Schmitt](http://christopherschmitt.com/). Googling it turned up [Divya Manian’s article](http://nimbupani.com/current-color-in-css.html) who says:

> …you can use this value to indicate you want to use the value of color for other properties that accept a color value: borders, box shadows, outlines, or backgrounds.
> 
> This value was first supported by Opera in 2009, since then, Firefox 3.5+, Chrome 1+, and Safari 4+.

Here’s [the spec](http://www.w3.org/TR/css3-color/#currentcolor) and the [Can I Use information](http://caniuse.com/#feat=currentcolor).

### [\#](https://css-tricks.com/currentcolor/#article-header-id-0)It will follow the cascade

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">body { color: red; }
div { border: 5px solid currentColor; }
```

### [\#](https://css-tricks.com/currentcolor/#article-header-id-1)You can’t trick it.

Let’s say you wanted to exploit the variable quality to it, but then reset the color for the actual text… this won’t work:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">div {

   color: red;
      
   border: 5px solid currentColor;
   box-shadow: 0 0 5px currentColor; 
    
   color: black;
   
 }
```

**Everything will be black.**