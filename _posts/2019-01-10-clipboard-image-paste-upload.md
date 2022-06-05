---
id: 648
title: 剪切板图片粘贴上传
date: '2019-01-10T23:00:36+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=648'
permalink: /2019/01/clipboard-image-paste-upload
categories:
    - Javascript
---

## 一、背景

在H5技术发展如火如荼的今天，前端，再也不是页面的代名词，而今的前端早已不再是写写页面写写简单的交互了，现在的前端相比十年前的前端已经发生了翻天覆地的变化，像近几年出现的angularjs，vuejs，react等前端框架已被广泛应用于各类在线协作平台，webapp中，谁又还能说前端很简单呢？

在以前，对于上传的实现，最原始的做法就是通过在form表单中放置type为file的input标签，然后用户选择后提交上传，但是页面在提交的时候会刷新，这种用户体验非常不友好，后来出现了xmlRequest，借助xhr我们可以在不刷新页面的情况下直接上传图片，用户体验有了很大的提升，但是我们还想再进一步，接着出现了dataTransfer和formData，我们发现可以通过借助dataTransfer和formData实现从电脑的资源管理器直接拖拽图片到网页上传，具体过程是从电脑拖拽图片到网页，js在drop的事件中取到当前事件对象的dataTransfer进而得到文件对象，然后实例化formData对象，借助xhr异步上传图片，这无疑是一个锦上添花的功能，至此，对于上传的用户体验已经达到了一定的告诉，那么我们是不是就不能再进一步，玩点更高逼格的东西，让用户体验再上一个台阶了呢？这就是我今天要说的，剪切板图片粘贴上传，这个没什么太多可讲的，就是一些API的应用，所以直接看实现了。

## 二、代码实现

1、首先我们看一下怎么获取剪切版中的图片

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">document.addEventListener('paste', function(event) {
    var items = (event.clipboardData && event.clipboardData.items) || [];
    var file = null;
 
    if (items && items.length) {
        for (var i = 0; i < items.length; i++) {
            if (items[i].type.indexOf('image') !== -1) {
                file = items[i].getAsFile();
                break;
            }
        }
    }
});
```

注意：这上面有一个坑，记住一定不要用数组的find方法去查找图片item，因为items是一个伪数组，并没有find方法

以上代码执行过程，全局监听paste事件，当粘贴事件触发时遍历剪切版对象（clipboardData）中的所有items，找到类型为图片的item并调用getAsFile方法得到文件对象，拿到file 对象后我们有两种选择：

（1）通过fileReader得到文件对象的base64字符串，实现代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var reader = new FileReader();
reader.onload = function(e){
  // 通过e.target.result取到base64然后上传
  // 作为src设到image标签上预览
}
reader.readAsDataURL(file); //此处的file为上面得到的文件对象
```

在得到了base64字符串后我们可以传递给后端，后端接受base64并存储，如果我们需要在上传前或者上传过程中预览图片，可以直接将上面得到的base64数据作为src传递给image标签然后预览。

（2）通过formData酱文件对象转换为二进制数据

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var formData = new FormData();
formData.append('file', file);
```

2、上传，不管我们选择使用base64还是formData，在得到了数据后接下来要做的就是上传，对于base64的上传这里就不做演示了，这里仅演示一下formData的上传，其实都一样，实现代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var xhr = new XMLHttpRequest();
xhr.onload = function () {
  try {
    // 取得响应消息
    var result = JSON.parse(this.responseText);
  } catch(err) {
    console.log(err)
  }
};
xhr.open('POST', './upload_file.php', true);
xhr.send(formData);
```

当然上面的上传只是上传了图片，如果除了图片还需要上传其他内容比如文件名，时间之类的，只需要在初始化formData的时候再添加字段就行了。

后端保存就不说了，不同人有不同的选择，node，.net, php，java只要用的顺手，都行。

## 三、局限性

对于qq，微信等的截图或者按print screen得到的截图，还有任意网页的右击复制图片都能完美支持，但是，对于电脑本地图片文件的复制没办法从剪切版获取到，如果有哪位朋友发现了可以实现的方法，请一点要告诉我一下，谢谢！

## 四、兼容性

对于chrome和safari经过测试，完美支持，对于safari使用方式有些差别，而且只能在设置了contenteditable属性额元素身上才能触发，要是遇到了IE就直接放弃吧，三个字不支持，多的就不说了，说多了都是泪。