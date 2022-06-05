---
id: 896
title: Vue2高版本新特性的总结及其使用
date: '2019-11-25T01:00:04+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/11/862-revision-v1'
permalink: '/?p=896'
---

*Vue2的一些新特性总结，要重新捡起Vue的人请花几分钟看看啦。。*

## 一、深度作用选择器（ &gt;&gt;&gt; ）

> 严格来说，这个应该是vue-loader的功能。[“vue-loader”: “^12.2.0”](https://github.com/vuejs/vue-loader/releases/tag/v12.2.0)

在项目开发中，如果业务比较复杂，特别像中台或B端功能页面都不可避免的会用到第三方组件库，产品有时会想对这些组件进行一些UI方面的定制。如果这些组件采用的是有作用域的CSS，父组件想要定制第三方组件的样式就比较麻烦了。

[深度作用选择器( `>>>` 操作符)](https://vue-loader.vuejs.org/guide/scoped-css.html#deep-selectors)可以助你一臂之力。

```
<pre class="wp-block-preformatted"><template>
<div>
   <h1 class="child-title">
       如果你希望 scoped 样式中的一个选择器能够作用得“更深”，例如影响子组件，你可以使用 >>> 操作
   </h1>
</div>
</template>

<script>
export default {
    name: 'child',
    data() {
        return {
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.child-title {
    font-size: 12px;
}
</style>
```

上面的child组件中 .child-title 的作用域CSS设定字体大小为12px，现在想在父组件中定制为大小20px，颜色为红色。

```
<pre class="wp-block-preformatted"><template>
<div>
   <child class="parent-custom"></child>
</div>
</template>
<script>
import Child from './child';
export default {
    name: 'parent',
    components:{
        Child
    },
    data() {
        return {
        }
    }
}
</script>

<style>
.parent-custom  >>> .child-title {
    font-size:20px;
    color: red;
}
</style>
```

效果妥妥的。但是别高兴太早，注意到上面的&lt;style&gt;使用的是纯css语法，如果采用less语法，你可能会收到一条webpack的报错信息。

```
<pre class="wp-block-preformatted"><style lang="less">
.parent-custom {
     >>> .child-title {
        font-size:20px;
        color: red;
    }
}
</style>

ERROR in ./~/css-loader!./~/vue-loader/lib/style-compiler?{"vue":true,"id":"data-v-960c5412","scoped":false,"hasInlineConfig":false}!./~/postcss-loader!./~/less-loader!./~/vue-loader/lib/selector.js?type=styles&index=0!./src/components/parent.vue
Module build failed: Unrecognised input
 @ /src/components/parent.vue (line 22, column 6)
 near lines:
   .parent-custom {
        >>> .child-title {
           font-size:20px;
```

上面的报错信息其实是less语法不认识 &gt;&gt;&gt;。(less的github issue上有人提议支持&gt;&gt;&gt;操作符，但本文使用的v2.7.3会有这个问题)

解决方案是采用的less的转义(scaping)和变量插值(Variable Interpolation)

```
<pre class="wp-block-preformatted"><style lang="less">
@deep: ~'>>>';
.parent-custom {
     @{deep} .child-title {
        font-size:20px;
        color: red;
    }
}
</style>
```

对于其他的css预处理器，因为没怎么用，不妄加评论，照搬一下文档的话。

> 有些像 Sass 之类的预处理器无法正确解析 &gt;&gt;&gt;。这种情况下你可以使用 /deep/ 操作符取而代之——这是一个 &gt;&gt;&gt; 的别名，同样可以正常工作。

## 二、组件配置项inheritAttrs、组件实例属性$attrs和$listeners

> [2.4.0新增](https://github.com/vuejs/vue/releases/tag/v2.4.0)

### 组件配置项 inheritAttrs

官方文档： <https://cn.vuejs.org/v2/guide/components-props.html#%E6%9B%BF%E6%8D%A2-%E5%90%88%E5%B9%B6%E5%B7%B2%E6%9C%89%E7%9A%84%E7%89%B9%E6%80%A7>

我们都知道假如使用子组件时传了a,b,c三个prop，而子组件的props选项只声明了a和b，那么渲染后c将作为html自定义属性显示在子组件的根元素上。

如果不希望这样，可以设置子组件的配置项 inheritAttrs:false，根元素就会干净多了。

```
<pre class="wp-block-preformatted"><script>
export default {
    name: 'child',
    props:['a','b'],
    inheritAttrs:false
}
</script>
```

### 组件实例属性$attrs和$listeners

先看看[vm.$attrs文档](https://cn.vuejs.org/v2/api/#vm-attrs)上是怎么说的

#### vm.$attrs

> 类型：{ \[key: string\]: string }
> 
> 只读
> 
> 包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind=”$attrs” 传入内部组件——在创建高级别的组件时非常有用。

归纳起来就是两点：

1. vm.$attrs是组件的内置属性，值是父组件传入的所有prop中**未被组件声明的prop**(class和style除外)。

还是以前面的child组件举例

```
<pre class="wp-block-preformatted">//parent.vue
<template>
    <div>
        <child class="parent-custom" a="a" b="b" c="c"></child>
    </div>
</template>

//child.vue
<script>
export default {
    name: 'child',
    props:['a','b'],
    inheritAttrs:false,
    mounted(){
        //控制台输出:
        //child:$attrs: {c: "c"}
        console.log('child:$attrs:',this.$attrs);
    }
}
</script>

```

1. 组件可以通过在自己的子组件上使用v-bind=”$attrs”，进一步把值传给自己的子组件。也就是说子组件会把$attrs的值当作传入的prop处理，同时还要遵守第一点的规则。

```
<pre class="wp-block-preformatted">//parent.vue
<template>
    <div>
        <child a="a" b="b" c="c"></child>
    </div>
</template>

//child.vue
<template>
    <div>
        <grand-child v-bind="$attrs" d="d"></grand-child>
    </div>
</template>
<script>
export default {
    name: 'child',
    props:['a','b'],
    inheritAttrs:false
}
</script>

//grandchild.vue
<script>
export default {
    name: 'grandchild',
    props:[],
    //props:['c'],
    inheritAttrs:false,
    mounted(){
        //控制台输出:
        //grandchild:$attrs: {d: "d", c: "c"}
        console.log('grandchild:$attrs:',this.$attrs);

        //如果props:['c']
        //控制台输出:
        //grandchild:$attrs: {d: "d"}
    },
}
</script>
```

#### vm.$listeners

> 类型：{ \[key: string\]: Function | Array }
> 
> 只读
> 
> 包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on=”$listeners” 传入内部组件——在创建更高层次的组件时非常有用。

归纳起来也是两点：

1. vm.$listeners是组件的内置属性，它的值是父组件(不含 .native 修饰器的) v-on 事件监听器。
2. 组件可以通过在自己的子组件上使用v-on=”$listeners”，进一步把值传给自己的子组件。如果子组件已经绑定$listener中同名的监听器，则两个监听器函数会以冒泡的方式先后执行。

```
<pre class="wp-block-preformatted">//parent.vue
<template>
    <div>
        <child @update="onParentUpdate"></child>
    </div>
</template>
<script>
export default {
    name: 'parent',
    components:{
        Child
    },
    methods:{
        onParentUpdate(){
            console.log('parent.vue:onParentUpdate')
        }
    }
}
</script>

//child.vue
<template>
    <div>
        <grand-child @update="onChildUpdate" v-on="$listeners"></grand-child>
    </div>
</template>
<script>
export default {
    name: 'child',
    components:{
        GrandChild
    },
    methods:{
        onChildUpdate(){
            console.log('child.vue:onChildUpdate')
        }
    }
}
</script>

//grandchild.vue
<script>
export default {
    name: 'grandchild',
    mounted(){
        //控制台输出:
        //grandchild:$listeners: {update: ƒ}
        console.log('grandchild:$listeners:',this.$listeners);

        //控制台输出:
        //child.vue:onChildUpdate
        //parent.vue:onParentUpdate
        this.$listeners.update();
    }
}
</script>
```

## 三、组件选项 provide/inject

> [2.2.0 新增](https://github.com/vuejs/vue/releases/tag/v2.2.0)

如果列举Vue组件之间的通信方法，一般都会说通过prop，自定义事件，事件总线，还有Vuex。  
[provide/inject](https://cn.vuejs.org/v2/api/#provide-inject)提供了另一种方法。

> 这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。
> 
> 如果你熟悉 React，这与 React 的上下文特性(context)很相似。

不过需要注意的是，在文档中并不建议直接用于应用程序中。

> provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中。

```
<pre class="wp-block-preformatted">//parent.vue
<template>
    <div>
        <child></child>
    </div>
</template>
<script>
export default {
    name: 'parent',
    provide: {
        data: 'I am parent.vue'
    },
    components:{
        Child
    }
}
</script>

//child.vue
<template>
    <div>
        <grand-child></grand-child>
    </div>
</template>
<script>
export default {
    name: 'child',
    components:{
        GrandChild
    }
}
</script>

//grandchild.vue
<script>
export default {
    name: 'grandchild',
    inject: ['data'],
    mounted(){
        //控制台输出:
        //grandchild:inject: I am parent.vue
        console.log('grandchild:inject:',this.data);
    }
}
</script>
```

> provide 选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的属性。
> 
> inject 选项应该是一个字符串数组或一个对象，该对象的 key 代表了本地绑定的名称，value 就为provide中要取值的key。

在2.5.0+时对于inject选项为对象时，还可以指定from来表示源属性，default指定默认值(如果是非原始值要使用一个工厂方法)。

```
<pre class="wp-block-preformatted">const Child = {
  inject: {
    foo: {
      from: 'bar',
      default: 'foo'
      //default: () => [1, 2, 3]
    }
  }
}
```

## 四、作用域插槽 slot-scope 

官方： [https://cn.vuejs.org/v2/guide/components-slots.html#作用域插槽](https://cn.vuejs.org/v2/guide/components-slots.html#%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%8F%92%E6%A7%BD)

## 五、Vue的错误捕获

1. 全局配置errorHandler

> 从2.2.0起，这个钩子也会捕获组件生命周期钩子里的错误。
> 
> 从 2.4.0 起这个钩子也会捕获 Vue 自定义事件处理函数内部的错误了。

更详细的说明可以查看文档[errorHandler](https://cn.vuejs.org/v2/api/#errorHandler)

1. 生命周期钩子errorCaptured

> [2.5.0+新增](https://github.com/vuejs/vue/releases/tag/v2.5.0)

更详细的说明可以查看文档[errorCaptured](https://cn.vuejs.org/v2/api/#errorCaptured)

如果熟悉React的话，会发现它跟错误边界(Error Boundaries)的概念很像，实际上也确实是这么用的。

在文档[Error Handling with errorCaptured Hook](https://gist.github.com/yyx990803/9bdff05e5468a60ced06c29c39114c6b#error-handling-with-errorcaptured-hook)就举了一个典型的例子

```
<pre class="wp-block-preformatted">Vue.component('ErrorBoundary', {
  data: () => ({ error: null }),
  errorCaptured (err, vm, info) {
    this.error = `${err.stack}\n\nfound in ${info} of component`
    return false
  },
  render (h) {
    if (this.error) {
      return h('pre', { style: { color: 'red' }}, this.error)
    }
    // ignoring edge cases for the sake of demonstration
    return this.$slots.default[0]
  }
})
```

```
<pre class="wp-block-preformatted"><error-boundary>
  <another-component/>
</error-boundary>
```

需要强调的是errorCaptured并不能捕获自身错误和异步错误（比如网络请求，鼠标事件等产生的错误）。

> In 2.5 we introduce the new errorCaptured hook. A component with this hook captures all errors (excluding those fired in async callbacks) from its child component tree (excluding itself).

## 六、 动态指令参数

<https://cn.vuejs.org/v2/guide/custom-directive.html#%E5%8A%A8%E6%80%81%E6%8C%87%E4%BB%A4%E5%8F%82%E6%95%B0>

## 七、事件修饰符 passive， exact

<https://cn.vuejs.org/v2/guide/events.html#%E4%BA%8B%E4%BB%B6%E4%BF%AE%E9%A5%B0%E7%AC%A6>

<https://cn.vuejs.org/v2/guide/events.html#exact-%E4%BF%AE%E9%A5%B0%E7%AC%A6>

## 八、自定义主键v-model的事件修改

<https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model>

## 九、原生事件绑定到组件 $listeners

<https://cn.vuejs.org/v2/guide/components-custom-events.html#%E5%B0%86%E5%8E%9F%E7%94%9F%E4%BA%8B%E4%BB%B6%E7%BB%91%E5%AE%9A%E5%88%B0%E7%BB%84%E4%BB%B6>

十、回顾v-watch的高级用法，比较常用

 <https://cn.vuejs.org/v2/api/#vm-watch>