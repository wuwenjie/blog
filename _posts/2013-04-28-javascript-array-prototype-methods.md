---
id: 319
title: 'Javascript Array常见方法说明'
date: '2013-04-28T22:30:36+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=319'
permalink: /2013/04/javascript-array-prototype-methods
categories:
    - Javascript
---

**不嫌弃包的大小的话，可以使用underscorejs 或者 lodash 更加好用。。。。**

### 一、前言-索引

ES5中新增的不少东西，了解之对我们写JavaScript会有不少帮助，比如数组这块，我们可能就不需要去有板有眼地`for`循环了。

ES5中新增了写数组方法，如下：

1. [forEach](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#foreach) (js v1.6)
2. [map](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#map) (js v1.6)
3. [filter](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#filter) (js v1.6)
4. [some](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#some) (js v1.6)
5. [every](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#every) (js v1.6)
6. [indexOf](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#indexof) (js v1.6)
7. [lastIndexOf](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#lastindexof) (js v1.6)
8. [reduce](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#reduce) (js v1.8)
9. [reduceRight](https://www.zhangxinxu.com/wordpress/2013/04/es5%e6%96%b0%e5%a2%9e%e6%95%b0%e7%bb%84%e6%96%b9%e6%b3%95/#reduceright) (js v1.8)

**浏览器支持**

- Opera 11+
- Firefox 3.6+
- Safari 5+
- Chrome 8+
- Internet Explorer 9+

对于让人失望很多次的IE6-IE8浏览器，Array原型扩展可以实现以上全部功能，例如`forEach`方法:

```
<pre class="wp-block-preformatted">// 对于古董浏览器，如IE6-IE8

if (typeof Array.prototype.forEach != "function") {
  Array.prototype.forEach = function () {
    /* 实现 */
  };
}
```

### 二、一个一个来

1. **forEach**  
    `forEach`是Array新方法中最基本的一个，就是遍历，循环。例如下面这个例子：\[1, 2 ,3, 4\].forEach(alert);等同于下面这个传统的`for`循环：var array = \[1, 2, 3, 4\]; for (var k = 0, length = array.length; k &lt; length; k++) { alert(array\[k\]); }Array在ES5新增的方法中，参数都是`function`类型，默认有传参，这些参数分别是？见下面：\[1, 2 ,3, 4\].forEach(console.log); // 结果： // 1, 0, \[1, 2, 3, 4\] // 2, 1, \[1, 2, 3, 4\] // 3, 2, \[1, 2, 3, 4\] // 4, 3, \[1, 2, 3, 4\]显而易见，`forEach`方法中的`function`回调支持3个参数，第1个是遍历的数组内容；第2个是对应的数组索引，第3个是数组本身。因此，我们有：\[\].forEach(function(value, index, array) { // … });对比jQuery中的`$.each`方法：$.each(\[\], function(index, value, array) { // … });会发现，第1个和第2个参数正好是相反的，大家要注意了，不要记错了。后面类似的方法，例如`$.map`也是如此。现在，我们就可以使用`forEach`卖弄一个稍显完整的例子了，数组求和：var sum = 0; \[1, 2, 3, 4\].forEach(function (item, index, array) { console.log(array\[index\] == item); // true sum += item; }); alert(sum); // 10再下面，更进一步，`forEach`除了接受一个必须的回调函数参数，还可以接受一个可选的上下文参数（改变回调函数里面的`this`指向）（第2个参数）。array.forEach(callback,\[ thisObject\])例子更能说明一切：var database = { users: \[“张含韵”, “江一燕”, “李小璐”\], sendEmail: function (user) { if (this.isValidUser(user)) { console.log(“你好，” + user); } else { console.log(“抱歉，”+ user +”，你不是本家人”); } }, isValidUser: function (user) { return /^张/.test(user); } }; // 给每个人法邮件 database.users.forEach( // database.users中人遍历 database.sendEmail, // 发送邮件 database // 使用database代替上面标红的this ); // 结果： // 你好，张含韵 // 抱歉，江一燕，你不是本家人 // 抱歉，李小璐，你不是本家如果这第2个可选参数不指定，则使用全局对象代替（在浏览器是为`window`），[严格模式](http://dmitrysoshnikov.com/ecmascript/es5-chapter-2-strict-mode/)下甚至是`undefined`.另外，forEach不会遍历纯粹“占着官位吃空饷”的元素的，例如下面这个例子：var array = \[1, 2, 3\]; delete array\[1\]; // 移除 2 alert(array); // “1,,3” alert(array.length); // but the length is still 3 array.forEach(alert); // 弹出的仅仅是1和3综上全部规则，我们就可以对IE6-IE8进行仿真扩展了，如下代码：// 对于古董浏览器，如IE6-IE8 if (typeof Array.prototype.forEach != “function”) { Array.prototype.forEach = function (fn, context) { for (var k = 0, length = this.length; k &lt; length; k++) { if (typeof fn === “function” &amp;&amp; Object.prototype.hasOwnProperty.call(this, k)) { fn.call(context, this\[k\], k, this); } } }; } 现在拿上面“张含韵”的例子测下我们扩展的`forEach`方法，您可能狠狠地点击这里：[兼容处理的forEach方法demo](http://www.zhangxinxu.com/study/201304/array-foreach.html)例如IE7浏览器下：
2. **map**  
    这里的`map`不是“地图”的意思，而是指“映射”。`[].map();` 基本用法跟`forEach`方法类似：array.map(callback,\[ thisObject\]);`callback`的参数也类似：\[\].map(function(value, index, array) { // … });`map`方法的作用不难理解，“映射”嘛，也就是原数组被“映射”成对应新数组。下面这个例子是数值项求平方：var data = \[1, 2, 3, 4\]; var arrayOfSquares = data.map(function (item) { return item \* item; }); alert(arrayOfSquares); // 1, 4, 9, 16`callback`需要有`return`值，如果没有，就像下面这样：var data = \[1, 2, 3, 4\]; var arrayOfSquares = data.map(function() {}); arrayOfSquares.forEach(console.log);结果如下图，可以看到，数组所有项都被映射成了`undefined`：  
    在实际使用的时候，我们可以利用`map`方法方便获得对象数组中的特定属性值们。例如下面这个例子（之后的兼容demo也是该例子）：var users = \[ {name: “张含韵”, “email”: “zhang@email.com”}, {name: “江一燕”, “email”: “jiang@email.com”}, {name: “李小璐”, “email”: “li@email.com”} \]; var emails = users.map(function (user) { return user.email; }); console.log(emails.join(“, “)); // zhang@email.com, jiang@email.com, li@email.com`Array.prototype`扩展可以让IE6-IE8浏览器也支持`map`方法：if (typeof Array.prototype.map != “function”) { Array.prototype.map = function (fn, context) { var arr = \[\]; if (typeof fn === “function”) { for (var k = 0, length = this.length; k &lt; length; k++) { arr.push(fn.call(context, this\[k\], k, this)); } } return arr; }; }您可以狠狠地点击这里：[兼容map方法测试demo](http://www.zhangxinxu.com/study/201304/array-map.html)结果显示如下图，IE6浏览器：
3. **filter**  
    `filter`为“过滤”、“筛选”之意。指数组`filter`后，返回过滤后的新数组。用法跟`map`极为相似：array.filter(callback,\[ thisObject\]);`filter`的`callback`函数需要返回布尔值`true`或`false`. 如果为`true`则表示，恭喜你，通过啦！如果为`false`, 只能高歌“我只能无情地将你抛弃……”。可能会疑问，一定要是`Boolean`值吗？我们可以简单测试下嘛，如下：var data = \[0, 1, 2, 3\]; var arrayFilter = data.filter(function(item) { return item; }); console.log(arrayFilter); // \[1, 2, 3\]有此可见，返回值只要是弱等于`== true/false`就可以了，而非非得返回 `=== true/false`.因此，我们在为低版本浏览器扩展时候，无需关心是否返回值是否是纯粹布尔值（见下黑色代码部分）：if (typeof Array.prototype.filter != “function”) { Array.prototype.filter = function (fn, context) { var arr = \[\]; if (typeof fn === “function”) { for (var k = 0, length = this.length; k &lt; length; k++) { fn.call(context, this\[k\], k, this) &amp;&amp; arr.push(this\[k\]); } } return arr; }; }接着上面`map`筛选邮件的例子，您可以狠狠地点击这里：[兼容处理后filter方法测试demo](http://www.zhangxinxu.com/study/201304/array-filter.html)主要测试代码为：var emailsZhang = users // 获得邮件 .map(function (user) { return user.email; }) // 筛选出zhang开头的邮件 .filter(function(email) { return /^zhang/.test(email); }); console.log(emailsZhang.join(“, “)); // zhang@email.com实际上，存在一些语法糖可以实现`map+filter`的效果，被称之为“数组简约式(Array comprehensions)”。目前，仅FireFox浏览器可以实现，展示下又不会怀孕：var zhangEmails = \[user.email for each (user in users) if (/^zhang/.test(user.email)) \]; console.log(zhangEmails); // \[zhang@email.com\]
4. **some**  
    `some`意指“某些”，指是否“某些项”合乎条件。与下面的`every`算是好基友，`every`表示是否“每一项”都要靠谱。用法如下：array.some(callback,\[ thisObject\]);例如下面的简单使用：var scores = \[5, 8, 3, 10\]; var current = 7; function higherThanCurrent(score) { return score &gt; current; } if (scores.some(higherThanCurrent)) { alert(“朕准了！”); }结果弹出了“朕准了”文字。 `some`要求至少有1个值让`callback`返回`true`就可以了。显然，`8 > 7`，因此`scores.some(higherThanCurrent)`值为`true`.我们自然可以使用`forEach`进行判断，不过，相比`some`, 不足在于，`some`只有有`true`即返回不再执行了。IE6-IE8扩展如下：if (typeof Array.prototype.some != “function”) { Array.prototype.some = function (fn, context) { var passed = false; if (typeof fn === “function”) { for (var k = 0, length = this.length; k &lt; length; k++) { if (passed === true) break; passed = !!fn.call(context, this\[k\], k, this); } } return passed; }; }于是，我们就有了“朕准了”的demo，您可以狠狠地点击这里：[兼容处理后的some方法demo](http://www.zhangxinxu.com/study/201304/array-some.html)
5. **every**  
    跟`some`的基友关系已经是公开的秘密了，同样是返回Boolean值，不过，`every`需要每一个妃子都要让朕满意，否则——“来人，给我拖出去砍了！”IE6-IE8扩展（与`some`相比就是`true`和`false`调换一下）：if (typeof Array.prototype.every != “function”) { Array.prototype.every = function (fn, context) { var passed = true; if (typeof fn === “function”) { for (var k = 0, length = this.length; k &lt; length; k++) { if (passed === false) break; passed = !!fn.call(context, this\[k\], k, this); } } return passed; }; }还是那个朕的例子，您可以狠狠地点击这里：[是否every妃子让朕满意demo](http://www.zhangxinxu.com/study/201304/array-every.html)if (scores.every(higherThanCurrent)) { console.log(“朕准了！”); } else { console.log(“来人，拖出去斩了！”); }结果是：
6. **indexOf**  
    `indexOf`方法在字符串中自古就有，`string.indexOf(searchString, position)`。数组这里的`indexOf`方法与之类似。array.indexOf(searchElement\[, fromIndex\])返回整数索引值，如果没有匹配（严格匹配），返回`-1`. `fromIndex`可选，表示从这个位置开始搜索，若缺省或格式不合要求，使用默认值`0`，我在FireFox下测试，发现使用字符串数值也是可以的，例如`"3"`和`3`都可以。var data = \[2, 5, 7, 3, 5\]; console.log(data.indexOf(5, “x”)); // 1 (“x”被忽略) console.log(data.indexOf(5, “3”)); // 4 (从3号位开始搜索) console.log(data.indexOf(4)); // -1 (未找到) console.log(data.indexOf(“5”)); // -1 (未找到，因为5 !== “5”)兼容处理如下：if (typeof Array.prototype.indexOf != “function”) { Array.prototype.indexOf = function (searchElement, fromIndex) { var index = -1; fromIndex = fromIndex \* 1 || 0; for (var k = 0, length = this.length; k &lt; length; k++) { if (k &gt;= fromIndex &amp;&amp; this\[k\] === searchElement) { index = k; break; } } return index; }; }一个路子下来的，显然，轮到demo了，您可以狠狠地点击这里：[兼容处理后indexOf方法测试demo](http://www.zhangxinxu.com/study/201304/array-indexof.html)下图为ietester IE6下的截图：
7. **lastIndexOf**  
    `lastIndexOf`方法与`indexOf`方法类似：array.lastIndexOf(searchElement\[, fromIndex\])只是`lastIndexOf`是从字符串的末尾开始查找，而不是从开头。还有一个不同就是`fromIndex`的默认值是`array.length - 1`而不是`0`.IE6等浏览器如下折腾：if (typeof Array.prototype.lastIndexOf != “function”) { Array.prototype.lastIndexOf = function (searchElement, fromIndex) { var index = -1, length = this.length; fromIndex = fromIndex \* 1 || length – 1; for (var k = length – 1; k &gt; -1; k-=1) { if (k &lt;= fromIndex &amp;&amp; this\[k\] === searchElement) { index = k; break; } } return index; }; }于是，则有：var data = \[2, 5, 7, 3, 5\]; console.log(data.lastIndexOf(5)); // 4 console.log(data.lastIndexOf(5, 3)); // 1 (从后往前，索引值小于3的开始搜索) console.log(data.lastIndexOf(4)); // -1 (未找到)懒得截图了，结果查看可狠狠地点击这里：[lastIndexOf测试demo](http://www.zhangxinxu.com/study/201304/array-lastindexof.html)
8. **reduce**  
    `reduce`是JavaScript 1.8中才引入的，中文意思为“减少”、“约简”。不过，从功能来看，我个人是无法与“减少”这种含义联系起来的，反而更接近于“迭代”、“递归(recursion)”，擦，因为单词这么接近，不会是ECMA-262 5th制定者笔误写错了吧~~此方法相比上面的方法都复杂，用法如下：array.reduce(callback\[, initialValue\])`callback`函数接受4个参数：之前值、当前值、索引值以及数组本身。`initialValue`参数可选，表示初始值。若指定，则当作最初使用的`previous`值；如果缺省，则使用数组的第一个元素作为`previous`初始值，同时`current`往后排一位，相比有`initialValue`值少一次迭代。var sum = \[1, 2, 3, 4\].reduce(function (previous, current, index, array) { return previous + current; }); console.log(sum); // 10说明：
    1. 因为`initialValue`不存在，因此一开始的`previous`值等于数组的第一个元素。
    2. 从而`current`值在第一次调用的时候就是`2`.
    3. 最后两个参数为索引值`index`以及数组本身`array`.
    
    以下为循环执行过程：// 初始设置 previous = initialValue = 1, current = 2 // 第一次迭代 previous = (1 + 2) = 3, current = 3 // 第二次迭代 previous = (3 + 3) = 6, current = 4 // 第三次迭代 previous = (6 + 4) = 10, current = undefined (退出)有了`reduce`，我们可以轻松实现二维数组的扁平化：var matrix = \[ \[1, 2\], \[3, 4\], \[5, 6\] \]; // 二维数组扁平化 var flatten = matrix.reduce(function (previous, current) { return previous.concat(current); }); console.log(flatten); // \[1, 2, 3, 4, 5, 6\]兼容处理IE6-IE8：if (typeof Array.prototype.reduce != “function”) { Array.prototype.reduce = function (callback, initialValue ) { var previous = initialValue, k = 0, length = this.length; if (typeof initialValue === “undefined”) { previous = this\[0\]; k = 1; } if (typeof callback === “function”) { for (k; k &lt; length; k++) { this.hasOwnProperty(k) &amp;&amp; (previous = callback(previous, this\[k\], k, this)); } } return previous; }; }然后，测试整合，demo演示，您可以狠狠地点击这里：[兼容IE6的reduce方法测试demo](http://www.zhangxinxu.com/study/201304/array-reduce.html)IE6浏览器下结果如下图：
9. **reduceRight**  
    `reduceRight`跟`reduce`相比，用法类似：array.reduceRight(callback\[, initialValue\])实现上差异在于`reduceRight`是从数组的末尾开始实现。看下面这个例子：var data = \[1, 2, 3, 4\]; var specialDiff = data.reduceRight(function (previous, current, index) { if (index == 0) { return previous + current; } return previous – current; }); console.log(specialDiff); // 0结果`0`是如何得到的呢？  
    我们一步一步查看循环执行：// 初始设置 index = 3, previous = initialValue = 4, current = 3 // 第一次迭代 index = 2, previous = (4- 3) = 1, current = 2 // 第二次迭代 index = 1, previous = (1 – 2) = -1, current = 1 // 第三次迭代 index = 0, previous = (-1 + 1) = 0, current = undefined (退出)为使低版本浏览器支持此方法，您可以添加如下代码：if (typeof Array.prototype.reduceRight != “function”) { Array.prototype.reduceRight = function (callback, initialValue ) { var length = this.length, k = length – 1, previous = initialValue; if (typeof initialValue === “undefined”) { previous = this\[length – 1\]; k–; } if (typeof callback === “function”) { for (k; k &gt; -1; k-=1) { this.hasOwnProperty(k) &amp;&amp; (previous = callback(previous, this\[k\], k, this)); } } return previous; }; }您可以狠狠地点击这里：[reduceRight简单使用demo](http://www.zhangxinxu.com/study/201304/array-reduceright.html)对比FireFox浏览器和IE7浏览器下的结果：

### 三、更进一步的应用

我们还可以将上面这些数组方法应用在其他对象上。

例如，我们使用forEach遍历DOM元素。

```
<pre class="wp-block-preformatted">var eleDivs = document.getElementsByTagName("div");
Array.prototype.forEach.call(eleDivs, function(div) {
    console.log("该div类名是：" + (div.className || "空"));
});
```

可以输出页面所有`div`的类名，您可以狠狠地点击这里：[Array新方法forEach遍历DOM demo](http://www.zhangxinxu.com/study/201304/array-foreach-dom-loop.html)

<figure class="wp-block-image">![forEach方法遍历DOM的使用演示 张鑫旭-鑫空间-鑫生活](https://image.zhangxinxu.com/image/blog/201304/2013-04-25_213419.png)</figure>结果如下，IE6下，demo结果：

等很多其他类数组应用。