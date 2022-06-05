---
id: 545
title: CSS3中的变量var了解
date: '2019-08-18T00:10:05+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/544-revision-v1/'
permalink: '/?p=545'
---

## 使用语法

首先我们先来看一个例子：  
html代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div class="element">这是一段文字</div>
```

css代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.element {
  width:200px;
  height:200px;
  --main-bg-color: #000;
  color:#fff;
  background-color: var(--main-bg-color);
}
```

<figure class="wp-block-image">![图片描述](https://segmentfault.com/img/bVXfrR?w=221&h=218)</figure>实现效果：

结果是该DOM元素背景变成了黑色。

CSS中原生的变量定义语法是：`--*`，变量使用语法是：`var(--*)`，其中`*`表示我们的变量名称。关于命名这个东西，各种语言都有些显示，例如CSS选择器不能是数字开头，JS中的变量是不能直接数值的，但是，在CSS变量中，这些限制通通没有，例如：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">:root{
    --main-bg-color: #000;
}
.element {
    background-color: var(--main-bg-color);
}
```

> 注意：变量名称不能包含`$，[，^，(，%`等字符，普通字符局限在只要是“`数字[0-9]`”“`字母[a-zA-Z]`”“`下划线_`”和“`短横线-`”这些组合，但是可以是中文，日文或者韩文，例如：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.element {
  width:200px;
  height:200px;
  --黑色: #000;
  color:#fff;
  background-color: var(--黑色);
}
```

**css变量完整语法：**  
CSS变量使用的完整语法为：`var( [, ]? )`，用中文表示就是：`var( <自定义属性名> [, <默认值 ]? )`，也即是如果我们没有定义变量名称，那么就会使用后面的值作为其默认属性值。  
如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.element {
    background-color: var(--new-bg-color,#EE0000);
}
```

得到的结果当然是后面颜色的值的背景。

我们来看一下如果变量名称不合法会出现什么结果，看下面例子：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">body {
  --color: 20px;
  background-color: #369;
  background-color: var(--color, #cd0000);
}
```

请问，此时&lt;body&gt;的背景色是？

- A. transparent
- B. 20px
- C. #369
- D. #cd0000

答案是：`A. transparent`  
CSS变量中，果发现变量值是不合法的，例如上面背景色显然不能是20px，则使用背景色的缺省值，也就是默认值代替，于是，上面CSS等同于：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">body {
    --color: 20px;
    background-color: #369;
    background-color: transparent;
}
```

## css变量在js中的应用

看如下例子，html代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div id="jsDom">这是一段文字</div>
```

css代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">#jsDom {
    --my-varwidth: 200px;
    background-color: #000;
    color:#fff;
    width:var(--my-varwidth);
    height:200px;
}
```

js代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var element = document.getElementById('jsDom');
var curWidth = getComputedStyle(element).getPropertyValue("--my-varwidth");
console.log(curWidth); //200px

//设置过后该DOM元素的宽度变为了300px
element.style.setProperty("--my-varwidth", '300px');
```

如果样式是写到行间呢？那么进行如下操作：  
html代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div id="jsDom" style="--my-varwidth:400px;width:var(--my-varwidth);">这是一段文字</div>
```

js代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var element = document.getElementById('jsDom');
var curWidth = element.style.getPropertyValue("--my-varwidth");
console.log(curWidth); //400px
```

## 浏览器兼容

<figure class="wp-block-image">![图片描述](https://segmentfault.com/img/bVXfF0?w=622&h=321)</figure>浏览器的兼容如图所示：

到目前位置IE11也不支持该css变量。

说到这儿感觉这个css变量也是很强大的，那么它跟预处理器比较，你觉得哪个更好？下面讲一下预处理器的劣势。

## 预处理器劣势

### 预处理器变量不是实时的

也许令新手惊讶的是，预处理器局限性最常见的情况是Sass无法在媒体查询中定义变量或使用@extend。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">$gutter: 1em;
@media (min-width: 30em) {
     $gutter: 2em; 
} 
 .Container { 
     padding: $gutter; 
 }
```

上面代码将编译为：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.Container { 
     padding: 1em;
 }
```

上面结果可以看出来，媒体查询块被丢弃，变量赋值被忽略。

由于无法在匹配@media规则的基础上改变变量，所以唯一的选择是为每个媒体查询分配一个唯一的变量，并单独编写每个变体。

### 预处理器变量不能级联

每当使用变量，作用域的问题就不可避免的出现。这个变量应该设置为全局变量吗？是否应该限定其范围为文件或模块？是否应该限制在块中？

由于CSS最终目的是为HTML添加样式，事实证明还有另一种有效的方法给变量限定作用域：DOM元素。但由于预处理器不在浏览器中运行并且无法看到标记，它们不能这样做。

假设有一个网站，面对偏好较大文字的用户，就向`<html>`元素添加类`user-setting-large-text`。当设置了这个类时，应当应用较大的$font-size变量赋值：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">$font-size: 1em;
.user-setting-large-text {
    $font-size: 1.5em;
} 
body { 
    font-size: $font-size; 
}
```

但同样，就像上面的媒体块示例，Sass完全忽略了该变量的赋值，这意味着这是不可能发生的。编译后的代码如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">body { 
    font-size: 1em;
}
```

### 预处理器变量不继承

虽然继承严格说来是级联的一部分，之所以把它单独分出来讲，是因为多次想调用这个特性却不得。

假设一种情况，要在DOM元素上基于其父元素应用的颜色而设置样式：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.alert {
    background-color: lightyellow;
}
.alert.info {
    background-color: lightblue;
}
.alert.error {
    background-color: orangered;
}

.alert button {
    border-color: darken(background-color, 25%);
}
```

上面的代码并不是有效的Sass（或CSS），但你应该明白它想达到什么目的。

最后一句声明试图在&lt;button&gt;元素从父元素`.alert`元素继承的`background-color`属性使用`Sass`的`darken`函数。如果类`info` 或`error`已经加在了`.alert`上（或如果`background-color`已通过`JavaScript`或用户样式设置），`button`元素能据此作出相应的响应。

显然这在`Sass中行不通`，因为`预处理器不知道DOM结构`，但希望你清楚的认识到为什么这类东西是有用的。

调用一个特定的用例：出于可访问性的原因，在继承了DOM属性上运行颜色函数是极其方便的。例如，确保文本始终可读，并充分与背景颜色形成鲜明对比。 有了自定义属性和新的CSS颜色函数，很快这将成为可能。

### 预处理器变量不可互操作

这是预处理器相对明显的一个缺点，提到它是因为我觉得它重要。如果你正使用`PostCSS`来构建网站，想使用只能通过Sass实现主题化的第三方组件，那你真是不走运了。

跨不同的工具集或CDN上托管的第三方样式表共享预处理器变量是不可能（或至少不容易）的。

原生的CSS自定义属性可以与任何CSS预处理器或纯CSS文件一起使用。反之则不然。

下面给一个css变量在媒体查询中的使用：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">:root {
    --gutter: 1.5em;
}

@media (min-width: 30em) {
    :root {
        --gutter: 2em;
    }
}
@media (min-width: 48em) {
    :root {
        --gutter: 3em;
    }
}
```

如果是预处理器这样写就无效了。