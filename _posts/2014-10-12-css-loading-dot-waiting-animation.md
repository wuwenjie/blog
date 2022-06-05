---
id: 340
title: CSS3实现loading点点点动画效果
date: '2014-10-12T23:28:28+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=340'
permalink: /2014/10/css-loading-dot-waiting-animation
categories:
    - 'HTML&amp;CSS'
---

利用CSS content内容生成技术以及CSS3 animation实现的，并且几乎没有任何的不足，这里给大家展示下如何实现的。

CSS代码如下：

```
<pre class="wp-block-preformatted">dot {
    display: inline-block; 
    height: 1em; line-height: 1;
    text-align: left;
    vertical-align: -.25em;
    overflow: hidden;
}
dot::before {
    display: block;
    content: '...\A..\A.';
    white-space: pre-wrap;   /* 也可以是white-space: pre */
    animation: dot 3s infinite step-start both;
}
@keyframes dot {
    33% { transform: translateY(-2em); }
    66% { transform: translateY(-1em); }
}
```

HTML代码如下：

```
<pre class="wp-block-preformatted">订单提交中<dot>...</dot>
```

然后成就即达成！并且IE6-IE9完美自动向下兼容（静态3个点），IE10+就是动画。

<https://javascript.shop/css%e8%ae%a9conent%e9%87%8c%e7%9a%84%e6%96%87%e5%ad%97%e6%8d%a2%e8%a1%8c-%ef%bc%88content-da%ef%bc%89/> 其中`'\A'`其实指的是换行符中的LF字符，其unicode编码是`000A`，在CSS `content`属性中则直接写作`'\A'`；换行符除了LF字符还有CR字符，其Unicode编码是`000D`，在CSS `content`属性中则直接写作`'\D'`。CR字符和LF字符分别指回车（CR）和换行（LF）。

本文利用`content`实现字符打点loading效果我是在这个github项目看到的：[tawian/text-spinners](https://github.com/tawian/text-spinners)

当然，我自己还有一丝欣慰的，就是站在巨人的肩上，我细节要比原实现要好，好在没有使用任何hack技巧实现的所有浏览器的全兼容（不支持animation的浏览器静态三个点），原来的实现直接忽略了IE9-IE6浏览器，直接没有点。

原来的实现`content`是使用的`<span>`元素，CSS部分使用的是`::after`伪元素，`display`设置为`inline-table`，第1行是`1`个点，代码如下：

```
<pre class="wp-block-preformatted"><span class="loading"></span>
```

```
<pre class="wp-block-preformatted">.loading::after {
  display: inline-table;
  content: "\A.\A..\A...";
  white-space: pre;
  animation: spin4 2s steps(4) infinite;
}
```

而我的实现的是使用自定义的`<dot>`元素，CSS部分使用的是`::before`伪元素，`display`设置为`block`，第1行是`3`个点，代码如下：

```
<pre class="wp-block-preformatted"><dot>...</dot>
```

```
<pre class="wp-block-preformatted">dot::before {
  display: block;
  content: '...\A..\A.';
  white-space: pre-wrap;
  animation: dot 3s infinite step-start both;
}
```

使用自定义`<dot>`元素的好处是IE7,IE8浏览器根本就不认识这个元素，相关CSS会忽略，直接显示3个点；使用`::before`伪元素，`display`设置为`block`原因在于其他现代浏览器可以把原来的`3`个点推到看不见的最下面，显示的是`content`插入的第1行的`3`个点。于是，全浏览器都显示良好。

### animation的一些参数

1. **animation-name**  
    就是`@keyframes`后面跟着的动画名称，本demo本文中名为`dot`，意思为“点”。
2. **animation-timing-function**  
    定义动画播放的方式，参数设置类似`transition-timing-function`.常见缓动类型有：先快后慢(OOXX-out)，还是后快先慢(OOXX-in)，还是中间快两头慢(OOXX-in-out)——(`OOXX = ease`)。这个邪恶记法以前介绍过，不好意思展开解释。而这里使用的是`step-start`, 顾名思意，“一步一步开始”，表现在动画中就是一帧一帧播放、一顿一顿画面；有时候就是需要这样的效果哦！
3. **animation-iteration-count**  
    顾名思意：“动画迭代次数”。默认就`1`次，当然，我们可以设置`2`次，`3`次，`4`次，…`这辈子撸的数目`次。如果撸的次数实在太壮观，建议使用`infinite`关键字，表示无限、无穷无尽，撸到天荒地老，铁杵成针！本demo就是这么干的~~
4. **animation-duration**  
    指一个动画周期持续时间。单位秒`s`或毫秒`ms`.
5. **animatin-delay**  
    指动画延时执行时间。单位秒`s`或毫秒`ms`.
6. **animation-direction**  
    指动画时间轴上帧前进的方向。默认为`normal`, 表示一路向前，往前播放。另外可选值`alternate`，表示动画往前播放完了之后，然后再倒带，倒带到头了再往后播放，来来回回~~本demo如果应用`alternate`值，那效果就会是：一个点→两个点→三个点→三个点→两个点→一个点……；而不是目前展示的一个点→两个点→三个点→一个点→两个点→三个点……
7. **animation-fill-mode**  
    顾名思意，“动画填充模式”，啥子意思呢？我们装修时候，都见过铺地面砖或地板的，地砖与地砖时间只有缝隙的，我们需要填充，如何个填充法，我们就称之为“填充模式”。  
    一个动画周期就好比一块地面砖，动画与动画周期之间就会存在类似的缝隙，`animation-fill-mode`就是确定动画遭遇缝隙时如何“填充”的。一图胜千言：  
    由图可见（网上的解释都TM简单的敷衍）：
    - **none**（默认值），表示动画应用之时、动画延时执行之前之前、以及动画结束之后，元素呈现的都是默认状态。
    - **forwards**，前进，表示动画结束后，元素就是当前动画结束时候的状态。对应`keyframe`中的`"to"`或`"100%"`帧。如果应用`alternate`值，同时无限或偶数次数动画，此时最终`keyframe`是`"from"`或`"0%"`关键帧。
    - **backwards**，返回，表示动画开始之前，元素处于`keyframe`是`"from"`或`"0%"`关键帧的状态。由于大多数动画的`animatin-delay`为0, 由于没有指定`forwards`状态(如：`both`值)，因此我们视觉上看到的表现是：动画结束后，动画回到了起始关键帧状态；实际是**动画开始之前就如此，而不是结束**，万万不可被此假象误导。要想看清现实，可以设置`animation-delay`为非`0`值，我们就可以看到动画开始之前，元素就是`"0%"`起始关键帧状态，而不是元素默认状态。直观且准确反映了`backwards`的准确释义。实际应用中，`animation-delay`设置了非`0`值，同时不是`step-start`动画形式，此参数慎用，除非元素默认状态就是起始帧状态，否则动画犹如抽风了一样~
    - **both**，`forwards`和`backwards`双P, 这是个略考智商的属性，既当爹又当妈的，这可怎么搞！好搞的，如果要求同一时间既爹又妈，你不是人妖，搞不来。但是白天当爹，晚上当妈，我想相对容易多。这里也是如此，`both`是**与**的关系，中文意思是“同时”，表示：动画开始之前是`"from"`或`"0%"`关键帧；动画完成之后是`"to"`或`"100%"`关键帧状态。
8. **animation-play-state**  
    动画运行状态，属性为：`running`（默认）以及`paused`. 这个什么时候有用的，使用`animation`实现视频播放效果的时候。