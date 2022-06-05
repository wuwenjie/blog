---
id: 518
title: 'Html Notification通知的简单使用（转）'
date: '2016-08-22T23:04:31+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=518'
permalink: /2016/08/html-notification-examples
categories:
    - 'HTML&amp;CSS'
---

**注意  
最新的Chrome的Notification要想有效果需要**`<strong>https</strong>`**协议才行。**

一、传统的通知实现

通知可以说是web中比较常见且重要的功能，私信、在线提问、或者一些在线即时通讯工具我们总是希望第一时间知道对方有了新的反馈，这个时候，就需要页面给予即使的通知。

在以前，我们的通知实现主要是通过闪烁页面的标题内容来实现，实现原理其实很简单，就是定时器不断修改`document.title`的值。

例如：

```
<pre class="wp-block-preformatted">setInterval(function() {
    var title = document.title;
    if (isShine == true) {
        if (/新/.test(title) == false) {
            document.title = '【你有新消息】';    
        } else {
            document.title = '【　　　　　】';
        }
    } else {
        document.title = titleInit;
    }
}, 500);
```

<figure class="wp-block-image">![你有新消息 gif](https://image.zhangxinxu.com/image/blog/201607/new-message.gif)</figure>结果标题就会这样：

窗体失焦的时候，标题就会闪。

这里有一个小的知识点，就是浏览器窗体获得焦点和失去焦点，Chrome和FireFox浏览器是`window`的`onfocus`, `onblur`方法；而IE浏览器则是`document`的`onfocusin`, `onfocusout`方法，因此有：

```
<pre class="wp-block-preformatted">window.onfocus = function() { };
window.onblur = function() { };

// for IE
document.onfocusin = function() { };
document.onfocusout = function() { };
```

如果复杂场景，请使用`addEventListener`和`attchEvent`进行事件绑定。

然而，这种提示有个致命的缺陷，就是用户的浏览器要一直是张开的。比方说用户浏览器最小化，标题就看不见，自然就无法及时get到有新消息的信息。

好了，新技术的出现不会是无缘无故的，总是为解决某一类问题或需求出现的。Web Notification就可以很好地解决上面的痛点。

### 二、HTML5 Web Notification桌面通知特点

HTML5 Web Notification通知是属于桌面性质的通知，有点类似于显示器右下角蹦出的QQ弹框，杀毒提示之类的，跟浏览器是脱离的，消息是置顶的。

比方说，你私信女神说“同事放我鸽子，独立日的票多了一张，浪费了可惜，要不送你？”，然后浏览器爽快地关闭…………的话，你是收不到消息的（*zxx: 此处有更新*），只能最小化，或非当前窗口，然后你就可以安安心心地看《余罪》，没错，就安安心心地看。跟你讲，只要女神一回复，立马，桌面右下角就会出现：“女神说：好啊！”的通知提示，你就可以秒回！女神看到这秒回的速度，心里想的一定是：“这傻小子，一定是一直盯着屏幕等我消息……” 剧情就完全不一样了！

<figure class="wp-block-image">![女神的回复通知](https://image.zhangxinxu.com/image/blog/201607/2016-07-05_220814.png)</figure>**更新于2016-07-12**  
浏览器关闭是否可以接收到Web Notification通知，如果说“收不到”，肯定是不准确的，因为我自己时不时可以收到facebook的通知。但是，恕我愚钝，一直没想明白原理。难道Facebook和Chrome酱有合作，如果是这样那就是特例；如果不是，那通知是哪里发出的呢？

求达人解惑！

**自己更新于2018-08-04**

Facebook的通知是应该是使用的FCM，也就是Firebase云信息传递，是一种跨平台消息传递解决方案。

使用Service Worker注册对象，调这个对象的pushManager.subscribe的方法让浏览器弹一个框，询问用户是否允许接受消息通知。

如果点击允许的话，浏览器就会向FCM请求生成一个subscription（订阅）的标志信息，然后把这个subscription发给服务端存起来，用来发Push给当前用户。服务端使用这个subscription的信息调web push提供的API向FCM发送消息，FCM再下发给对应的浏览器。然后浏览器会触发Service Worker的push事件，让Service Worker调showNotification显示这个push的内容。操作系统就会显示这个Push。

然而，Firebase国内被墙，默认情况下，无法推送消息，大大影响了此功能的收益。要想有用，需要一个境外服务器中转，或用户自己梯子已经架起来了。

**兼容性**  
IE14以及其他桌面浏览器都支持Web Notification，目前移动端的支持情况并不好，Android部分支持，iOS Safari浏览器全军覆没(至9.3版本)：

<figure class="wp-block-image">![Web Notification兼容性截图](https://image.zhangxinxu.com/image/blog/201607/2016-07-05_221531.png)</figure>移动端要是支持的话，我猜，可能会跟原生app的提示效果类似，直接出现在屏幕上，感觉到时候应该蛮吊！

### 三、HTML5 Web Notification语法

```
<pre class="wp-block-preformatted">window.Notification
```

如果浏览器支持Web Notification，不考虑私有前缀，则window.Notification就会是一个有很多静态属性和实例方法的函数。基本上，Web Notification所有的语法都是围绕Notification这个函数来进行的。

显然，通知这种事情是有可能扰民的，因此，必须经过用户同意才行。因此：

**1. Notification.requestPermission()**

<figure class="wp-block-image">![是否允许显示通知](https://image.zhangxinxu.com/image/blog/201607/2016-07-05_220512.png)</figure>这是一个静态方法，作用就是让浏览器出现是否允许通知的提示，window系统Chrome浏览器目前的UI效果是这样的：

语法目前有新旧两种，下面这个是最近规范上更新的基于promise的语法：

```
<pre class="wp-block-preformatted">Notification.requestPermission().then(function(permission) { ... });
```

下面这个是基于简单的回调：

```
<pre class="wp-block-preformatted">Notification.requestPermission(callback);
```

其中`callback`是可选参数，根据[MDN](https://developer.mozilla.org/en-US/docs/Web/API/Notification/requestPermission)的说法，Gecko 46开始舍弃了这种语法，但是，我自己使用FireFox 47测试，跑得很正常（有part4的截图为证）。难道FireFox的版本号不等同于Gecko的版本号？

无论是then中的还是直接callback函数的参数都是一样的，表示当前是否允许。只会是`granted`, `denied`, 或`default`.

其中`granted`表示用户允许通知，`denied`表示用户嫌弃你，`default`表示用户目前还没有管你。

```
<pre class="wp-block-preformatted">Notification.requestPermission().then(function(result) {
  // result可能是是granted, denied, 或default.
});
```

**2. Notification.permission**<sup>\[只读\]</sup>  
这是一个静态属性。表示是否允许通知，值就是上面的`granted`, `denied`, 或`default`.

<figure class="wp-block-image">![默认用户是否允许通知的值](https://image.zhangxinxu.com/image/blog/201607/2016-07-05_224126.png)</figure>默认情况下，`Notification.permission`的值是`'default'`:

因此，`Notification.requestPermission()`的回调方法中，可以不使用`result`参数，直接使用`Notification.permission`获取当前的通知状态。

现在，用户允许出通知了，我们也知道可以出通知了，下面剩下的就是显示通知了。

**3. new Notification(title, options)**  
通过new构造，显示通知。其中`title`是必须参数，表示通知小框框的标题内容，`options`是可选参数，对象，支持的参数以及释义见下表：

| 属性名 | 释义 |
|---|---|
| dir | 默认值是`auto`, 可以是`ltr`或`rtl`，有点类似[direction属性](http://www.zhangxinxu.com/wordpress/2016/03/css-direction-introduction-apply/)。表示提示主体内容的水平书写顺序。 |
| lang | 提示的语言。没看出来有什么用。大家可以忽略之~ |
| body | 提示主体内容。字符串。会在标题的下面显示。比方说上面的“好啊！(害羞.gif)”。 |
| tag | 字符串。标记当前通知的标签。 |
| icon | 字符串。通知面板左侧那个图标地址。 |
| data | 任意类型和通知相关联的数据。 |
| vibrate | 通知显示时候，设备震动硬件需要的[振动模式](https://developer.mozilla.org/en-US/docs/Web/API/Vibration_API#Vibration_patterns)。所谓振动模式，指的是一个描述交替时间的数组，分别表示振动和不振动的毫秒数，一直交替下去。例如\[200, 100, 200\]表示设备振动200毫秒，然后停止100毫秒，再振动200毫秒。 |
| renotify | 布尔值。新通知出现的时候是否替换之前的。如果设为`true`，则表示替换，表示当前标记的通知只会出现一个。注意都这里“当前标记”没？没错，`true`参数要想其作用，必须`tag`需要设置属性值。然后，通知就会像这样覆盖：而不会是默认的叠高楼： |
| silent | 布尔值。通知出现的时候，是否要有声音。默认`false`, 表示无声。 |
| sound | 字符串。音频地址。表示通知出现要播放的声音资源。 |
| noscreen | 布尔值。是否不再屏幕上显示通知信息。默认`false`, 表示要在屏幕上显示通知内容。 |
| sticky | 布尔值。是否通知具有粘性，这样用户不太容易清除通知。默认`false`, 表示没有粘性。根据我自己的猜测，应该和`position`的`sticky`属性值类似。 |

**4. Notification.close()**  
通知显示了，如何关闭呢？可以通过调用`Notification.close()`实例方法，实际上，通知如果你放着不管，一段时间后就会自动隐藏，具体多久不详，我估摸着5秒有的。

**5. 事件句柄**  
**Notification.onclick**  
点击通知，然后……

**Notification.onerror**  
通知显示异常，然后。例如，明明`Notification.permission`是`default`，你还让我显示。

下面这些呢有必要独立出来，虽然现在是支持挺好的，但是，由于目前规范并没有把它们列入其中，所以，未来有可能浏览器就不支持了。  
**Notification.onclose**  
通知关闭了，然后…… 无论是用户手动关闭，还是直接`Notification.close()`关闭都会触发该该事件。

**Notification.onshow**  
通知显示的时候，该干嘛干嘛~~

**6. 其他属性值**  
除了`Notification.permission`外，`Notification`还有很多其他只读属性值，但是，基本上和上面的`options`参数一致，返回的值也是`options`和默认值的合并值（如果浏览器支持的话）。

**Notification.title**<sup>\[只读\]</sup>

**Notification.dir**<sup>\[只读\]</sup>

**Notification.lang**<sup>\[只读\]</sup>

**Notification.body**<sup>\[只读\]</sup>

**Notification.tag**<sup>\[只读\]</sup>

**Notification.icon**<sup>\[只读\]</sup>

**Notification.data**<sup>\[只读\]</sup>

**Notification.silent**<sup>\[只读\]</sup>

**Notification.title**<sup>\[只读\]</sup>

**Notification.timestamp**<sup>\[只读\]</sup>  
通知创建或者可以使用的时间。

**Notification.noscreen**<sup>\[只读\]</sup>

**Notification.renotify**<sup>\[只读\]</sup>

**Notification.sound**<sup>\[只读\]</sup>

**Notification.sticky**<sup>\[只读\]</sup>

**Notification.vibrate**<sup>\[只读\]</sup>

### 四、HTML5 Web Notification实例演示

知道了作用和API，下面就可以来个实例演示下，看看究竟真面目如何。

<figure class="wp-block-image">![demo按钮截图](https://image.zhangxinxu.com/image/blog/201607/2016-07-06_002004.png)</figure>点击demo页面按钮：

<figure class="wp-block-image">![是否允许显示通知](https://image.zhangxinxu.com/image/blog/201607/2016-07-05_220512.png)</figure>如果你是第一次，会有如下提示：

<figure class="wp-block-image">![FireFox浏览器下的提示内容截图](https://image.zhangxinxu.com/image/blog/201607/2016-07-05_223136.png)</figure>通过后，就会出现妹子相关的通知信息了，例如，FireFox浏览器下（使用的是callback回调实现）：

<figure class="wp-block-image">![张小姐加好友截图](https://image.zhangxinxu.com/image/blog/201607/2016-07-06_002320.png)</figure>此时，你浏览器最小化，或者页面刷新，该通知都是纹风不动的。点击通知，可以和页面进行交互，例如，本demo显示了一段文字：

核心代码如下（显示和点击）：

```
<pre class="wp-block-preformatted">if (Notification.permission == "granted") {
    var notification = new Notification("Hi，帅哥：", {
        body: '可以加你为好友吗？',
        icon: 'mm1.jpg'
    });
    
    notification.onclick = function() {
        text.innerHTML = '张小姐已于' + new Date().toTimeString().split(' ')[0] + '加你为好友！';
        notification.close();    
    };
}    
```

完整测试代码参见demo。

至此，一个可以应用于实际开发的Web Notification demo就跃然纸上了。好像facebook已经在使用Web Notification了。