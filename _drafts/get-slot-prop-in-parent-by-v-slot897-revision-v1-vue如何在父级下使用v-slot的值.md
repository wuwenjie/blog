---
id: 911
title: Vue如何在父级下使用v-slot的值
date: '2019-11-25T02:46:07+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/11/897-revision-v1'
permalink: '/?p=911'
---

## 关于作用域插槽v-slot的用法可以先看看文档 

<https://cn.vuejs.org/v2/guide/components-slots.html#%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%8F%92%E6%A7%BD>

## 现在我们进入正题

我们用到一个第三方组件validate，这个第三方组件的插槽传了一个属性error值给我们，我们现在想要在父级中获得这个error值, 为了简单起见，我们创建一个validate组件作为测试。

我们觉得可以在v-slot下使用一个方法，把error传到父级去，不就行了吗，的确是可以的： <https://jsfiddle.net/jswenjie/pxru6y2m/5/>

<script async="" src="//jsfiddle.net/jswenjie/pxru6y2m/5/embed/js,html,result/"></script>## 问题出现

我们的页面有多个validate的情况下，需要搜集所有的error，那么我们可以用个数组：<https://jsfiddle.net/jswenjie/pxru6y2m/7/>

<script async="" src="//jsfiddle.net/jswenjie/pxru6y2m/7/embed/js,html,result/"></script>我们发现虽然结果是正确的，不过在控制台下出现了warning警告，\[Vue warn\]: You may have an infinite update loop in a component render function. 有死循环的问题。

## 问题解决

为了解决这个问题，我考虑到是否可以用一个组件把 父级和子集关联起来呢，相当于给他们搭建一个桥梁，结果证明是可以的，就是略显繁琐，不知道大家有没有更好的方法呢？代码如下： <https://jsfiddle.net/jswenjie/pxru6y2m/11/>

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">
<html lang="en">
  <head>
    <script src="https://cn.vuejs.org/js/vue.js"></script>
  </head>
  <body>
    <div id="app">
      <validate v-for="x in 2" :key="x" v-slot="{ error: scopeError }">
        in scope {{ scopeError }}
        <slot-bridge
          :error="error"
          :index="x-1"
          :scope-error="scopeError"
        ></slot-bridge>
      </validate>
      <div>in parent {{ error }}</div>
    </div>
    <script>
      Vue.component("validate", {
        data() {
          return {
            error: false
          };
        },
        mounted: function() {
          setInterval(() => (this.error = Date.now()), 1000);
        },
        template: '<div><slot :error="error" /></div>'
      });

      Vue.component("slot-bridge", {
        render: () => null,
        props: ["error", "index", "scopeError"],
        watch: {
          scopeError(val) {
            this.$set(this.error, this.index, val);
          }
        }
      });
      new Vue({
        el: "#app",
        data() {
          return {
            error: []
          };
        }
      });
    </script>
  </body>
</html>
```

<script async="" src="//jsfiddle.net/jswenjie/pxru6y2m/11/embed/js,html,result/"></script></body></html>