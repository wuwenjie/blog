---
id: 306
title: HTML的书写规范
date: '2013-01-09T22:07:02+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=306'
permalink: /2013/01/html-coding-rules
categories:
    - 'HTML&amp;CSS'
---

The following document outlines a reasonable style guide for HTML development. These guidelines strongly encourage the use of existing, common, sensible patterns. They should be adapted as needed to create your own style guide.

This is a living document and new ideas are always welcome. Please contribute.

## [](https://github.com/necolas/idiomatic-html#table-of-contents)Table of contents

1. [General principles](https://github.com/necolas/idiomatic-html#general-principles)
2. [Whitespace](https://github.com/necolas/idiomatic-html#whitespace)
3. [Format](https://github.com/necolas/idiomatic-html#format)
4. [Attribute order](https://github.com/necolas/idiomatic-html#attribute-order)
5. [Naming](https://github.com/necolas/idiomatic-html#naming)
6. [Practical example](https://github.com/necolas/idiomatic-html#example)

[License](https://github.com/necolas/idiomatic-html#license)

## [](https://github.com/necolas/idiomatic-html#1-general-principles)1. General principles

- All code in any code-base should look like a single person typed it, no matter how many people contributed.
- Strictly enforce the agreed upon style.
- If in doubt when deciding upon a style, use existing, common patterns.

## [](https://github.com/necolas/idiomatic-html#2-whitespace)2. Whitespace

Only one style should exist across the entire source of your code-base. Always be consistent in your use of whitespace. Use whitespace to improve readability.

- Never mix spaces and tabs for indentation.
- Choose between soft indents (spaces) or real tabs. Stick to your choice without fail. (Preference: spaces)
- If using spaces, choose the number of characters used per indentation level. (Preference: 4 spaces)

Tip: configure your editor to “show invisibles”. This will allow you to eliminate end of line whitespace, eliminate unintended blank line whitespace, and avoid polluting commits.

## [](https://github.com/necolas/idiomatic-html#3-format)3. Format

- Always use lowercase tag and attribute names.
- Write one discrete element per line.
- Use one additional level of indentation for each nested element.
- Use valueless boolean attributes (e.g. `checked` rather than `checked="checked"`).
- Always use double quotes to quote attribute values.
- Omit the `type` attributes from `link` stylesheet, `style` and `script` elements.
- Always include closing tags.
- Don’t include a trailing slash in self-closing elements.

(Keep line-length to a sensible maximum, e.g., 80 columns.)

Example:

```
<pre class="wp-block-preformatted"><div class="tweet">
    <a href="path/to/somewhere">
        <img src="path/to/image.png" alt="">
    </a>
    <p>[tweet text]</p>
    <button disabled>Reply</button>
</div>
```

### [](https://github.com/necolas/idiomatic-html#exceptions-and-slight-deviations)Exceptions and slight deviations

Elements with multiple attributes can have attributes arranged across multiple lines in an effort to improve readability and produce more useful diffs.

Example:

```
<pre class="wp-block-preformatted"><a class="[value]"
 data-action="[value]"
 data-id="[value]"
 href="[url]">
    <span>[text]</span>
</a>
```

## [](https://github.com/necolas/idiomatic-html#4-attribute-order)4. Attribute order

HTML attributes should be listed in an order that reflects the fact that class names are the primary interface through which CSS and JavaScript select elements.

1. `class`
2. `id`
3. `data-*`
4. Everything else

Example:

```
<pre class="wp-block-preformatted"><a class="[value]" id="[value]" data-name="[value]" href="[url]">[text]</a>
```

## [](https://github.com/necolas/idiomatic-html#5-naming)5. Naming

Naming is hard, but very important. It’s a crucial part of the process of developing a maintainable code base, and ensuring that you have a relatively scalable interface between your HTML and CSS/JS.

- Use clear, thoughtful, and appropriate names for HTML classes. The names should be informative both within HTML and CSS files.
- Avoid *systematic* use of abbreviated class names. Don’t make things difficult to understand.

Example with bad names:

```
<pre class="wp-block-preformatted"><div class="cb s-scr"></div>
```

```
<pre class="wp-block-preformatted">.s-scr {
  overflow: auto;
}

.cb {
  background: #000;
}
```

Example with better names:

```
<pre class="wp-block-preformatted"><div class="column-body is-scrollable"></div>
```

```
<pre class="wp-block-preformatted">.is-scrollable {
    overflow: auto;
}

.column-body {
    background: #000;
}
```

## [](https://github.com/necolas/idiomatic-html#6-practical-example)6. Practical example

An example of various conventions.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<html>
    <head>
        <meta charset="utf-8">
        <title>Document</title>
        <link rel="stylesheet" href="main.css">
        <script src="main.js"></script>
    </head>
    <body>
        <article class="post" id="1234">
            <time class="timestamp">March 15, 2012</time>
            <a data-id="1234"
             data-analytics-category="[value]"
             data-analytics-action="[value]"
             href="[url]">[text]</a>
            <ul>
                <li>
                    <a href="[url]">[text]</a>
                    <img src="[url]" alt="[text]">
                </li>
                <li>
                    <a href="[url]">[text]</a>
                </li>
            </ul>

            <a class="link-complex" href="[url]">
                <span class="link-complex__target">[text]</span>
                [text]
            </a>

            <input value="text" readonly>
        </article>
    </body>
</html>
```

</body></html>