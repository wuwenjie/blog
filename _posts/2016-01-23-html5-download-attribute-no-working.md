---
id: 499
title: 'HTML5 download属性无效的问题'
date: '2016-01-23T22:08:24+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=499'
permalink: /2016/01/html5-download-attribute-no-working
categories:
    - 'HTML&amp;CSS'
---

用户点击下载多媒体文件(图片/视频等)，最简单的方式：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><a href='url' download="filename.ext">下载</a>
```

如果url指向第三方资源，download会失效，表现和不使用download时一致——浏览器能打开的文件，浏览器会直接打开，不能打开的文件，会直接下载。浏览器打开的文件，可以手动下载。

解决方案一：将文件打包为.zip/.rar等浏览器不能打开的文件下载。

解决方案二：通过后端转发，后端请求第三方资源，返回给前端，前端使用file-saver等工具保存文件。

如果url指向的第三方资源配置了CORS，download依然无效，但可以通过xhr请求获取文件，然后下载到本地。

/\*\*

- 用FileSave保存文件
- @param url  
    \*/  
    export function downloadUrlFile(url) {  
    const xhr = new XMLHttpRequest();  
    xhr.open(‘GET’, url, true);  
    xhr.responseType = ‘blob’;  
    xhr.setRequestHeader(‘Authorization’, ‘Basic a2VybWl0Omtlcm1pdA==’);  
    xhr.onload = () =&gt; {  
    if (xhr.status === 200) {  
     // 获取图片blob数据并保存  
     saveAs(xhr.response, ‘abc.jpg’);  
    }  
    }; xhr.send();  
    }

/\*\*

- URL方式保存文件到本地
- @param name 文件名
- @param data 文件的数据  
    \*/  
    function save(name, data) {  
    var urlObject = window.URL || window.webkitURL || window;  
    var export\_blob = new Blob(\[data\]);  
    var save\_link = document.createElementNS(‘http://www.w3.org/1999/xhtml’, ‘a’)  
    save\_link.href = urlObject.createObjectURL(export\_blob);  
    save\_link.download = name;  
    \_fakeClick(save\_link);  
    }

第三方跨域多媒体资源无法直接下载。很奇怪，浏览器不能打开的文件可以下载，浏览器能打开的文件不能下载，这个限制似乎没有多大意义。

不依靠后端，有两个可能破解这个限制的思路。

1、window.open(url)，再向新窗口写入一个，触发点击。

验证结果：这种向别人的网页中嵌入自己内容的方式，极大影响浏览器的安全，无法实现。

<figure class="wp-block-image">![](url)</figure>2、, onload的回调中，将img 绘入 canvas，canvas.toDataUrl()，然后保存。

MDN给出的例子 使用download保存画布为png

验证结果：canvas.drawImage(img,0,0)后，canvas被跨域资源污染，canvas.toDataUrl()调用报错。

/\*\*

- 下载url图片
- @param imageUrl  
    \*/  
    const downloadUrl = (imageUrl) =&gt; {  
    const downloadCanvas = $(‘#download-canvas’)\[0\];  
    const img = new Image();  
    img.onload = () =&gt; {  
     const ctx = downloadCanvas.getContext(‘2d’);  
     ctx.drawImage(img, 0, 0);  
     const imageDataUrl = downloadCanvas.toDataURL(‘image/jpeg’);   
     // Uncaught DOMException: Failed to execute ‘toDataURL’ on ‘HTMLCanvasElement’: Tainted canvases may not be exported.  
     saveAs(imageDataUrl, ‘附件’);  
    };  
    img.src = imageUrl;  
    };  
    结论：

浏览器已经限制死跨域下载多媒体文件的各种方式。

最正规的办法还是让后端做一次转发。请求后端，后端向第三方请求文件，返回给前端，前端保存文件。