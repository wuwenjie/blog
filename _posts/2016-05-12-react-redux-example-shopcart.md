---
id: 505
title: React+Redux的一个简单开发实例
date: '2016-05-12T22:45:38+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=505'
permalink: /2016/05/react-redux-example-shopcart
categories:
    - Javascript
---

首先，我们来看看这个实例的原型：

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2019/08/屏幕快照-2019-08-23-10.41.13.png)</figure>看到这个水果篮子的样子，大家应该可以明白它的功能：你可以做两件事 — 摘苹果和吃苹果。当你摘苹果时，应用会向后台发送ajax请求索取苹果，每个苹果有两个属性：编号和重量。当你吃苹果掉时，不用告诉后台，在前端偷偷吃掉就好啦~ 同时苹果篮子会显示当前的苹果量和已经吃掉的苹果量。好！那下面我们来开工！

下面先来总体了解一下 redux 应用的基本原理，一图胜千言：

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2019/08/屏幕快照-2019-08-23-10.42.31.png)</figure>可见整个redux流程的逻辑非常清晰，数据流是单向循环的，就像一个生产的流水线：

> store（存放状态） -&gt; container（显示状态） -&gt; reducer （处理动作）-&gt; store

这一点对精细化分工协作很有好处。

我们来看看这三个概念：

- **store** 是应用的状态管理中心，保存着是应用的状态（state），当收到状态的更新时，会触发视觉组件进行更新。
- **container** 是视觉组件的容器，负责把传入的状态变量渲染成视觉组件，在浏览器显示出来。
- **reducer** 是动作(action)的处理中心， 负责处理各种动作并产生新的状态（state），返回给store。

NOTE：从对象的包含关系上讲，reducer 是store的一部分，但在逻辑上我们把它分出来，这样会比较容易理解整个redux流程。

我们可以做个形象的比喻，把 js 比喻成巴士，把 store, container, reducer 比喻为三个车站，再把 state 和 action 比喻成两种乘客。这是一趟环路巴士：

1. *`js巴士`* 从 *`store车站`* 出发，载上 *`state乘客`* ，*`state乘客`* 到达某个 *`container车站`* 下车并把自己展示出来
2. 过了一会，有一个 *`action乘客`* 上车了，*`js巴士`* 把 *`action乘客`* 送到 *`reducer车站`*，在这里 *`action乘客`* 和*`state乘客`* 生了一个孩子 *`new state`*，js巴士把 *`new state`* 送回了 *`store车站`*（好像是人生轮回→\_→）

redux 只是定义了应用的数据流程，只解决了 “数据层”（model layer） 的问题，一般还会使用 react， angular 等作为“显示层” （UI layer） 来一起使用，我们项目采用 react 作为显示框架。

在开始之前，这里先提供一些介绍react和redux的参考资料，如果在下文遇到哪些点不理解，可以来这里翻看参考资料：

- react相关： [react官网](http://facebook.github.io/react/), [react中文站点](http://reactjs.cn/), [阮一峰react 入门](http://www.ruanyifeng.com/blog/2015/03/react.html), [react教程集合](http://react-china.org/c/jiao-cheng)
- redux相关：[redux官网](http://redux.js.org/) , [redux中文文档](http://cn.redux.js.org/)

下文的展示的js代码，会用到少量简单的 [es6](http://es6.ruanyifeng.com/#docs/intro) 语法，可以在遇到时参考这里，或自己查找资料：

- [import](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import) / [export](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/export) ：es6代码模块化模式
- [let 声明语句](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let) ： 块级变量声明语句
- [箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)： (..) =&gt; {..} 形式的函数

我们会使用 babel 编译器把es6语法编译成es5, 所以大家不必担心浏览器兼容性问题，可以大胆使用es6。

应用的基础配置工作由前端开发主管负责，大家不必详细理解。

## 按任务分工来分步讲解

按照开发的内容，我们把前端团队分为两个小组： “布局组” 和 “逻辑组”，每个小组有2种任务，一共4种任务。

- **布局组** – 负责 contianer、component 部分
    - 任务1：**静态布局** – 使用 HTML + CSS 静态布局
    - 任务2：**动态布局** – 使用 JSX 语法对静态布局做动态渲染处理
- **逻辑组** – 负责 action、reducer 部分
    - 任务1：**action 开发** – 制作 redux 流程的 action
    - 任务2：**reducer 开发** – 制作 redux 流程的 reducer

**布局组** 要求对 HTML + CSS 布局比较熟悉，只需要会简单的 js 即可， 不需要完整地理解redux流程。  
**逻辑组** 要求对 js 比较熟悉，最好可以比较完整地理解redux流程， 但基本不需要涉及HTML + CSS布局工作。

接下来，先给出我们教程相关的 src 目录。这里大家可以先一扫而过，大概了解即可

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">- src              源码文件夹：包含项目源码，我们基本都在这个文件夹下做开发
    - containers   容器文件夹：存放容器组件，比如 “苹果篮子”
    - components   组件文件夹：存放普通显示组件，比如 “苹果”
    - actions      actions文件夹：存放可以发出的action 
    - reducers     reducers文件夹：存放action的处理器reducers
    - services     服务文件夹：存放经过封装的服务，如 ajax服务, 模拟数据服务
    - styles       样式文件夹：存放应用的样式，如css, scss
    - images       图片文件夹：存放图片资源
    - apis         开发接口文件夹：存放开发接口文档
```

下面正式开始啦，先从布局组开始。

## 布局组

### 任务1：静态布局

- 能力要求：只需要会使用 HTML + CSS （SASS）进行布局即可
- 任务内容：1. 苹果篮子组件（容器组件） 2. 水果组件（显示组件）

redux 的组件分为两类，一类是**容器组件 container** ，一类是**普通显示组件 component**。容器负责接收store中的state和并发送action, 大多数时候需要和store直接连接，容器一般不需要多次使用，比如我们这个应用的苹果篮子。普通组件放在容器里面，由容器确定显示的时机、数量和内容，普通组件一般会多次使用。

#### 1. 苹果篮子容器 AppleBasket.jsx + appleBasket.scss

苹果篮子组件的原型如下：

<figure class="wp-block-image">![苹果篮子](https://segmentfault.com/img/remote/1460000007633604)</figure>对于容器，我们使用 React 组件类 的方式书写，这里主要是静态的jsx代码，相当于html。

下面是 *AppleBasket.jsx*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import React from 'react';
import { connect } from 'react-redux';

class AppleBusket extends React.Component {
  
  render(){
    return (
      <div className="appleBusket">
        <div className="title">苹果篮子</div>
        
        <div className="stats">
            <div className="section">
                <div className="head">当前</div>
                <div className="content">0个苹果，0克</div>
            </div>
            <div className="section">
                <div className="head">已吃掉</div>
                <div className="content">2个苹果，480克</div>
            </div>            
        </div>
                    
        <div className="appleList">
            <div className="empty-tip">苹果篮子空空如也</div>
        </div>
        
        <div className="btn-div">
            <button>摘苹果</button>
        </div>
        
      </div>
    );
  }

}

export default connect()(AppleBusket);
```

同时静态布局开发人员还要负责书写样式，宅印的样式使用sass 书写， 下面是的例子是*appleBasket.scss* , 大家可以做参考：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">.appleBusket{
    width: 400px;
    margin: 20px;
    border-radius: 4px;
    border: 1px solid #ddd;
    >.title{
        padding: 6px 0px;
        text-align: center;
        color: #069;
        font-size: 20px;
        //font-weight: 600;
    }
    >.stats{
        width: 100%;
        $border: 1px dashed #ddd;
        border-top: $border;
        border-bottom: $border;
        padding: 10px 0px;
        .section{
            display: inline-block;
            width: 50%;
            padding-left: 8px;
            .head{
                padding: 6px 0px;
                font-size: 16px;
                color: #069;
            }
            .content{
                font-size: 20px;
                font-weight: 200;
            }
            &:first-of-type{
                border-right: 1px solid #f0f0f0;
            }
        }
    }
    >.appleList{
        padding: 10px 0px;
        .empty-tip{
            text-align: center;
            font-size: 16px;
            color: #ccc;
            padding: 20px 0px;
        }
    }
    >.btn-div{
        text-align: center;
        button{
            color: #fff;
            background-color: #096;
            border: none;
            font-size: 14px;
            padding: 6px 40px; 
            border-radius: 6px;
            margin: 20px auto;
        }
    }
}
```

#### 2. 苹果组件 AppleItem.jsx + appleItem.scss

苹果组件的样子如下：

<figure class="wp-block-image">![苹果组件](https://segmentfault.com/img/remote/1460000007633605)</figure>普通组件的定义方法和容器类似，只是其不需要使用redux连接器来封装，如下：

*AppleItem.jsx*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import React from 'react';

class AppleItem extends React.Component {

    render() {

        return (
            <div className="appleItem">
                <div className="apple"><img src="../images/apple.png" alt=""/></div>
                <div className="info">
                    <div className="name">红苹果 - 1号</div>
                    <div className="weight">265克</div>
                </div>
                <div className="btn-div"><button>吃掉</button></div>
            </div>
        );

    }


}

export default AppleItem;
```

样式文件 appleItem.scss 在此省略。

哪些显示元素要作为容器，哪些要作为普通组件，没有百分之百确定划分规则，大家根据自己的理解把它划分到某一类即可。

这些就是任务一的内容，书写静态布局，基本都是html+css工作，不需要涉及js代码。

### 任务2：动态渲染

写完了静态布局后，接下来要进行动态渲染啦~

动态渲染听起来很高大上，其实意思就是根据一个stete数据对象来显示内容而已。首先需要确定其state的结构。**容器**的state 是 store 中state的一部分，前端管理员会事先约定好其数据结构，并在对应的reducer中给出，只要去那里复制一份出来即可。**普通组件**的state只要自己定义即可，并在文件中说明清楚。

#### 1. 容器的动态渲染

下面看看“苹果篮子”的动态布局，我们去 appleBasketReducer.js 可以得到水果篮子的 state 的结构如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
    isPicking : false,
    newAppleId: 1,
    apples: [
        /*{
            id: 0,
            weight: 235,
            isEaten: false
         }*/
    ]
}
```

我们这个数据结构把它 “实例化”，如下这样放在我们改成写的 AppleBasket.jsx 中，然后我们开始书写我们的动态渲染代码啦，如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import React from 'react';
import { connect } from 'react-redux';

import AppleItem from '../components/AppleItem';

class AppleBusket extends React.Component {

    render() {
        
        let { state } = this.props;
        
        //这部分从对应的 appleBasketReducer.js 中拷贝
        let mockState = {
            isPicking : false,
            newAppleId: 3,
            apples: [
                {
                    id: 1,
                    weight: 235,
                    isEaten: true
                },
                {
                    id: 2,
                    weight: 256,
                    isEaten: false
                }
            ]
        };
        
        //是否开启模拟数据的开关，注释这行代码关闭模拟数据
        state = mockState;
        
        
        //对 state 做显示级别的转化
        let stats = {
            appleNow: {
                quantity: 0,
                weight: 0
            },
            appleEaten: {
                quantity: 0,
                weight: 0
            }
        };
        
        state.apples.map(apple => {
            let selector = apple.isEaten ? 'appleEaten':'appleNow';
            stats[selector].quantity ++;
            stats[selector].weight += apple.weight;
        })
        
        
        return (
            <div className="appleBusket">
                <div className="title">苹果篮子</div>

                <div className="stats">
                    <div className="section">
                        <div className="head">当前</div>
                        <div className="content">
                            {stats.appleNow.quantity}个苹果，
                            {stats.appleNow.weight}克
                        </div>
                    </div>
                    <div className="section">
                        <div className="head">已吃掉</div>
                        <div className="content">
                            {stats.appleEaten.quantity}个苹果，
                            {stats.appleEaten.weight}克
                        </div>
                    </div>
                </div>

                <div className="appleList">
                    { state.apples.map(apple => <AppleItem state ={apple} />) }
                </div>

                <div className="btn-div">
                    <button>摘苹果</button>
                </div>

            </div>
        );
    }

}

function select(state) {
    return {
        state: state.appleBusket
    }
}

export default connect(select)(AppleBusket);
```

可见，动态布局的工作要求只是在 HTML + CSS 布局的基础上，再加上 JSX 语法能力即可。

#### 2. 普通显示组件的动态渲染

普通显示组件的动态渲染和容器类似，只是这里的state可以自己规定，并给出示例的mockState（模拟state），使用组件的人按照示例传入数据即可使用。

AppleItem.jsx 的更新如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import React from 'react';

class AppleItem extends React.Component {

    shouldComponentUpdate(nextProps) {
        return nextProps.state != this.props.state;
    }

    render() {

        let { state, actions } = this.props;

        /**
         * 这个区域是 mock 数据区，也作为组件文档，请书写清楚
         * //在组件发布时，请注释掉，提高性能
         */
        let mockState = {
            id: 1,
            weight: 256,
            isEaten: false
        };
        
        let mockActions = {
            eatApple : id => console.log('eatApple',id)
        };

        /**
         * 开关这行代码，用于切换装入的数据来源。(为了开关的方便，请把两句代码合成一行)
         * 在开发阶段打开，使用内部 state 和 action, 开发完成后请注释关闭
         */
        state = mockState; actions = mockActions;

        if (state.isEaten) return null;

        return (
            <div className="appleItem">
                <div className="apple"><img src="../images/apple.png" alt=""/></div>
                <div className="info">
                    <div className="name">红苹果 - {state.id}号</div>
                    <div className="weight">{state.weight}克</div>
                </div>
                <div className="btn-div"><button onClick={() => actions.eatApple(state.id) }>吃掉</button></div>
            </div>
        );

    }


}

export default AppleItem;
```

容器和普通显示组件state的对比：

1. 容器的state我们是从store中的总state直接获得的，注意 AppleBusket.jsx 靠后面这段代码：`<strong>function</strong> <strong>select</strong>(state) {    <strong>return</strong> {        state: state.appleBusket    }}`select是一个state筛选器, 功能是选择总state中的 appleBusket 作为本容器的state。而这个state的格式我们会在其对应的reducer中规定（因为我们需要在reducer中提供对应state的默认值）
2. 普通显示组件的state格式由组件开发人员自己约定即可，并在mockState 区域给出例子。当别人要使用你的显示组件时，必须根据你规定的格式传入state数据。在组件里面，我们一般会实现如下这样一个自动切换器，当组件的使用者在使用该组件时没有传入state, 就会显示内部的模拟state，为使用者带来预览效果。`<strong>if</strong>(<strong>state</strong> === undefined) <strong>state</strong> = mockState;`

以上就是**布局组**的开发工作： 静态布局 + 动态布局。前者只需要会html+css布局即可，后者还要会一些jsx的语法和基本的js语法。

## 逻辑组

### 任务1：**action 开发** 

任务内容：开发 redux 流程的 action，并把action部署到对应容器和组件中。  
技能要求：需要对js比较熟悉，并要求要会使用jq的ajax功能。

首先，我们先来看看 action 的定义。

**1. 狭义的 action**

狭义的action是指一个简单的对象，对象的结构如下，只要在对象内包含type属性指明action的名称即可，同时，在对象的其他属性里，可以以任何形式自由保存其他相关数据。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let action = {
    type: 'ACTION_NAME',
    ...
}
```

一般 type 的内容使用 大写字母+下划线 的格式。

在这个定义的基础上，业界提出以一种标准 action， 叫做 [Flux Standard Action](https://github.com/acdlite/flux-standard-action) , 该标准下的action除了type属性之外，只允许多加（不是必须包含）这三个属性：payload，error，meta。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let FSA = {
    type: 'ACTION_NAME',
    payload: <bool | number | string | object>, //action的负载，可以是数据或 error 对象
    error: <bool>, // 指明该action是否是一个以 error 为负载的action
    meta: <string> // action元数据， 包含解释该action含义的信息
}
```

我们宅印约定都要使用 Flux Standard Action，下面是吃苹果 action：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let FSA = {
    type: 'EAT_APPLE',
    payload: 3, // 负载是3, 说明吃掉3号苹果， 这里也可以写成 { id : 3 }
    meta: 'This action will eat an apple!' // (不是必须的)
}
```

一个action只是一个对象，他需要根据时机被 store 的 dispatch 函数调用才会开始进行处理：`store.dispatch(action_1)`。

**2. 广义的 action**

广义的 action 是指在中间件的支持下，dispatch 函数可以调用的数据类型，除了普通action之外，常见的有 thunk, promise 等。我们用常用的 thunk来举个例子。

thunk 其实就是一个代码片段，可以简单理解为一种特定的函数，我们可以dispatch 这个thunk。 thunk函数具有如下的签名

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">(dispatch, getState) => { 
    //在函数体内可以使用 dispatch 方法来发射其他 action
    //在函数体内可以使用 getState 方法来获取当前的state
}
```

下面是一个我们摘苹果动作的例子：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let pickAppleAction = (dispatch, getState) => { 
    ajax({
        url: '/pickApple',
        method: 'GET',
    }).done(data => {
        //发射普通 action
        dispatch({
            type: 'DONE_PICK_APPLE',
            payload: data.weight // 或者 payload: {weight: data.weight}
        }); 
    }).fail(xhr => {
        //发射普通 action, 其负载是一个error
        dispatch({
            type: 'FAIL_PICK_APPLE',
            payload: new Error(xhr.responseText) ,
            error: true
        }); 
    })
}
        
```

定义好之后，我们可以直接这样调用这个thunk：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">dispatch( pickAppleAction )
```

接下来，我们来做进一步优化，把action统一为actionCreator , 我们不难察觉，每次都要书写{ type: ‘ACTION\_NAME’ … } 是很麻烦也很容易出错的，actionCreator 就是为解决这个问题而生的，actionCreator 就是一个产生特定action（这里指广义的action）的函数，下面来看简单的actionCreator 例子：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">//传统写法
var eatApple = function(id) {
    return {
        type: 'EAT_APPLE', 
        payload: id
    }
}

// es6 写法
let eatApple = id => ({
    type: 'EAT_APPLE', 
    payload: id
})
```

这样一来，一方面是使用起来比较简单方便，另一方面是具有文档作用。  
只需要这样发射action就可以啦：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">dispatch(eatApple(3))
```

普通action的actionCreator封装工作, 可以使用 [redux-actions](https://github.com/acdlite/redux-actions) 自动完成, 查看其文档就可以快速上手，可以根据情况使用。

在项目中，我们会为每个板块写一个的action文件，并统一使用actionCreator, 所以最终 appleAction.js 如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import ajax from '../services/ajax'; //经过封装的加强型 ajax 函数

//这是名空间，对普通action做划分
const prefix = 'apple/';

let actions = {

    //注意这里需要 () => ... , 不然 pickAppleAction 不是一个actionCreator, 而是一个thunk
    pickApple: () => (dispatch, getState) => {
        
        //如果正在摘苹果，则结束这个thunk, 不执行摘苹果
        if(getState().isPicking)
            return;
        
        //通知开始摘苹果
        dispatch(actions.beginPickApple());
        
        //发送摘苹果请求
        ajax({
            url: '/appleBasket/pickApple',
            method: 'GET'
        }).done(data => {
            dispatch(actions.donePickApple(data.weight))
        })
        .fail(xhr => {
            dispatch(actions.failPickApple(xhr.responseText));
        })
    },
    
    beginPickApple: () => ({
        type: 'apple/BEGIN_PICK_APPLE'
    }),
    
    donePickApple: appleWeight => ({
        type: 'apple/DONE_PICK_APPLE',
        payload: appleWeight
    }),
    
    failPickApple: errMsg => ({
        type: 'apple/FAIL_PICK_APPLE',
        payload: new Error(errMsg),
        error: true
    }),
    
    eatApple: appleId => ({
        type: 'apple/EAT_APPLE',
        payload: appleId
    })

};

export default actions;
```

这样一来，只要引入 appleAction.js ，就可以快速使用定义好的action，结合某些编辑器的智能提示功能，非常方便，下面是 vsCode 编辑器的效果：

<figure class="wp-block-image">![导入苹果篮子板块的action](https://segmentfault.com/img/remote/1460000007633606)</figure><figure class="wp-block-image">![vsCode中的智能提示 ](https://segmentfault.com/img/remote/1460000007633607)</figure>写好了action之后，只要在 container 的对应位置装上action就好了, 下面是appleBasket.jsx 总体代码:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import React from 'react';
import { connect } from 'react-redux';

import AppleItem from '../components/AppleItem';
import actions from '../actions/appleActions';

class AppleBusket extends React.Component {
    render() {
        let { state, dispatch } = this.props;
        ...
        return (
          ...
          <div className="appleList">
              { state.apples.map(apple =>
                  <AppleItem
                      state ={apple}
                      actions={{eatApple: id => dispatch(actions.eatApple(id))}}
                      key={apple.id}
                  />
              ) }
          </div>

          <div className="btn-div">
              <button onClick={() => dispatch(actions.pickApple())}>摘苹果</button>
          </div>
          ...
       )
    }
}

function selectState(state) {
    return {
        state: state.appleBusket
    }
}
export default connect(selectState)(AppleBusket);
```

注意这两行。就是装入action的地方

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">actions={{eatApple: id => dispatch(actions.eatApple(id))}}
<button onClick={() => dispatch(actions.pickApple())}>摘苹果</button>
```

上面代码中引入的actions其实是actionCreators。

此外，actionCreator还有很简洁的用法：对actionCreator做dispatch级别的封装，这个过程我们可以使用 redux 提供的 bindActionCreators 函数自动完成。然后就可以直接调用action，而不需要使用dispatch方法去调用actionCreator。看下面更新后的代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import React from 'react';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

import AppleItem from '../components/AppleItem';
import actions from '../actions/appleActions';

class AppleBusket extends React.Component {
    render() {
        let { state, actions} = this.props;
        ...
        return (
          ...
          <div className="appleList">
              { state.apples.map(apple =>
                  <AppleItem
                      state ={apple}
                      actions={{eatApple: actions.eatApple}}
                      key={apple.id}
                  />
              ) }
          </div>

          <div className="btn-div">
              <button onClick={actions.pickApple}>摘苹果</button>
          </div>
          ...
       )
    }
}

function selectState(state) {
    return {
        state: state.appleBusket
    }
}

function buildActionDispatcher(dispatch) {
  return {
      actions: bindActionCreators(actions, dispatch)
    }
}

export default connect(selectState, buildActionDispatcher)(AppleBusket);
```

注意这三个变动：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let { state, actions } = this.props;
actions={{eatApple: actions.eatApple }}
<button onClick={actions.pickApple}>摘苹果</button>
```

我们对比一下之前的写法：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let { state, dispatch } = this.props
actions={{eatApple: id => dispatch(actions.eatApple(id))}}
<button onClick={() => dispatch(actions.pickApple())}>摘苹果</button>
```

可以发现使用新的方式使代码简洁了很多！

但是，这对于有对象属性提示功能编辑器来说，这种方式会使编辑器无法分析对象属性：

<figure class="wp-block-image">![不能判断出对象的属性，大家可以对比上一图的效果](https://segmentfault.com/img/remote/1460000007633608)</figure>这时，需要一边看actions文件对该actions对象的定义，一边在目标位置填入action，不过这也不是很麻烦。而且对于使用没有对象属性提示的编辑器的开发者来说，这个 drawback 根本就不存在。我们相对推荐使用这种经过dispatch封装的action, 但不要求，大家根据自己的情况使用即可。

**对于普通显示组件**

对于普通显示组件的actions传递方式，我们统一使用actions属性传递，如下：

*AppleBasket.jsx*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">...
<AppleItem
    state ={apple}
    actions={{eatApple: actions.eatApple }}
    key={apple.id}
/>
...
```

*AppleItem.jsx*

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">...
<button onClick={() => actions.eatApple(state.id)}>吃掉</button>
...
```

普通显示组件使用统一actions属性接受父级的action，可以在组件内部建立mockActions, 这个mockActions 既有文档功能，也有测试功能，非常实用：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let mockActions = {
    eatApple : id => console.log('eatApple',id), //指定了函数的签名
    foo: (arg1,arg2) => console.log('foo',arg1,arg2) //也响应了调用测试
};

/**
  * 开关这行代码，用于切换装入的state和actions来源。(为了开关的方便，请把两句代码合成一行)
  * 在开发阶段打开，使用内部 state 和 action, 开发完成后请注释关闭
  */
state = mockState; actions = mockActions;    
```

点击 “摘苹果” 和 “吃掉” 按钮，我们看看控制台，已经可以发出我们想要的action啦：

<figure class="wp-block-image">![该log功能需要 redux-log 中间件支持](https://segmentfault.com/img/remote/1460000007633609)</figure>好啦，actions 开发的内容就介绍到这里。我们来总结一下我们对action所做的封装：

> **action** -&gt; **actionCreator** -&gt; **actionDispatcher**

### 任务2：reducer 开发

开发内容： reducer的其实就是action的**处理器**。其开发的内容很明确清晰，就是开发一类函数，接受action 和 当前的state，返回新的state。

技术要求：要求对js比较熟悉，需要会使用 immutable.js 这个数据静态化库。

下面是reducer功能的图解：

<figure class="wp-block-image">![reducer的功能](https://segmentfault.com/img/remote/1460000007633610)</figure>我们先看看我们苹果板块的state的数据结构，非常简单，这里是某个时刻的状态：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
    isPicking : false,
    newAppleId: 1,
    apples: [
        {
            id: 0,
            weight: 235,
            isEaten: false
        }
    ]
}
```

有三个一级属性：

- **isPicking** ：表示是否正在摘苹果，我们在上面已经知道，摘苹果其实是发送一个ajax请求，向后台摘一个苹果，这个请求在进行时我们会把 isPicking 设置为ture, 表明正在摘苹果，同时禁止在完成前再发送摘苹果请求
- **newAppleId**：表示新苹果的编号
- **apples**：是苹果列表数组，存放着苹果对象，苹果对象的结构在apples数组里有给出实例。

我们上面提及actions分为广义的action和狭义的普通action。其实，非普通action, 如thunk，一般会以发起普通action结束。我们reducer只需要处理狭义上的普通action,。在我们摘苹果应用里，总共有这4个普通action:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">//通知store应用开始摘苹果
beginPickApple: () => ({
    type: 'apple/BEGIN_PICK_APPLE'
}),

//摘苹果成功    
donePickApple: appleWeight => ({
    type: 'apple/DONE_PICK_APPLE',
    payload: appleWeight
}),

//摘苹果失败    
failPickApple: error => ({
    type: 'apple/FAIL_PICK_APPLE',
    payload: error,
    error: true
}),

//吃苹果
eatApple: appleId => ({
    type: 'apple/EAT_APPLE',
    payload: appleId
})
```

下面是根据action，写出的 reducer 的基本结构：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""> (state = defaultState, action) => {

    switch (action.type) {
        case 'apple/BEGIN_PICK_APPLE':
            //TODO
            return state;
            
        case 'apple/DONE_PICK_APPLE':
            //TODO
            return state;
            
        case 'apple/FAIL_PICK_APPLE':
            //TODO
            return state;
            
        case 'apple/EAT_APPLE':
            //TODO
            return state;
            
        default:
            return state;
    }
  
};
```

我们可以看到，reducer是一个函数，接受state和action两个参数，在函数内部，根据 action.type 来确定要做哪些操作，并且每种操作都要返回state（或者是新的，或者是原来的）。

我们以 `apple/EAT_APPLE`动作为例，讲解如何书写reducer。EAT\_APPLE 动作的含义是吃苹果，我们可以非常简单地处理这个动作：直接把对应苹果对象的 isEaten 属性设为true即可。

按照一般的思维，我们会这样处理：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">...
    case 'apple/EAT_APPLE':
        state.apples[action.payload].isEaten = true;
        return state;
...
            
```

但是，这种方法在 redux 应用里看不到作用，因为这种写法不会使store触发react进行重新渲染，为什么呢？因为 `newState == oldState` ! 下面我们来做一些解释：

首先，要先从js对象的相等判断运算说起，我们看下面的代码

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let a = { foo: 'bar'};
let b = { foo: 'bar'};
console.log( a == b ); //结果是 false
```

a 和 b 看起来一样，但为什么是false呢？因为对象和数组的赋值是引用赋值， a 和 b 只是一个引用符号，其所指向的对象实体不同（比如 a -&gt; object#001, b -&gt; object#002），js的对象（数组）相等判断是根据是否指向同一个对象实体来的确定的 (object#001 ？= object#002 // false)，详见 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Equality)。

再看看下面的例子：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">let a = {foo: 'bar'};
let b = a;
b.foo = 'good';

console.log( a == b ); //结果是 true
```

现在应该可以理解刚才为什么`newState == oldState`了吧~

redux 是根据返回的state是否改变来决定是否通知 react 更新的。根据这种情况所，可能有人会这样改进刚才的reducer：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">    state.apples[action.payload].isEaten = true;
    newState = Object.assign({},state);
    return newState;
```

这样一来，点击 “吃掉”按钮，看到了有效果了，苹果不见了！但是，这种写法只是迎合了redux更新视觉组件的触发条件，还具有很大的局限性，不是我们redux规定的reducer。下面我们来看看正确的reducer：

首先，reducer有这样的重要约束：**在reducer里，不可以修改原来的state，需要保持使每个版本的state不变**。

这种保持数据不变（[Persistent data structure](https://en.wikipedia.org/wiki/Persistent_data_structure)）的方式在函数式编程（[Functional programming](https://en.wikipedia.org/wiki/Functional_programming)）非常常见。在我们的redux应用里，其意义在于：

**1. 调试意义**：保持每个版本的state的不变性，使得我们可以跟踪每个时刻的state, 跟踪应用的“发展史”，这个特性为调试带来了很大的方便。

<figure class="wp-block-image">![我们点击了摘苹果按钮，最终发起了两个(普通)action, 我们可以轻松查看action前后的state](https://segmentfault.com/img/remote/1460000007633611)</figure>**2. 性能意义**：保持state不变这个约束引导我们使用**局部更新对象的方法**，这样会可以非常有效地提高react或其他显示框架的渲染效率。我们先来看看为了保持数据不变性，要怎么对state做更新，以我们的苹果篮子state为例：

例子：通知开始摘苹果：`apple/BEGIN_PICK_APPLE`

为了保证每个版本的state不变性，我们有两种实现方式：“深复制”，“浅复制”。我们来看两种模式的内部原理：

**深复制方式**：有人会这样想：“保持state的不变性很容易，只需要深复制一个state, 让后在新的state要怎么修改就怎么修改，不ok了吗？”，如下就是深复制

<figure class="wp-block-image">![深复制](https://segmentfault.com/img/remote/1460000007633612)</figure>这种方式是一种很低级保持不变性的方式：

1. 深复制操作运行效率低
2. 没有为渲染环节提供提高渲染效率的铺垫

它只是简单迎合保持数据不变性的约束，虽然有一定调试意义，但是，不但没有提高程序的性能，反而降低了程序的总体性能！没有实践意义。

**浅复制方式**：浅复制模式只对内部数据发生变化的引用做更新，如下

<figure class="wp-block-image">![Paste_Image.png](https://segmentfault.com/img/remote/1460000007633613)</figure>“state” 对象的内部数据发生变化，所以创建新的state引用；而apples array 内部数据不发生变化，所以就不对该引用做更新！在这个操作中，这种浅复制的方法运行效率比较高，而且其简单地实现了数据不变性，为调试带来方便，同时，也是更重要的，这种浅复制的方式极大地提高了视觉组件渲染阶段的运行效率！我们来对比一下：当用户点击摘苹果时，如果使用“深复制”，渲染程序需要重新遍历整个state对象树来做视觉更新，而使用浅复制来实现数据不变性时，渲染程序只需要遍历state对象的**一级子节点**即可，而不需要对apples array 做遍历，性能大大地提高。尤其是当苹果很多的时候，两种方式的性能差距是非常明显的。

备注：在react组件里面，要开启条件更新这个生命周期函数 shouldComponentUpdate， 才会对把这个性能提高点释放出来，类似这样：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">...
shouldComponentUpdate(nextProps) {
    return nextProps.state != this.props.state;
}
...
```

下面我们再给出 “吃苹果” reducer 进行浅复制的例子：

<figure class="wp-block-image">![吃苹果过程](https://segmentfault.com/img/remote/1460000007633614)</figure>现在大家应该理解了用浅复制实现数据不变性的原理和意义了，下面我们来看具体的代码实现。

我们的代码用 es6 编写，这里要用到 es6 两个非常方便的特性：

1. [Obejct.assign()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 方法，该方法用于产生新的对象
2. 延展操作符 [Spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) : …

大家可以稍微看一下文档，或者看我下面的例子就知道其用法了：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// apple basket reducer

export default (state = {
    isPicking: false,
    newAppleId: 1,
    apples: [
        {
            id: 0,
            weight: 235,
            isEaten: false
        }
    ]
}, action) => {
    
    let newState ;
    

    switch (action.type) {
        case 'apple/BEGIN_PICK_APPLE':
            newState = Object.assign({}, state, {
                isPicking: true
            });
            return newState;

        case 'apple/DONE_PICK_APPLE':
            newState = Object.assign({}, state, {
                apples: [
                    ...state.apples,
                    {
                        id: state.newAppleId,
                        weight: action.payload,
                        isEaten: false
                    }
                ],
                newAppleId: state.newAppleId + 1,
                isPicking: false
            })
            return newState;

        case 'apple/FAIL_PICK_APPLE':
            //这里只是简单处理
            newState = Object.assign({}, state, {
                isPicking: false
            });
            return newState;

        case 'apple/EAT_APPLE':
            newState = Object.assign({}, state, {
                apples: [
                    ...state.apples.slice(0, action.payload),
                    Object.assign({}, state.apples[action.payload], { isEaten: true }),
                    ...state.apples.slice(action.payload + 1)
                ]
            })
            return newState;

        default:
            return state;
    }

};
```

大家会发现这种浅复制操作在开发的过程会复杂一点，于是就有了 [immutable.js](http://facebook.github.io/immutable-js/) 这个专门处理不变性数据的库(也是facebook出品)，它可以使用类似赋值的方式生成浅复制的不变性数据，下面来看看它怎么简化我们的开发：

我们用 `apple/EAT_APPLE` 这个reducer来说明。

这是原来的 reducer：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">...
case 'apple/EAT_APPLE':
    newState = Object.assign({}, state, {
        apples: [
            ...state.apples.slice(0, action.payload),
            Object.assign({}, state.apples[action.payload], { isEaten: true }),
            ...state.apples.slice(action.payload + 1)
        ]
    })
    return newState;
...
```

这是使用 immutable.js 库的reducer :

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import { fromJS } from 'immutable';
...
case 'apple/EAT_APPLE':
    return fromJS(state).setIn(['apples',action.payload,'isEaten'], true).toJS();
...
```

用了immutable.js后，轻松一行代码搞定！如果团队约定 state 都用 immutable 内部的数据类型，就可以连 fromJS 和 toJS 的转化都省了，超级方便！

到这里， reducer 任务的介绍就结束啦~

## 总结

至此，我们四个任务都介绍完了，大家应该对redux流程有一定概念了，我们来回顾一下我们的4个任务：

<figure class="wp-block-image">![](https://javascript.shop/wp-content/uploads/2019/08/屏幕快照-2019-08-23-10.49.00.png)</figure>这样子，我们通过流程化把 react + redux 的主要流程都定义好了，这种模式的可构建性很高，可以构建非常复杂的单页面应用，不会因为应用的业务复杂性增加而增加开发复杂性。

并且在这种分工里面，布局组对专注于写样式布局，大多是基本的HTML+CSS 工作；逻辑组专注于开发应用逻辑，基本都是JS工作，分工得到非常明确的规划，人们可以更好地做精自己负责的工作，各个部件的耦合性很低，人员安排灵活性比较大。

这就是我们用苹果篮子实例讲解的react+redux开发流程，大家形成redux流程基本的概念就好，具体的理解还是要在实践中慢慢参透。

一些依赖的JS库没有在这里给大家介绍，大家可以在后面的相关js库中查看。