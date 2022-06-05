---
id: 842
title: vs2019在线下载过慢解决
date: '2019-10-19T14:20:18+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=842'
permalink: /2019/10/solution-for-vs2019-download-slowly-in-china
categories:
    - 'net&amp;c#'
---

 最近想安装最新版的visual studio 2019，因为微软是在线下载并安装的，因为大家都懂的某些原因，导致网络非常的慢，只有10KB的速度，后来想去下载离线包，可是一看离线包有20多个G，也放弃了，因为我并不需要那么完整的包。所以还是去解决在线安装过慢问题，这个问题只要修改host即可了，解决方法如下：

 C:\\Windows\\System32\\drivers\\etc\\host 修改host，在host最后一行加上:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""> 110.53.72.104 download.visualstudio.microsoft.com
```

如果你刚才启动了在线安装就立即关闭，重新打开在线安装，就会发现速度有很多大的提升了，我从10KB，提升到了3M以上

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2019/10/QQ图片20191019141619.png)</figure>Visual Studio 2019 Enterprise 企业版：  
BF8Y8-GN2QH-T84XB-QVY3B-RC4DF  
  
Visual Studio 2019 Professional 专业版：  
NYWVH-HT4XC-R2WYW-9Y3CM-X4V3Y