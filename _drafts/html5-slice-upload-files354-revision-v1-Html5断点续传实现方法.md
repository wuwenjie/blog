---
id: 355
title: Html5断点续传实现方法
date: '2019-08-17T00:11:39+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/354-revision-v1/'
permalink: '/?p=355'
---

**大文件分块**

一般常用的web服务器都有对向服务器端提交数据有大小限制。超过一定大小文件服务器端将返回拒绝信息。当然，web服务器都提供了配置文件可能修改限制的大小。针对iis实现大文件的上传网上也有一些通过修改web服务器限制文件大小来实现。不过这样对web服务器的安全带了问题。攻击者很容易发一个大数据包，将你的web服务器直接给拖死。   
现在针对大文件上传主流的实现方式，通过将大文件分块。比如针对一个100M文件，按2M拆分为50块。然后再将每块文件依次上传到服务器上，上传完成后再在服务器上合并文件。   
在web实现大文件上传，核心主要实现文件的分块。在Html5 File API 出现以前，要想在web上实现文件分块传输。只有通过flash或Activex实现文件的分块。

在Html5 下，我们可以直接通过file的slice 方法来实现文件的分块。如：

XML/HTML Code复制内容到剪贴板

1. file.slice(0,1000);
2. file.slice(1000,2000);
3. file.slice(2000,3000);

然后再通过XMLHttpRequest异步上传到服务器。

**Html5 上传文件类库**

如果你有兴趣及时间 ，当然可以自己用html5的File API来实现。本人在网上查找到了以下两个支持html5类库。   
resumable.js 附git上的地址：https://github.com/23/resumable.js   
Pludload http://plupload.com/

resumable是一个纯html5上传类库。   
而Pludload是一个支持html5,flash,silverlight,html4,它会自动判断浏览是否支持html5不支持将用其它的上传方式。   
我测试下来，resumable和Pludload都支持html5分块上传文件。用下来觉得resumable比较适合，以下就选resumable来进行介绍。

resumable.js断点上传使用介绍

主要配置介绍：   
JavaScript Code复制内容到剪贴板

1. **var** r = **new** Resumable({
2. target:’/test/upload’,
3. chunkSize:1\*1024\*1024,
4. simultaneousUploads:4,
5. testChunks: **true**,
6. throttleProgressCallbacks:1,
7. method: “octet”
8. });

chunkSize 分块文件大小，以字节为单位   
simultaneousUploads 同时上传文件块的进程数，可以同时允许多个文件块上传。   
testChunks 上前文件块是否先通过get方式发送文件信息检测文件是否已经上传。

resumable断点上传是通过testChunks配置节点来实现，当设置为true时。resumable会先发送一个get请求，如果http状态返回 200。则认为当前块已经上传完成，然后进行下一块的get请求。如果http状态返回的不是200，则将通过post方式发送当前块数据包进行文件块上传。

设置testChunks为true每次上传都会增加一个get请求，如果我们已经知道上次中断上传前文件的块数。下次直接从中断的块数上传就行了。这样可以为每个块减少一次http的get请求。   
针对这个需求，我修改了resumable的源码，为resumable里的file对象增加了一个startchunkindex的属性，默认为0。用于设置当前文件从哪一个块开始上传。这样，我们只需要在在文件上传前从服务器上进行一次查询(查询当前文件上传到哪一块)，返回上次上传的文件块索引。再将索引值设置到file的startchunkindex属性就可以实现从上次断开的文件块开始上传。   
调用方式：

JavaScript Code复制内容到剪贴板

1. // Handle file add event
2. r.on(‘fileAdded’, **function** (file) {
3. file.startchunkindex = 0; // 设置当前文件开始上传的块数

具体可以查看附件里的demo。

**收尾工作**

所有文件块上传完，最后工作就是合并保存文件。附件为resumable断上传.net实现的服务端例子，包括简单的文件合并功能。其它语言的demo大家也可以从resumable的git上下载。   
demo例子为了简单，只是把文件存放在本机。在真实的生产环境中。一般应该放在单独的文件服务器上（前台web通过ftp或文件夹共享方式上传到文件服务器），然后对上传好的文件进行分发镜像或处理（比如视频压缩）。当然最好是存在分布式文件系统中，目前看下来放到Hadoop分布式文件系统(HDFS)是一个不错的好方案。

demo

<figure class="wp-block-image">[![](https://files.jb51.net/file_images/article/201512/20151205140901.png)](https://files.jb51.net/file_images/article/201512/20151205140901.png)</figure>[Vs2012 Html5 Upload demo下载](http://xiazai.jb51.net/201512/yuanma/Html5Upload_net(jb51.net).rar)