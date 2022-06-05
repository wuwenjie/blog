---
id: 187
title: 回流与重绘：CSS性能会让JavaScript变慢？
date: '2009-12-11T22:29:42+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=187'
permalink: /2009/12/css-make-js-slowly
categories:
    - 'HTML&amp;CSS'
---

I’ve been [tweeting](http://twitter.com/stubbornella/statuses/1131777322) and [posting to delicious about reflows and repaints](http://delicious.com/stubbornella/reflow), but hadn’t mentioned either in a talk or blog post yet.

I first started thinking about reflows and repaints after a [firey exchange](http://www.flickr.com/photos/kiouv/3035720145/) with [Mr. Glazman](http://www.glazman.org/weblog/) at [ParisWeb](http://www.paris-web.fr/2008/). I may be stubborn, but I did actually listen to his arguments. 🙂 [Stoyan](http://www.phpied.com/) and I began discussing ways to quantify the problem.

Going forward the performance community needs to partner more with browser vendors in addition to our more typical black box experiments. Browser makers know what is costly or irrelevant in terms of performance. *Opera lists [repaint and reflow as one of the three main contributors to sluggish JavaScript](http://dev.opera.com/articles/view/efficient-javascript/?page=3#reflow), so it definitely seems worth a look.*

Let’s start with a little background information. A *repaint* occurs when changes are made to an elements skin that changes visibility, but do not affect its layout. Examples of this include outline, visibility, or background color. According to Opera, repaint is expensive because the browser must verify the visibility of all other nodes in the DOM tree. A *reflow* is even more critical to performance because it involves changes that affect the layout of a portion of the page (or the whole page). Reflow of an element causes the subsequent reflow of all child and ancestor elements as well as any elements following it in the DOM.

For example:

```
<pre class="wp-block-preformatted"><body>
<div class=”error”>
	<h4>My Module</h4>
	<p><strong>Error:</strong>Description of the error…</p>
	<h5>Corrective action required:</h5>
	<ol>
		<li>Step one</li>
		<li>Step two</li>
	</ol>
</div>
</body>
```

In the html snippet above, a reflow on the paragraph would trigger a reflow of the strong because it is a child node. It would also cause a reflow of the ancestors (div.error and body – depending on the browser). In addition, the h5 and ol would be reflowed simply because they follow that element in the DOM. According to Opera, most reflows essentially cause the page to be re-rendered:

> Reflows are very expensive in terms of performance, and is one of the main causes of slow DOM scripts, especially on devices with low processing power, such as phones. In many cases, they are equivalent to laying out the entire page again.

### So, if they’re so awful for performance, what causes a reflow?

Unfortunately, lots of things. Among them some which are particularly relevant when writing CSS:

- Resizing the window
- Changing the font
- Adding or removing a stylesheet
- Content changes, such as a user typing text in  
    an input box
- Activation of CSS pseudo classes such as :hover (in IE the activation of the pseudo class of a sibling)
- Manipulating the class attribute
- A script manipulating the DOM
- Calculating offsetWidth and offsetHeight
- Setting a property of the style attribute

[Mozilla article about reflows](http://www.mozilla.org/newlayout/doc/reflow.html) that outlines causes and when they could be reduced.

### How to avoid reflows or at least minimize their impact on performance?

Note: I’m limiting myself to discussing the CSS impact of reflows, if you are a JavaScripter I’d definitely recommend reading my [reflow links](http://delicious.com/stubbornella/reflow), there is some really good stuff there that isn’t directly related to CSS.

1. [Change classes on the element you wish to style (as low in the dom tree as possible)](http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/#low)
2. [Avoid setting multiple inline styles](http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/#inline)
3. [Apply animations to elements that are position fixed or absolute](http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/#animations)
4. [Trade smoothness for speed](http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/#smooth)
5. [Avoid tables for layout](http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/#tables)
6. [Avoid JavaScript expressions in the CSS](http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/#expressions) (IE only)

#### **Change classes as low in the dom tree as possible**

Reflows can be top-down or bottom-up as reflow information is passed to surrounding nodes. Reflows are unavoidable, but you can reduce their impact. *Change classes as low in the dom tree as possible*and thus limit the scope of the reflow to as few nodes as possible. For example, you should avoid changing a class on wrapper elements to affect the display of child nodes. Object oriented css always attempts to attach classes to the object (DOM node or nodes) they affect, but in this case it has the added performance benefit of minimizing the impact of reflows.

#### **Avoid setting multiple inline styles**

We all know interacting with the DOM is slow. We try to group changes in an invisible DOM tree fragment and then cause only one reflow when the entire change is applied to the DOM. Similarly, setting styles via the style attribute cause reflows. *Avoid setting multiple inline styles* which would each cause a reflow, the styles should be combined in an external class which would cause only one reflow when the class attribute of the element is manipulated.

#### **Apply animations with position fixed or absolute**

*Apply animations to elements that are position fixed or absolute*. They don’t affect other elements layout, so they will only cause a repaint rather than a full reflow. This is much less costly.

#### **Trade smoothness for speed**

Opera also advises that we *trade smoothness for speed*. What they mean by this is that you may want to move an animation 1 pixel at a time, but if the animation and subsequent reflows use 100% of the CPU the animation will seem jumpy as the browser struggles to update the flow. Moving the animated element by 3 pixels at a time may seem slightly less smooth on very fast machines, but it won’t cause CPU thrashing on slower machines and mobile devices.

#### **Avoid tables for layout (or set table-layout fixed)**

*Avoid tables for layout*. As if you needed *another* reason to avoid them, tables often require multiple passes before the layout is completely established because they are one of the rare cases where elements can affect the display of other elements that came before them on the DOM. Imagine a cell at the end of the table with very wide content that causes the column to be completely resized. This is why tables are not rendered progressively in all browsers (thanks to Bill Scott for this tip) and *yet another* reason why they are a bad idea for layout. According to Mozilla, [even minor changes will cause reflows of all other nodes in the table](http://www.mozilla.org/newlayout/doc/reflow.html).

Jenny Donnelly, the owner of the YUI data table widget, recommends using a fixed layout for data tables to allow a more efficient layout algorithm. Any value for table-layout other than “auto” will trigger a fixed layout and allow the table to render row by row according to the CSS 2.1 specification. Quirksmode shows that [browser support for the table-layout property](http://www.quirksmode.org/css/tables.html#tablelayout) is good across all major browsers.

> In this manner, the user agent can begin to lay out the table once the entire first row has been received. Cells in subsequent rows do not affect column widths. Any cell that has content that overflows uses the ‘overflow’ property to determine whether to clip the overflow content.
> 
> [Fixed layout, CSS 2.1 Specification](http://www.w3.org/TR/CSS21/tables.html#fixed-table-layout)

> This algorithm may be inefficient since it requires the user agent to have access to all the content in the table before determining the final layout and may demand more than one pass.
> 
> [Automatic layout, CSS 2.1 Specification](http://www.w3.org/TR/CSS21/tables.html#auto-table-layout)

#### **Avoid JavaScript expressions in the CSS**

This rule is an oldie but goodie. The main reason these **expressions are so costly** is because they are recalculated each time the document, or part of the document, reflows. As we have seen from all the *many* things that trigger a reflow, it can occur thousands and thousands of times per second. Beware!

### Further study

The Yahoo! Exceptional Performance team ran an experiment to determine the optimal method to include an external stylesheet. We recommended putting a link tag in the head because, while it was one second slower (6.3 to 7.3 seconds) all the other methods blocked progressive rendering. While progressive rendering is non-negotiable (users *hate* staring at a blank screen), it does make me curious about the effects of rendering, repaints, reflows and resulting CPU thrashing on component download and overall response time. If we could reduce the number of reflows during loading could we maybe gain back a tenth of the lost time (100ms)? What if it was as much as half?

At SXSW I was trying to convince [Steve](http://stevesouders.com/) that reflows are important by telling him about an experiment I’ve been meaning to run for a long time, but just haven’t had time. I do hope someone can pick up where I left off (hint! hint!). While loading the page I’d like to intentionally trigger reflows at various rates. This could perhaps be accomplished by toggling a class name on the body (experiment) versus the last child of the body with no descendants (control). By comparing the two, and increasing in the number of reflows per second, we could correlate reflows to response time. Measuring the impact of reflows on JS responsiveness will be harder because anything we do to trigger the reflows will likely impact the experiment.

In the end, quantifying the impact is only mildly interesting, because browser vendors are telling us it matters. Perhaps more interesting is to focus on what causes reflows and how to avoid them. That will require better tools, so I challenge browser vendors and the performance community to work together to make it a reality!

### See it in action

Perhaps you are a visual person? These videos are a really cool visualization of the reflow process.

1. <http://www.youtube.com/watch?v=nJtBUHyNBxs>
2. <http://www.youtube.com/watch?v=ZTnIxIA5KGw>
3. <http://www.youtube.com/watch?v=dndeRnzkJDU>

### Reflow gone amok

In order to improve performance browser vendors may try to limit reflows from affecting adjacent nodes or combine several reflows into one larger change such as [Mozilla’s dirty reflows](http://www.mozilla.org/newlayout/doc/reflow.html). This can improve performance, but sometimes it can also cause display problems. You can use what we’ve learned about reflows and trigger them when necessary to correct related display problems.

For example, when toggling between tabs on our image optimization site, [http://smush.it](http://smush.it/), the height of the content is variable from tab to tab. Occasionally the shadow gets left behind as it is several ancestor nodes above the content being toggled and its container may not be reflowed. This image is simulated because the bug is difficult to catch on camera as any attempts to shoot it cause the reflow that corrects it. If you find yourself with a similar bug, move the background images to DOM elements *below* the content being toggled.

<figure class="wp-block-image">[![Smush.it! with un-reflowed shadows](http://www.stubbornella.org/content/wp-content/uploads/2009/03/smushit-reflow-problem-300x252.png)](http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/smushit-reflow-problem/)</figure>Smush it on tab change in Firefox only.

Another example is dynamically adding items to an ordered list. As you increase from 9 to 10 items or 99 to 100 items the numbers in the list will no longer line up properly across all navigators. When the total number increases by an order of magnitude and the browser doesn’t reflow siblings, the alignment is broken. Quickly toggling the display of the entire list or adding a class, even if it has no associated styles, will cause a reflow and correct the alignment.

### Tools

A few tools have made waves lately. Stoyan Stefanov and I have been looking for decent ways to measure reflows and repaints and there are a few tools which show promise (despite being very early alpha). Beware, some of these seriously destroyed my browser before I got them working correctly. In most cases you’ll need to have installed the latest nightly builds.

When [Mozilla announced the MozAfterPaint](https://developer.mozilla.org/web-tech/2008/10/13/mozafterpaint/) Firefox API, the internets were abuzz.

- UPDATE: Lindsey Simon at Google wrote a [bookmarklet that tests reflow times on any browser](http://reflowr.appspot.com/). Fantastic. (Note: all that shaking is normal!)
- John Resig wrote a [bookmarklet to visualize paint events](http://ejohn.org/blog/browser-paint-events/).
- Kyle Scholz created this tool to [visualize paint events before onload](http://www.kylescholz.com/blog/2008/11/digging_into_paint_events_in_f_1.html).
- Alex at Yoono has created the [XUL profiler](http://blog.yoono.com/blog/2008/12/download-yoono-xulprofiler/).

Has anyone else seen any cool tools for evaluating reflows? Please send them my way!

And a couple other tools not directly dealing with reflows.

- [IE Visual Roundtrip Analyzer (VRTA)](http://www.microsoft.com/downloads/details.aspx?FamilyID=119f3477-dced-41e3-a0e7-d8b5cae893a3&displaylang=en), because if Microsoft named it something fun we might actually want to use it. 🙂
- [Performance Profiling Tools for WPF](http://msdn.microsoft.com/en-us/library/aa969767.aspx).

Ultimately, we need a cross browser tool to quantify and reduce reflows and repaints. I’m hoping that the performance community can partner with browser vendors to make this tool a reality. The browser vendors have been telling us for a while that this was where we needed to look next, the ball is in our court.

</body>