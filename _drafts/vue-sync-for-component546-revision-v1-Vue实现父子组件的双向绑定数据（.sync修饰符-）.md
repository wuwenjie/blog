---
id: 547
title: 'Vue实现父子组件的双向绑定数据（.sync修饰符 ）'
date: '2019-08-18T00:20:03+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/546-revision-v1/'
permalink: '/?p=547'
---

## 1.前言

最近在vue 项目中有一个需求， 就是我需要根据不同的类型在页面中放不同的组件， 组件需要跟当前页面的数据进行双向绑定，如果都写在同一个页面 代码会显得比较多，毕竟我当前页面已经7-800行代码了 所以我需要把一些元素定义成组件 ，封装起来，所以就会遇到 数据的传值绑定问题  
在这里我就分享我的方法，也许很多博客上有过！

## 2.父组件

首先我们来看看官方文档 \[[https://cn.vuejs.org/v2/guide/components.html#sync-](https://links.jianshu.com/go?to=https%3A%2F%2Fcn.vuejs.org%2Fv2%2Fguide%2Fcomponents.html%23sync-)修饰符\]

**.sync** 修饰符所提供的功能。当一个子组件改变了一个 prop 的值时，这个变化也会同步到父组件中所绑定  
就是说我们可以直接在我们需要传的prop后面加上 **.sync**  
比如 我下面需要绑定 p\_model,然后我在他后面加上.sync

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><certificate-input
              :p_model.sync='pname'>
   </certificate-input>
```

他会扩展成：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""> <certificate-input  :p_model="pname" @update:p_model="val => pname= val"></certificate-input>
```

父组件全部代码：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><template>
 <div>
   <certificate-input
              :p_model.sync='pname'
              :xi_model.sync="xiname">
   </certificate-input>
</div>
</template>
import CertificateInput from '../common/CertificateInput.vue'
export default {
    name: 'fathor',
    components: {
        CertificateInput 
    },
    data() {
      return {
            pname:"",
            xiname:""
    }     
}
```

## 子组件

上面说了一大推父组件下面我们来看看子组件怎么写 ？  
因为我项目中使用vux 代码就直接复制过来改了一下

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><template>
    <div>
      <x-input
            title="姓名"  
            v-model="name" 
            ></x-input>
        <x-input title="身份证号" 
                v-model="idCard" 
                placeholder="请输入身份证号"
                required>
        </x-input>
    </div>
</template>
<script type="text/javascript">
    import { XInput} from 'vux'
    export default{
        name:'certificateInput',
        props:["p_model","xi_model"],
        components:{
            XInput
        },
        data(){
            return{
                name:this.p_model,
                idCard:this.xi_model
            }
        },
        watch:{
      
            p_model(val) { 
                this.address = val;
            },
            name(val){
      //设置监听，如果改变就更新 p_model
                this.$emit('update:p_model', val)
            },
            xi_model(val){
                this.certificate = val
            },
            idCard(val){
                 this.$emit('update:xi_model', val)
            }
        }
    }
</script>
```

由上面可以看出 子组件主要代码 就是监听他的改变 然后触发父组件监听的事件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">   name(val){
      //设置监听，如果改变就更新 p_model
             this.$emit('update:p_model', val)
}
```

好了 上面就是我的方法  
感觉写的好low  
以后多多改善

后面的修改:

在上文中我试用了watch 去监听 其实这可以换种方式来操作  
// 如：watch中的

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">p_model(val) { 
          this.address = val;
            },
```

可以改成

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">computed: {
  _model:{
   get() {
    return this.p_model
   },
  set(val) {
    this.$emit("update:p_model",val)
  }
  }
}
```