---
id: 831
title: 考驾照的记录
date: '2019-09-26T14:00:35+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/09/167-revision-v1'
permalink: '/?p=831'
---

注意前三个项目打灯。每次停车都打空挡后 ，不要拉手刹！先冷静想想接下来要干嘛，是不是要打灯。  
起步灯一定要记住，在驶入侧方停车之前一定要记住！！！侧方停车：切线中标10公分左右注意打灯出来时 右车头一半时候 再右打死，主要怕的是右边轮子压线看到缝 回正

半坡：一定要身体坐正再去看定点那个位置。看到两条黄线中间线上面一点点就要踩住了。切线10公分，注意稳住离合，第一个抖动之后只要稍微**一丝丝**的抬那么一小点后 稳住两秒，弓形脚后跟顶住慢慢抬刹车。**如果听到很重的闷闷声，左脚千万要稳住**，即使很慢也没事脚后跟顶着稳住车子太快就早点定点看到黄线中间就踩住刹车，车子较慢就看到两条黄线完毕就踩住。 坐正了看。车轮还没上坡 要切20公分，回正之后，打多少回多少，对到10公分，就要回多少。然后再去对10公分微调的打多少回多少。快，顿，丝踩丝抬！如果觉得动力不足，也不要快快的抬起来，要丝抬啊，不然熄火  
直角：车头没过线之后，一小会儿就要左打死、很慢的话**提前多一丁点就够了,打死之后 注意丝抬一点点离合器**  
曲线：注意半圈，车标两点 凹凸点打完半圈。注意左边看是两条黄线，**右边看是一条黄线15公分**。打方向盘只能在两点四点之间，如果直走没事的话就不要乱推了。  
刚进入的时候 一般是回两点靠左的，在弯道接连处必须回平顺过去。 右边的弯道一般是要往四点打，越来越窄的时候就要回平，快出弯道的时候就要回平。第一个弯道 车头3分之一的时候（凹凸点提前一点点） 左打完一圈。 第二个弯道 喷水点等距的时候（也是提前一点点） 右打一圈。 当要出弯道的时候， 中标对到 正面的时候 要立马回正方向  
倒库：三点一线，在左后视镜最右下角看的话快到两条黄线宽的时候 很重要 右打死 因为我打方向慢由原来的一条黄线提早了。三点一线对正之后，如果车身有点歪，只能方向盘回打一点点，走一小会儿，然后再微微调整回正。三把轮的第二把回轮是很微小的。 右边库进来时候，如果库门角离车身超过了15公分，就不要回正了，走完门把手就再打死。如果是顶着门把手就要回正走。左边库进来时候，如果库门角离车身超过了20公分，就不要回正了。右边库进来，如果左边越窄，对角的回的秒数就越少 就可以一把进。左边很宽就必须 回的秒数多一些！门把手快要走完时候 就要准备再次右打死了。进库后，要往窗边看 注意车轮与地面的接触点，定点准确踩住。稍微往后一丁点

踩离合：脚尖踩到底，然后慢慢抬到车子即将要动了，然后脚跟着地，稍微压一下，踝关节抬一下 稳住。接下来只能用踝关节进行离合控制，除非要踩到底时候。

考场视频：[http://v.youku.com/v\_show/id\_XNzY1MjMyOTk2.html](http://v.youku.com/v_show/id_XNzY1MjMyOTk2.html)模拟：<http://dri8.com/index.htm><http://tieba.baidu.com/p/2114744624>[http://v.pps.tv/play\_3HWVH3.html](http://v.pps.tv/play_3HWVH3.html)[http://news.jsyks.com/n\_19074a](http://news.jsyks.com/n_19074a)<http://www.91town.com/thread-1245201497-1-1.html>[http://mp.weixin.qq.com/s?\_\_biz=MzA4MzIyOTYxOA==&amp;mid=200149161&amp;idx=2&amp;sn=01ede4b30bef813627d0d454deb9ead2&amp;;3rd=MzA3MDU4NTYzMw==&amp;scene=6#rd](http://mp.weixin.qq.com/s?__biz=MzA4MzIyOTYxOA==&mid=200149161&idx=2&sn=01ede4b30bef813627d0d454deb9ead2&3rd=MzA3MDU4NTYzMw==&scene=6#rd)三把轮  
<http://www.jppt.com.cn/xmpublic/>

  
Scriptish : <http://tampermonkey.net/>

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// ==UserScript==
// @name         http://www.jppt.com.cn
// @namespace    http://www.jppt.com.cn
// @version      0.1
// @description  http://www.jppt.com.cn
// @author       You
// @match        http://www.jppt.com.cn/xmllvip
// @runat        document-end
// @grant        none
// ==/UserScript==
if (window.console) {
  console.log('挂学时开始:' + new Date());
}
window.computee = function() {
  if (window.console) {
    console.log('刚刚屏蔽了验证码:' + new Date());
  }
};
setInterval(function() {
  if (currentTrained >= 3.8 * 60 * 60) {
    endTrain();
    alert(
      '现在挂了' +
        formatTime(currentTrained) +
        ',不能连续挂机4个小时，现在帮您退出了'
    );
  } else {
    window.location.href = window.location.href;
  }
}, 4.5 * 60 * 1000);
```