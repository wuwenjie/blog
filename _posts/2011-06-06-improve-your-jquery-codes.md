---
id: 376
title: 25个常规方法优化你的jquery代码
date: '2011-06-06T12:29:52+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=376'
permalink: /2011/06/improve-your-jquery-codes
categories:
    - Javascript
---

**1. 从Google Code加载jQuery**Google Code上已经托管了多种JavaScript类库，从Google Code上加载jQuery比直接从你的服务器加载更有优势。它**节省了你服务器上的带宽**，能够很快的从Google的内容分布网络(CDN)上加载JS类库。更重要的是，如果用户访问那些发布在Google Code上的站点后它会被**缓存下来。**  
这样做很有意义。有多少站点使用了没有被缓存的相同jQuery副本，而这些很容易做到,引入:

&lt;script type=”text/javascript” src=” http://ajax.googleapis.com/ajax/libs/jquery/1.4/jquery.min.js”&gt;&lt;/script&gt;

 **2. 使用备忘单**不仅仅是jQuery，很多编程语言也有类似的备忘单，在一张A4的纸上就可以很容易看到每个函数的用法。幸运的是已经有好心的家伙把jQuery的备忘单做得很完善了：  
<http://www.gscottolson.com/weblog/2008/01/11/jquery-cheat-sheet/>  
<http://colorcharge.com/jquery/>

**3. 整合所有的脚本并缩减它们**不错，这是JavaScript的一个常见技巧。可是一个使用了jQuery的大项目可能使用了很多相关的jQuery插件(本站就使用了easing,localScroll,lightbox,preload)，因此它通常是适用的。浏览器不能同时加载JS脚本（大多数情况下），这意味着如果你同一时间加载很多脚本的话，将减缓页面的加载速度。因此，如果每个页面都要加载这些脚本，你应该考虑在发布之前将这些脚本整合成一个稍大的JS脚本。一些jQuery插件已经最小化了，但是你应该打包你的JS脚本和那些没有缩减过的脚本，这仅需要几秒的时间就可以完成。  
就个人而言，我推荐[Packer by Dean Edwards](http://dean.edwards.name/packer/)

**4. 使用Firebug出色的控制台日志工具**如果你还没有安装Firebug，那么你真的应该把它装上。除了许多其它有用的特性（比如允许你检查http传输情况、发现你的CSS问题），它也有极好的日志命令，允许你很容易调试JS脚本。  
这里有Firebug所有[特性的详细说明](http://getfirebug.com/console.html)。  
我最喜欢的特性有”[console.info](http://console.info/)“，通过它你可以把信息和变量值输出到控制台上，而不必使用alert；”console.time”则允许你在一组代码上设置定时器，从而计算出JS脚本运行所花费的时间。这一切都很容易做到:  
<a>复制代码</a>代码如下:  
console.time(‘create list’);   
for (i = 0; i &lt; 1000; i++) {   
var myList = $(‘.myList’);   
myList.append(‘This is list item ‘ + i);   
}   
console.timeEnd(‘create list’);

**5. 通过缓存最小化选择操作** jQuery的选择器棒极了。它们可以在页面上以极其简单的方法找到任何元素，但是在内部它们必须通过大量的步骤才可以实现选择操作，如果你错误的使用它们，那么你可能发现一切都变得相当慢。  
如果你一次又一次的选择相同元素(例如在一个循环中)，那么你可以一次选择出它并放入内存中，同时你可以在核心内容里操作它。看下面的例子，这里我们利用循环往UL里添加条目：  
<a>复制代码</a>代码如下:  
for (i = 0; i &lt; 1000; i++) {   
var myList = $(‘.myList’);   
myList.append(‘This is list item ‘ + i);   
}

这在我的PC上Firefox 3花费了1066毫秒时间（可以设想一下在IE6中的情况！），对JavaScript而言这个操作相当慢。现在让我们来看看下面的代码，这里我们仅使用了一次选择操作：  
<a>复制代码</a>代码如下:  
var myList = $(‘.myList’);   
for (i = 0; i &lt; 1000; i++) {   
myList.append(‘This is list item ‘ + i);   
}

仅仅用了224毫秒，通过移动一行代码就快了将近4倍。

**6. 最小化DOM操作**我们通过减少对DOM的插入操作可以让上面的代码运行得更快。DOM的插入操作（像.append(),.prepend(),.after(),.wrap()）是相当耗时的，执行这些操作会拖慢程序的运行。  
我们所要做的就是使用字符串拼接来构造一个list项并用一个函数往列表里添加这些项，比如.html()。请看下面的例子：  
<a>复制代码</a>代码如下:  
var myList = $(‘#myList’);   
for (i=0; i&lt;1000; i++){   
myList.append(‘This is list item ‘ + i);   
}

在我的PC上花费了216毫秒，仅仅在1/5秒左右。但是如果我们使用字符串构造list项，使用下面的HTML方法完成插入操作：   
<a>复制代码</a>代码如下:  
var myList = $(‘.myList’);   
var myListItems = ”;   
for (i = 0; i &lt; 1000; i++) {   
myListItems += ‘&lt;li&gt;This is list item ‘ + i + ‘&lt;/li&gt;’;   
}   
myList.html(myListItems);

它耗时185毫秒，尽管没有快很多，但是也提高了31毫秒的时间。  
**7. 处理DOM插入操作时，将需要的内容包装在一个元素中** 嗯，不要问我为什么要这样做（我相信一个有相当经验的程序员会给你解释）。  
在上面的例子中我们使用.html()将1000个item项插入到UL中。如果在插入操作之前我们将这些项包装在UL标签中，然后把完整的UL插入到另一个DIV标签中，那么我们实际上仅仅插入一个标签而不是1000个，这看起来要更高效些。请看下面这个例子：  
<a>复制代码</a>代码如下:  
var myList = $(‘.myList’);   
var myListItems = ‘&lt;ul&gt;’;

for (i = 0; i &lt; 1000; i++) {   
myListItems += ‘&lt;li&gt;This is list item ‘ + i + ‘&lt;/li&gt;’;   
}   
myListItems += ‘&lt;/ul&gt;’;   
myList.html(myListItems);

现在所花费的时间仅19毫秒，比我们之前的第一个例子明显提高了50倍。   
**8. 尽可能使用ID而不是class** jQuery利用classes进行DOM元素选择操作与通过ID进行选择一样容易，因此与之前相比更自由的使用classes进行元素选择操作很有吸引力。不过由于jQuery使用浏览器固有的方法（getElementById ）进行选择操作，因此利用ID进行选择操作更有优势。有多快呢？让我们来看看。  
我使用前一个例子，修改它以便于我们创建的每个LI有一个唯一的class。然后我将遍历之，每次选择一个元素：  
<a>复制代码</a>代码如下:  
// Create our list   
var myList = $(‘.myList’);   
var myListItems = ‘&lt;ul&gt;’;   
for (i = 0; i &lt; 1000; i++) {   
myListItems += ‘&lt;li class=”listItem’ + i + ‘”&gt;This is a list item&lt;/li&gt;’;   
}   
myListItems += ‘&lt;/ul&gt;’;   
myList.html(myListItems);   
// Select each item once   
for (i = 0; i &lt; 1000; i++) {   
var selectedItem = $(‘.listItem’ + i);   
}

正如所想的，我的浏览器花费了5066毫秒的时间（5秒多）。因此我修改上述代码以使用ID而不是class，然后通过ID进行选择。   
<a>复制代码</a>代码如下:  
// Create our list   
var myList = $(‘.myList’);   
var myListItems = ‘&lt;ul&gt;’;   
for (i = 0; i &lt; 1000; i++) {   
myListItems += ‘&lt;li id=”listItem’ + i + ‘”&gt;This is a list item&lt;/li&gt;’;   
}   
myListItems += ‘&lt;/ul&gt;’;   
myList.html(myListItems);   
// Select each item once   
for (i = 0; i &lt; 1000; i++) {   
var selectedItem = $(‘#listItem’ + i);   
}

仅仅耗时61毫秒，几乎快了100倍

**9. 给选择器提供上下文**默认情况下，当你使用类似$(‘.myDiv’)的选择器时将在整个DOM文档查找元素，这有很大的代价。  
当执行选择操作时，jQuery函数可以指定第二个参数：jQuery( expression, context )通过给选择器提供一个上下文，那就会在这个context中进行元素查找，而不必在整个DOM文档中查找元素。  
为了解释这个，我们采用前面的第一段代码。它创建一个有1000项内容的UL，每项都有一个单独的class。  
然后遍历之每次选择一项。你应该记得通过class选择所有的1000项item需要耗时5秒多。  
<a>复制代码</a>代码如下:  
var selectedItem = $(‘#listItem’ + i);

然后我给其添加一个上下文，以便于仅在UL中执行选择操作：   
<a>复制代码</a>代码如下:  
var selectedItem = $(‘#listItem’ + i, $(‘.myList’));

由于效率太差，仍耗时3818毫秒的时间，但是通过一个很小的修改仍获得了25%的速度提升。   
**10. 正确使用方法链** jQuery最炫的一个特性就是jQuery能够连续的进行方法调用。举例来说，你想去切换元素的class：  
<a>复制代码</a>代码如下:  
$(‘myDiv’).removeClass(‘off’).addClass(‘on’);

如果你像我这样，你可能在前五分钟的jQuery学习就可以更进一步使用它。首先它仍可以跨行操作(jQuery是JavaScript) ，这意味着你能够写出下面这样工整的代码：   
<a>复制代码</a>代码如下:  
$(‘#mypanel’)   
.find(‘TABLE .firstCol’)   
.removeClass(‘.firstCol’)   
.css(‘background’ : ‘red’)   
.append(‘&lt;span&gt;This cell is now red&lt;/span&gt;’);

使用链表的习惯将有助于你减少选择器的使用。然而可以更深入使用之，你想在一个元素上执行好几个函数，但是以某种方式改变了操作的元素：   
<a>复制代码</a>代码如下:  
$(‘#myTable’).find(‘.firstColumn’).css(‘background’,’red’);

我们选择了一个表格，在其中找到class为”firstColumn”的单元格，然后使之背景变为红色。   
现在我们希望将所有class为”lastColumn”的单元格背景设为蓝色。因为我们已经使用了find()函数过滤出class不为”firstColumn”的所有单元格，因此我们需要再一次对表格使用选择操作，我们难道不能连续进行方法调用吗？幸运的是jQuery提供了end()函数，这将匹配的元素列表变为前一次状态以便于你可以执行方法链表：   
<a>复制代码</a>代码如下:  
$(‘#myTable’)   
.find(‘.firstColumn’)   
.css(‘background’,’red’)   
.end()   
.find(‘.lastColumn’)   
.css(‘background’,’blue’);

写一个能够进行方法链式调用的自定义jQuery函数也很容易。你所做的就是要写个能修改元素并返回元素的函数。  
<a>复制代码</a>代码如下:  
$.fn.makeRed = function() {   
return $(this).css(‘background’, ‘red’);   
}   
$(‘#myTable’).find(‘.firstColumn’).makeRed().append(‘hello’);

它很简单吧！

**11. 学会正确使用效果**在我刚开始使用jQuery的时候，就很喜欢这一点：它可以很容易使用预定义好的各种动画效果，像slideDown()和fadeIn()之类的。由于jQuery提供的animate()方法十分易用和强大，我们很容易深入使用它。事实上，在jQuery源代码中不少方法就是通过animate()函数来实现效果的。  
<a>复制代码</a>代码如下:  
slideDown: function(speed,callback){   
return this.animate({height: “show”}, speed, callback);   
},

fadeIn: function(speed, callback){   
return this.animate({opacity: “show”}, speed, callback);   
}

animate()方法仅仅作用在CSS上，根据数值平滑的进行转换。因此你能够改变宽度、高度、透明度、背景色、top、left、margin、颜色、字体大小以及任何你想要的。   
给菜单项添加高度变化的效果是很容易做到的：   
<a>复制代码</a>代码如下:  
$(‘#myList li’).mouseover(function() {   
$(this).animate({“height”: 100}, “slow”);   
});

不像其他的jQuery函数，动画效果自动的排进队列，因此如果在第一个特效完成之后你想运行第二个特效，需要两次调用animate方法：   
<a>复制代码</a>代码如下:  
$(‘#myBox’).mouseover(function() {   
$(this).animate({ “width”: 200 }, “slow”);   
$(this).animate({“height”: 200}, “slow”);   
});

如果你想动画效果同时发生，那么需要将所有的styles作为一个参数对象传入方法中：   
<a>复制代码</a>代码如下:  
$(‘#myBox’).mouseover(function() {   
$(this).animate({ “width”: 200, “height”: 200 }, “slow”);   
});

你能够给值是数字的属性添加动画效果。你也可以下载插件帮助你给非数字值的属性添加动画效果，像[colors and background colors](http://plugins.jquery.com/project/color)

**12. 了解事件代理**与之前相比，jQuery能够更容易得向DOM元素无缝添加事件。这是很棒的特性，然而向元素添加太多的事件是效率很差的。在很多情况下事件代理允许你用少量的事件实现同样的目的。最好的解释方法就是使用实例：  
<a>复制代码</a>代码如下:  
$(‘#myTable TD’).click(function(){   
$(this).css(‘background’, ‘red’);   
});

当我们点击表格中的单元格时，上面的代码将使所有单元格背景变为红色。比方说，你有一个10列、50行的网格，那么就会绑定上500个事件。嗯，这时就是事件代理出场的时候了：   
<a>复制代码</a>代码如下:  
$(‘#myTable’).click(function(e) {   
var clicked = $(e.target);   
clicked.css(‘background’, ‘red’);   
});

e’包含了事件的信息，包括了实际接收到click事件的目标元素。我们所要做的就是检查是哪个单元格被点击了。相当的巧妙！   
事件代理带来了另外一个好处。正常情况下，在你往一个元素集合绑定一个事件，该事件仅仅只是绑定到这些集合元素上。如果你向DOM中添加了新的元素，尽管这些新元素被选择器所匹配，但是这些新元素并不会绑定上事件处理（你同意我的观点吗？），因此不会有事件发生。   
当使用事件代理时，你能够在事件被DOM绑定后仍然可以添加多个被匹配的元素到其中，而它们同样能够正常工作。  
**13. 利用classes存储状态** 这是在html中存储信息最基本的方法。jQuery擅长基于classes进行元素的操作，因此如果你需要存储元素的状态信息，为什么不试试使用额外的class来存储它呢？  
这里有一个例子。我们想创建一个展开的菜单。当你点击按钮时，我们希望通过slideDown()和slideUp()进行菜单的展开与收缩。请看下面的HTML：  
<a>复制代码</a>代码如下:  
&lt;div class=”menuItem expanded”&gt;   
&lt;div class=”button”&gt;   
click me   
&lt;/div&gt;   
&lt;div class=”panel”&gt;   
&lt;ul&gt;   
&lt;li&gt;Menu item 1&lt;/li&gt;   
&lt;li&gt;Menu item 2&lt;/li&gt;   
&lt;li&gt;Menu item 3&lt;/li&gt;   
&lt;/ul&gt;   
&lt;/div&gt;   
&lt;/div&gt;

非常的简单！我们仅仅向包装器DIV添加一个额外的class，它只是告诉我们item项的状态。因此在按钮点击之后我们所需要的只是click事件处理，这会执行相应的slideUp()和slideDown()方法。   
<a>复制代码</a>代码如下:  
$(‘.button’).click(function() {

var menuItem = $(this).parent();   
var panel = menuItem.find(‘.panel’);

if (menuItem.hasClass(“expanded”)) {   
menuItem.removeClass(‘expanded’).addClass(‘collapsed’);   
panel.slideUp();   
}   
else if (menuItem.hasClass(“collapsed”)) {   
menuItem.removeClass(‘collapsed’).addClass(‘expanded’);   
panel.slideDown();   
}   
});

这是很简单的一个例子，不过你可以给一个元素或HTML片断添加额外的classes以存储所有种类的信息。   
然而，除了在简单的情况之外我们更应该使用下面这个技巧。   
**14. 更好的方法是利用jQuery内置的data()方法存储状态** 由于某些原因，这方面没有很好的文档可以参考。jQuery提供了内置的data()方法，与DOM元素不同的是，它可以用来存储key/value类型的数据。数据的存储是很容易的：  
<a>复制代码</a>代码如下:  
$(‘#myDiv’).data(‘currentState’, ‘off’);

我们修改上一个例子的代码，以便于我们可以使用相同的HTML内容(除了没有”expanded”类)并使用data()函数来进行状态的存储：   
<a>复制代码</a>代码如下:  
$(‘.button’).click(function() {

var menuItem = $(this).parent();   
var panel = menuItem.find(‘.panel’);

if (menuItem.data(‘collapsed’)) {   
menuItem.data(‘collapsed’, false);   
panel.slideDown();   
}   
else {   
menuItem.data(‘collapsed’, true);   
panel.slideUp();   
}   
});

我相信你也会赞同这种方法的使用的确更加的精巧，对于data()和removeData()的更多信息，请查看[jQuery internals](http://docs.jquery.com/Internals)  
**15. 写你自己的选择器** jQuery有许多内置的选择器用以通过ID、class、标签、属性以及其他元素进行选择操作。然而当你需要基于其它一些内容进行元素选择而jQuery却没有提供该选择器时，你能做什么呢？  
嗯，一个解决方案可能是从一开始就给元素添加上classes，从而利用这些classes进行元素的选择操作。然而这被证明很难对jQuery扩展出新的选择器。  
最好的解释方法就是使用实例：  
<a>复制代码</a>代码如下:  
$.extend($.expr\[‘:’\], {   
over100pixels: function(a) {   
return $(a).height() &gt; 100;   
}   
});

$(‘.box:over100pixels’).click(function() {   
alert(‘The element you clicked is over 100 pixels high’);   
});

代码的前一部分创建一个自定义的选择器，它可以找出所有长度超过100px的元素。接下来的代码仅仅是将click事件绑定到使用该选择器查找出来的那些元素上。   
这里我不做更具体的讲解，但是你能设想一下它有多么的强大！如果你在google上搜索”custom jquery selector”，你会看到有很多这方面的例子。

**16. 精简你的HTML并在页面加载后修改它**  
这个标题可能没有多大意思，但是这个技巧可能理顺你的代码、减小代码体积和页面的下载时间、有助优化你的搜索引擎。请看下面的例子：  
<a>复制代码</a>代码如下:  
&lt;div class=”fieldOuter”&gt;   
&lt;div class=”inner”&gt;   
&lt;div class=”field”&gt;This is field number 1&lt;/div&gt;   
&lt;/div&gt;   
&lt;div class=”errorBar”&gt;   
&lt;div class=”icon”&gt;&lt;img src=”icon.png” alt=”icon” /&gt;&lt;/div&gt;   
&lt;div class=”message”&gt;&lt;span&gt;This is an error message&lt;/span&gt;&lt;/div&gt;   
&lt;/div&gt;   
&lt;/div&gt;   
&lt;div class=”fieldOuter”&gt;   
&lt;div class=”inner”&gt;   
&lt;div class=”field”&gt;This is field number 2&lt;/div&gt;   
&lt;/div&gt;   
&lt;div class=”errorBar”&gt;   
&lt;div class=”icon”&gt;&lt;img src=”icon.png” alt=”icon” /&gt;&lt;/div&gt;   
&lt;div class=”message”&gt;&lt;span&gt;This is an error message&lt;/span&gt;&lt;/div&gt;   
&lt;/div&gt;   
&lt;/div&gt;

上面是一个HTML的具体例子，为了解释目的做了少量修改。我相信你也会认为这段代码相当的丑陋。如果类似代码很长的话，你最终会形成一个相当长且丑陋的页面。因此你可以像下面这样处理它：   
<a>复制代码</a>代码如下:  
&lt;div class=”field”&gt;This is field 1&lt;/div&gt;   
&lt;div class=”field”&gt;This is field 2&lt;/div&gt;   
&lt;div class=”field”&gt;This is field 3&lt;/div&gt;   
&lt;div class=”field”&gt;This is field 4&lt;/div&gt;   
&lt;div class=”field”&gt;This is field 5&lt;/div&gt;

所有你要做的就是在页面加载完成之后通过jQuery的操作将丑陋的HTML添加回去：   
<a>复制代码</a>代码如下:  
$(document).ready(function() {   
$(‘.field’).before(‘&lt;div class=”fieldOuter”&gt;&lt;div class=”inner”&gt;’);   
$(‘.field’).after(‘&lt;/div&gt;&lt;div class=”errorBar”&gt;&lt;div class=”icon”&gt;   
&lt;img src=”icon.png” alt=”icon” /&gt;&lt;/div&gt;&lt;div class=”message”&gt;   
&lt;span&gt;This is an error message&lt;/span&gt;&lt;/div&gt;&lt;/div&gt;&lt;/div&gt;’);   
});

这样做并不总是可取的，在页面加载后的一瞬间你将会看到页面的闪动，但是在特定情况下你有很多重复的HTML内容，这时通过这个方法你可以显著的减小页面代码体积，减少无关且重复的标记能使你的SEO从中受益。   
**17. 为了速度和SEO方面的考虑，延迟加载内容** 另外还有一个方法可以提升页面加载速度，理顺Spiders搜索的HTML内容，通过在页面加载之后使用AJAX请求晚加载其他内容，这样用户就可以马上开始浏览，让Spider看到你想要它们进行索引的内容。  
我们已经在自己的网站上使用了这个技术。本页面上部的紫色按钮会弹出三个表格，方位与Google地图，这会使我们页面大小增加两倍。因此我们仅需要把这些HTML内容放入一个静态页面中，在页面加载完成之后通过load()函数加载它：  
<a>复制代码</a>代码如下:  
$(‘#forms’).load(‘content/headerForms.html’, function() {   
// Code here runs once the content has loaded   
// Put all your event handlers etc. here.   
});

我不会在页面上随处使用这个技巧。对此，你必须权衡考虑。你需要有额外的页面请求，而且页面上的部分内容不能立即呈现给用户，但是正确的使用这个技巧对优化会很有帮助。   
**18. 使用jQuery提供的工具函数** jQuery不仅仅有闪光的效果。jQuery作者也提供了一些相当实用的方法，这填补了JacaScript的一些缺陷。  
<http://docs.jquery.com/Utilities>  
尤其，提供一些常见的数组函数的浏览器支持是一个补丁。jQuery提供了迭代、过滤、克隆、合并和从数组中去除重复项的方法。  
其他常用的函数包括得到下拉框中的选择项。用传统的JavaScript方法，你就必须使用getElementById得到&lt;select&gt;元素，然后通过遍历它的子元素找出被选中的元素。而jQuery提供了相当容易使用的方法：  
<a>复制代码</a>代码如下:$(‘#selectList’).val();  
花时间浏览官方网站上的jQuery文档与一些不常用的方法上是很值得的。   
**19. 使用noConflict重命名jQuery对象** 大多数JavaScript框架都使用$符号作为缩写，当在同一个页面使用多个JS框架时，页面很容易发生冲突。幸运的是有一个简单的方法。noConflict()函数交回$的控制权并允许你设置成自己的变量名：  
<a>复制代码</a>代码如下:  
$(‘#selectList’).val();

**20. 如何得知图片已加载完毕** 这也一个没有很好文档说明的问题（至少在我查找时没看到），但是在创建照片库、旋转灯笼效果等方面，它是相当常见的需求。而这在jQuery中很容易实现。  
所有你要做的就是在IMG上使用.load()方法，在其中添加一个回调函数。下面的例子改变了一个图片src的属性同事附加上一个简单的load函数：  
<a>复制代码</a>代码如下:  
$(‘#myImage’).attr(‘src’, ‘image.jpg’).load(function() {   
alert(‘Image Loaded’);   
});

你应该可以发现一旦图片加载完毕就会弹出一个alert。   
**21. 总是使用最新版本**jQuery仍在不断的更新，它的作者John Resig一直在寻找提高jQuery性能的方法。jQuery当前的版本是1.3.2，John已经宣称他正在写一个新的选择器引擎Sizzle，这可能会显著的提高选择器性能（在Firefox中提升了4倍），因此我们应当保持最新版本。

**22. 如何检查元素是否存在**你不必检查元素是否在页面上存在就可以使用它，因为如果没有在DOM中找到合适的元素，jQuery什么也不会做。可是当我们需要检查元素是否被选择了，或是有多少项被选择了，你可以使用length属性：  
<a>复制代码</a>代码如下:  
if ($(‘#myDiv).length) {   
// your code   
}

简单之极。   
**23. 给你的HTML属性增加JS类** 我是从Karl Swedberg那学到这个技巧，过去学习jQuery时一直在看他的书。  
他最近在我以前的文章留下了对该用法的评论，基本原则如下示之。  
首先，在jQuery加载之后你可以使用方法将”JS”类添加到HTML标签中：  
<a>复制代码</a>代码如下:$(‘HTML’).addClass(‘JS’);  
因为这仅仅发生在javascript有效的时候，如果用户打开JavaScript开关，那么你可以使用它给元素添加上CSS风格：   
<a>复制代码</a>代码如下:.JS #myDiv{display:none;}

因此，这意味着在JavaScript打开时我们可以隐藏内容，然后在需要时使用jQuery显示这些内容（比如在用户点击时收缩或展开内容），同时在关闭JavaScript（以及搜索Spiders）时会看到所有内容。我将在晚些时候使用这个技巧。  
可以在这里看到[他的所有文章](http://www.learningjquery.com/2008/10/1-awesome-way-to-avoid-the-not-so-excellent-flash-of-amazing-unstyled-content)。   
**24. 返回’false’以防止默认行为** 这是很明显的，也可能不是。如果你有这样的习惯：  
<a>复制代码</a>代码如下:  
&lt;a href=”#” class=”popup”&gt;Click me!&lt;/a&gt;  
然后添加上如下的事件处理：   
<a>复制代码</a>代码如下:  
$(‘popup’).click(function(){   
// Launch popup code   
});

你在长页面使用上述方法时，它可能可以正常工作。有些时候你会注意到在点击链接后锚点会跳转到页面上部。   
所有你要做的就是阻止它的默认行为，或者实际上你可以把”return false;”添加到任何事件的默认行为上。像这样：   
<a>复制代码</a>代码如下:  
$(‘popup’).click(function(){   
// Launch popup code   
return false;   
});

**25. ready事件的简写**一个小技巧但是通过使用$(document).ready()的简写，你可以少输入几个字符。  
取代：  
$(document).ready(function (){   
// your code   
});  
你可以简写成：   
<a>复制代码</a>代码如下:  
$(function (){   
// your code   
});