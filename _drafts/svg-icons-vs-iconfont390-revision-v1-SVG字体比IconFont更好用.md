---
id: 391
title: SVG字体比IconFont更好用
date: '2019-08-17T13:18:25+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/390-revision-v1/'
permalink: '/?p=391'
---

**也许大家都用过字体图标（**[**https://www.iconfont.cn/**](https://www.iconfont.cn/)**——，但这次看了这篇文章，感觉直接使用svg更加方便。。。**

I’ve been a big proponent of icon fonts. Lots of sites really need a system for icons, and icon fonts offer a damn fine system. However, I think **assuming you’re good with IE 9+**, using inline SVG and the `<use>`element to reference an icon is a superior system.

First let’s cover how it works.

A nice way to handle your icons is to have a folder full of `.svg` files.

<figure class="wp-block-image">![](https://css-tricks.com/wp-content/uploads/2014/03/folder-of-svgs.png)<figcaption>That’s one of the cool things about working with SVG – they **are** the source files.</figcaption></figure>They can be colored, not colored, multiple shapes, sizes, whatever.

<figure class="wp-block-image">![](https://css-tricks.com/wp-content/uploads/2014/03/svg-whatever.png)</figure>You can let Illustrator (or whatever) save it however, with all the cruft that comes along for the ride:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 16.0.4, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->

<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="100px" height="100px" viewBox="0 0 100 100" enable-background="new 0 0 100 100" xml:space="preserve">
<g>
	<path d="M50.049,0.3c14.18,0.332,25.969,5.307,35.366,14.923S99.675,36.9,100,51.409c-0.195,11.445-3.415,21.494-9.658,30.146 - yadda yadda yadda"/>
</g>
</svg>
```

### [\#](https://css-tricks.com/svg-sprites-use-better-icon-fonts/#article-header-id-0)Combine the .svg files

You can manually do this if you want. I’ve done it. You don’t even have to look at the final file. Just call it `svg-defs.svg` or something.

It should just be an `<svg>` tag, with a `<defs>` tag (which just means you are defining stuff to use later), and then a bunch of `<g>` (group) tags. Each `<g>` tag will have a unique ID, and will wrap all the paths and whatnot for each icon.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><svg>
  <defs>

    <g id="shape-icon-1">
      <!-- all the paths and shapes and whatnot for this icon -->
    <g>

    <g id="shape-icon-2">
      <!-- all the paths and shapes and whatnot for this icon -->
    <g>

    <!-- etc -->

  </defs>
</svg>
```

Turns out `<symbol>` is probably a better choice than `<g>`. [Read all about it!](https://css-tricks.com/svg-symbol-good-choice-icons/)

Again you can do that by hand, but of course that’s a bit laborious. Fabrice Weinberg has created a Grunt plugin called [grunt-svgstore](https://github.com/FWeinb/grunt-svgstore) that automates this.

If you’ve never used Grunt, you can do it. [Here’s a screencast](https://css-tricks.com/video-screencasts/130-first-moments-grunt/) to get you started.

You can install it with:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npm install grunt-svgstore --save-dev
```

Make sure the task is available with:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">grunt.loadNpmTasks('grunt-svgstore');
```

And then in the config:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">svgstore: {
  options: {
    prefix : 'shape-', // This will prefix each <g> ID
  },
  default : {
      files: {
        'dest/svg-defs.svg': ['svgs/*.svg'],
      }
    }
  }
},
```

In the output file, `svg-defs.svg`, each icon (whatever paths and stuff from the source .svg file) will be wrapped up in a `<g>` tag with a unique, prefixed ID, and the file name (minus the .svg). Like:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><g id="shape-codepen">
```

### [\#](https://css-tricks.com/svg-sprites-use-better-icon-fonts/#article-header-id-1)Inject that SVG at the top of the document

Literally include it, like:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<html lang="en">

<head>
  ...
</head>

<body>
  <?php include_once("processed/svg-defs.svg"); ?>
```

Or however you want to do that.

It’s gotta be at the top, sadly, as there is [a Chrome bug](https://code.google.com/p/chromium/issues/detail?id=349175) in which this isn’t going to work if defined later. Although… there is more to this story because as I type these words, the theme this very site is using has the icons defined at the bottom of the document and it works. Ughkgh confusing.

### [\#](https://css-tricks.com/svg-sprites-use-better-icon-fonts/#article-header-id-2)Use the icons wherever

Now you can use them wherever! Like:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><svg viewBox="0 0 100 100" class="icon shape-codepen">
  <use xlink:href="#shape-codepen"></use>
</svg>
```

Note that [grunt-svgstore](https://github.com/FWeinb/grunt-svgstore) is now using `<symbol>` so you don’t even need to use the viewBox!

Make sure you use those class names on the svg to size it.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">/* Do whatever makes sense here.
   Just know that the svg will be an 
   enormous 100% wide if you don't 
   reign in the width. */
.icon {
  display: inline-block;
  width: 25px;
  height: 25px;
}
```

### [\#](https://css-tricks.com/svg-sprites-use-better-icon-fonts/#article-header-id-3)Yay: you can style them (and their parts) with CSS

One of the reasons we loved icon fonts is the ability to style them with CSS. This technique one-ups that in that we do everything we could there, and more, because:

1. We can style all the separate parts
2. SVG has even more things you can control, like special filters and strokes

The svg is (kinda) in the DOM, so JavaScript too. Here’s some styling possibilities and a demo of this all at work:

<figure><iframe allowfullscreen="true" height="337" src="https://codepen.io/chriscoyier/embed/EBHlD?height=337&theme-id=1&slug-hash=EBHlD&default-tab=result&user=chriscoyier&name=cp_embed_1"></iframe></figure>### [\#](https://css-tricks.com/svg-sprites-use-better-icon-fonts/#article-header-id-4)Another way: IcoMoon

[IcoMoon](http://icomoon.io/app/), which is known for producing icon fonts, actually does a fantastic job of producing SVG sprites as well. After selecting all the fonts you want, click the SVG button on the bottom and you’ll get that output, including a demo page with the inline SVG method.

<figure class="wp-block-image">![](https://css-tricks.com/wp-content/uploads/2014/03/icomoon-out.jpg)</figure>### [\#](https://css-tricks.com/svg-sprites-use-better-icon-fonts/#article-header-id-5)Browser Support

On the browser support front, the danger zones are IE 8 and down, Safari 5 and down, iOS 4.3 and down, and Android 2.3 and down. But if your policy is “the last two major versions” – you’re looking at pretty much 100% support.

Remember that icons can be used as a supporting role only, like always accompanied by a word. If that’s the case, support isn’t too big of a deal. If these are stand-alone, and non-display would make the site unusable, that’s a big deal.

I probably would go for icon fonts, as the support there is much deeper. Just make sure you [do it up right](https://css-tricks.com/html-for-icon-font-usage/).

### [\#](https://css-tricks.com/svg-sprites-use-better-icon-fonts/#article-header-id-6)This is going to get a lot better

Ideally we’d be able to do this:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><svg viewBox="0 0 100 100" class="icon shape-codepen">
  <use xlink:href="/images/svg-defs.svg#shape-codepen"></use>
</svg>
```

This *does* work in some browsers, meaning you could skip the include at the top of the document. Doing it this way means an extra HTTP request, but that means you can utilize caching more efficiently (not bloat document caching). In testing, Jonathan Neal discovered you need to have the xmlns attribute on the `<svg>` for it to work:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><svg xmlns="http://www.w3.org/2000/svg">
```

But even then, no support in any IE. **Unless** you wanted to swap out the whole `<svg><use>` with an `<object>`, which does work. Jonathan Neal again [figured this out](view-source:http://sandbox.thewikies.com/svg/):

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">/MSIE|Trident/.test(navigator.userAgent) && document.addEventListener('DOMContentLoaded', function () {
  [].forEach.call(document.querySelectorAll('svg'), function (svg) {
    var use = svg.querySelector('use'); 

    if (use) {
      var object = document.createElement('object');
      object.data = use.getAttribute('xlink:href');
      object.className = svg.getAttribute('class');
      svg.parentNode.replaceChild(object, svg);
    }
  });
});
```

[His demo](view-source:http://sandbox.thewikies.com/svg/) now also has a method which makes an Ajax request for the contents and injects that, which allows the fills to work in IE 9. Not as efficient, but more like a polyfill.

I imagine someday straight up `<svg><use>` linking right to the .svg will be the way to go. Or even perhaps `<img>` working with URL fragment identifiers on the SVG.

- - - - - -

Browsers treat `<use>` like the shadow DOM:

<figure class="wp-block-image">![](https://css-tricks.com/wp-content/uploads/2014/03/svg-shadow.png)</figure>Right now, we can target, say, an individual `<path>` with CSS, like:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.targetting-a-path {
  fill: red;
}
```

But that will affect *all* instances of that path. You’d think you could do:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">svg.shape-version-2 .targetting-a-path {
  fill: red;
}
```

But that doesn’t work. It crosses that shadow DOM boundary. Ideally you’d use the “hat” selector to break that:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">svg.shape-version-2 ^ .targetting-a-path {
  fill: red;
}
```

But that’s not supported yet either and it’s not entirely clear if that’s exactly how that will work or not.

### [\#](https://css-tricks.com/svg-sprites-use-better-icon-fonts/#article-header-id-7)“Versus” icon fonts

**Vector-based:** tie

**Style with CSS:** slight edge to SVG sprites (targeting parts, SVG specific styling like strokes)

**Weird failures:** SVG seems to just work (when supported). Icon fonts seem to fail in weird ways. For instance, you map the characters to normal letters, then the font loading fails and you get random characters abound. Or you map to “Private Use Area” and some browsers decide to re-map them to [really weird characters like roses](https://css-tricks.com/wp-content/uploads/2014/03/icon-font-fail.png), but it’s hard to replicate. Or you want to host the @font-face files on a CDN, but that’s cross-origin and Firefox hates that, so you need your server to serve the right cross-origin headers, but your Nginx setup isn’t picking that up right, SIGH. SVG wins this one.

**Semantics:** Not a huge deal, but I think an `<svg>` makes a bit more sense for an image than a `<span>`.

**Accessibility:** Maybe someone can tell me? Can we/should we give the `<svg>` a title attribute or something? Or a `<text>` element inside that we visually hide? **Update:** the `<title>` element [might do](https://twitter.com/dirkschulze/status/443769999153303552). Or perhaps the `<desc>` element as used in this [SVG access spec](http://www.w3.org/TR/SVG-access/#Fig-3.5).

**Ease of use:** Tools like [Fontello](http://fontello.com/) and [IcoMoon](http://icomoon.io/) are pretty good for an icon font workflow, but the folder-full-of-SVGs with Grunt squishing them together for you is even easier, I think.

- - - - - -

Ian Feather [posted an article](http://ianfeather.co.uk/ten-reasons-we-switched-from-an-icon-font-to-svg/) about why they switched away from icon fonts as well and I agree with every single point.