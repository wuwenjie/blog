---
id: 636
title: 'HTML5 video视频倍速播放playbackRate'
date: '2019-08-18T19:43:58+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/633-revision-v1/'
permalink: '/?p=636'
---

```
<pre class="wp-block-preformatted">// 视频获取速率
var videoSpeed = video.playbackRate;
// 视频设置播放速率，如2倍速播放
video.playbackRate = 2;
// 获取音频播放速率
var audioSpeed = audio.playbackRate;
// 音频设置播放速率，如2倍速播放
audio.playbackRate = 2;

现在看见没有倍速播放功能的网站，你可以F12打开在控制台更改成自己想要的倍速进行观看了。。。 document.getElementById('#video').playbackRate = 2;
```