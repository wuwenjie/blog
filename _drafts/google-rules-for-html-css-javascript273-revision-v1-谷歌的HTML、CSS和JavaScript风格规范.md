---
id: 275
title: 谷歌的HTML、CSS和JavaScript风格规范
date: '2019-08-16T19:47:28+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/273-revision-v1/'
permalink: '/?p=275'
---

Are you forced to adhere with company coding guidelines set in stone at some point in 1969? Or are your fellow developers free to express their artistic side with embedded ASCII art?

I like browsing through style guidelines. There are usually obvious rules, those which seem bizarre and a few hidden gems which you hadn’t discovered before. Unfortunately, few companies are brave enough to publish internal guidelines. The [BBC revealed their documents in 2010](http://www.sitepoint.com/bbc-css-coding-standards/) but [Google has finally released the style guides](https://code.google.com/p/google-styleguide/) the company uses for internal projects.

Rules are provided for C++, Objective C, Python, XML, and R — but we’re most interested in HTML, CSS and JavaScript. The documents are refreshingly short. I’ve seen too many where developers are expected to learn 1,001 arcane and subjective rules which show-off the writer’s knowledge rather than imparting useful advice.

Let’s take a closer look…

## [HTML](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml)

There are few surprises in the [HTML style guide](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml):

- serve semantic HTML5 as text/html
- separate markup, styling and scripting
- validate your markup when possible
- use fall-backs when technologies such as `canvas` aren’t supported

Google recommends shaving every byte: files should be UTF-8 encoded, trailing white space should be trimmed and you should avoid entity references such as &amp;mdash; and &amp;rdquo;. They even recommend omitting optional structures and closing tags, e.g.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">

<title>Saving money, saving bytes</title>
<p>Qed.
```

Uggh. I’m all for saving a few bytes but still prefer a stricter XHTML-like syntax.

Strangely, Google recommends indenting with two spaces rather than a tab. Isn’t that twice the quantity of whitespace characters you need?!

## [CSS](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml#CSS_Style_Rules)

[Stylesheet guidelines](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml#CSS_Style_Rules) are dominated by further best-practice and byte-saving techniques:

- use valid CSS unless dealing with bugs or proprietary syntaxes (vendor prefixes)
- use short ID and class names, but ensure they’re not cryptic or presentational (e.g. #blue-button)
- consider prefixed names for larger projects, e.g. #xyz-help, .xyz-column
- simplify selectors when possible, e.g. #example rather than ul#example
- use shorthand syntax notations
- do not use quotes in `url()` or unit values after a zero
- use short #abc hexadecimal color notation rather than #aabbcc
- end every declaration with a semi-colon (even the last one)
- avoid browser hacks

Google suggests omitting leading zeros from measurements, such as .5em. It saves a character, but I prefer code which is easier to scan.

The guide recommends a newline for every comma-separated selector which applies to a block. I tend to agree, although I’m guilty of using long lists of selector strings.

Finally, it states that property declarations should be alphabetized, e.g.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
#example
{
	border: 1px solid #000;
	border-radius: 6px;
	display: block;
	font-family: sans-serif;
	margin: 1em;
	outline: 0;
	padding: 10px;
	text-align: center;
}
```

Personally, I prefer to order properties in related chunks, such as block type/dimensions, fonts, text alignment, padding, margins, color, background, border and miscellaneous properties. Perhaps it would confuse some developers but I’ve used the method for many years and works for me.

## [JavaScript](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)

Understandably, the [JavaScript style guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml) is longer but eases you in with the basics:

- always use `var` to declare variables
- always end lines with a semi-colon
- use standard rather that non-standard features
- use camelCaseNames and UPPERCASE constants, but avoid the `const` keyword which isn’t supported in IE
- use namespacing techniques
- avoid `eval()` except for deserialization (strangely, JSON.Parse isn’t mentioned as an alternative)
- avoid `with()` on objects and `for-in` on arrays
- use array and object literals rather than more verbose declarations
- be aware of [truthy and falsy rules](http://www.sitepoint.com/javascript-truthy-falsy/)
- do not use IE conditional comments in your JavaScript source
- don’t modify the prototypes of built-in objects — that’s a shame since it’s one of JavaScripts more powerful features, but you know it’ll lead to problems
- use closures carefully and do not introduce circular references
- similarly, [be careful using ‘this’](http://www.sitepoint.com/javascript-this-gotchas/)

There was one unusual recommendation: don’t use functions within blocks, i.e. write:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
if (x) {
  var foo = function() {}
}
```

rather than:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
if (x) {
  function foo() {}
}
```

You’ll see the second syntax used everywhere. It works without problems but it’s not valid ECMAScript.

The document also mentions that wrapper objects should never be used for primitives unless you’re typecasting. It can lead to unexpected results such as:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
var x = new Boolean(false);
if (x) {
	// this code evaluates
}
```

Dumb JavaScript.

Finally, the guide states you should use single quotes (‘) in preference to double quotes (“) when delimiting strings which *could* contain HTML attributes. I use double quotes everywhere and I’m not sure I can change the habit easily. That said, I use single quotes for static PHP strings, so perhaps I’m being picky!

The best advice is saved for the end of the documents: be consistent. Developers will rarely agree with every programming guideline but you should follow your own rules. It’ll make life easier for others trying to understand your code and when you when return to it six months later.

Do you agree with [Google’s Style Guides](https://code.google.com/p/google-styleguide/)? Does your company force you to follow strange development practices?