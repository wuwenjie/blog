---
id: 601
title: mpvue开发小程序过程中遇到的问题
date: '2019-08-18T18:19:17+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/600-revision-v1/'
permalink: '/?p=601'
---

## 前期准备

### 1.框架选型

原生小程序开发方式与vue有些类似，所以用过vue的前端er会很容易上手。但是原生的开发体验实在糟糕，在前端组件化的今天用原生开发组件显得很无力。对于习惯vue开发方式的前端er来说mpvue再合适不过了。mpvue可以将H5代码打包成小程序代码，目前mpvue还做不到一套代码多端运行（毕竟各个端有自己的差异性，小程序没有document和window，所以那些第三方移动端组件库并不能适用于小程序），但是已经大大减少了开发的工作量。

### 2.项目的搭建

<figure class="wp-block-image">![](https://user-gold-cdn.xitu.io/2018/10/12/166676de076f82a0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)</figure>### 3.项目结构

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">project 
└───build 
└───config 
└───dist 
└───node_modules 
└───server//mock服务器 
└───src 
    └───assets
        └───sass
        |    common.scss   // 全局样式
    └───components
    └───pages
    └───plugins //存放封装的插件
    └───services
    |    Api.js   // 封装请求
    |    WxApi.js   // 对小程序api二次封装    

    └───store
        └───modules // vuex模块文件夹
        |    index.js // vuex处理文件
        |    action.js
        |    state.js
        |    mutations.js 
        |    type.js复制代码
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">    |   App.vue
    |   config.js//配置信息，如请求地址等
    |   main.js
└───static    //静态资源
    └───images//图片
    └───font//字体图标
│   README.md
│   package.json  
│   package-lock.json  
复制代码
```

### 3.其他

小程序网络请求只能是https协议且不能有端口号

## 登录流程

### 1.关于OpenId和UnionId

`OpenId` 是一个用户对于一个小程序／公众号的标识，开发者可以通过这个标识识别出用户。

`UnionId` 是一个用户对于同主体微信小程序／公众号／APP的标识，开发者需要在微信开放平台下绑定相同账号的主体。开发者可通过`UnionId`，实现多个小程序、公众号、甚至APP 之间的数据互通了。

同一个用户的这两个 ID 对于同一个小程序来说是永久不变的，就算用户删了小程序，下次用户进入小程序，开发者依旧可以通过后台的记录标识出来。

### 2.官方给出的最佳实践

新版的小程序对获取用户信息授权进行了改动，用户在小程序中需要点击组件后，才可以触发登录授权弹窗、授权自己的昵称头像等数据。因此官方给出的最佳实践是

> 1.调用 `wx.login` 获取 `code`，然后从微信后端换取到 `session_key`，用于解密 `getUserInfo`返回的敏感数据。  
> 2.使用 `wx.getSetting` 获取用户的授权情况  
>  1) 如果用户已经授权，直接调用 API `wx.getUserInfo` 获取用户最新的信息；  
>  2) 用户未授权，在界面中显示一个按钮提示用户登入，当用户点击并授权后就获取到用户的最新信息。  
> 3.获取到用户数据后可以进行展示或者发送给自己的后端。

<figure class="wp-block-image">![](https://user-gold-cdn.xitu.io/2018/10/12/1666792d0d31bda3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)</figure>### 3.实现

1. 制作一个登录页面
2. 在App.vue里的onLaunch生命周期中判断Storage中是否存在，如不存在跳转到登录页并把当前页面的路由当作参数传递过去，如存在再调用wx.checkSession()检查session\_key 的有效性否则跳到登录页并把当前页面的路由当作参数传递过去
3. 在ajax请求response拦截器里判断状态码为401表示token已过期，跳转到登录页重新登陆并把当前页面的路由当作参数传递过去
4. 登录按钮加上open-type=”getUserInfo”属性，并监听getuserinfo事件，用户点击后会返回加密后的用户信息，此时执行wx.login()获取到code,将code和用户信息发送到后台换取token，并把token存储到Storage

附官方的最佳实践[mp.weixin.qq.com/s/JBdC-G9Mw…](https://link.juejin.im?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FJBdC-G9MwaptFjQeD9ujeA)

## vuex模块化

在开发时有时会遇到一些变量需要跨两三个页面传递公用，所以引入vuex是比较好的解决方案。当一个项目比较大时，所有的状态都集中在一起会得到一个比较大的对象，进而显得臃肿，难以维护。为了解决这个问题，Vuex允许我们将store分割成模块（module），每个module有自己的state，mutation，action，getter。新建以下目录

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">     store
        └───modules // vuex模块文件夹
               └───  kx //一级模块
                 └───  jkdbb//二级模块
                    | index.js  
                    ...
        |    index.js // vuex处理文件
        |    action.js
        |    state.js
        |    mutations.js 
        |    type.js复制代码
```

action，state，mutations，type作为公共。

在index.js中设置namespaced为true

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">export default {
    namespaced: true,
    state: {
        ywmkid: '1',
        formData:{}
    },
    mutations:{
        SET_YWMKID (state,ywmkid) {
            state.ywmkid = ywmkid
        },
        SET_FORMDATA (state,formData) {
            state.formData = formData
        }
    }
    
}复制代码
```

在store下的index.js

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import Vue from 'vue'
import Vuex from 'vuex'
import state from './state'
import mutations from './mutations'
import actions from './actions'
import jkdbb from './module/kx/jkdbb/index'

Vue.use(Vuex)

const isDebug = process.env.NODE_ENV !== 'production'

export default new Vuex.Store({
    state,
    mutations,
    actions,
    modules:{
        jkdbb
    },
    strict: isDebug
})
复制代码
```

访问模块里的state

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">this.$store.state.jkdbb.ywmkid复制代码
```

修改模块里的state

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">this.$store.commit('jkdbb/SET_YWMKID',this.$route.query.id)复制代码
```

## mpvue-router-patch

vue-router不兼容小程序，所以为了和h5代码保持一致选用mpvue-router-patch，其和vue-router类似的使用方式，默认对应小程序的navigateTo。附小程序的三种跳转方式的区别

> wx.navigateTo：跳转到某个页面。会产生一个新的页面  
> wx.redirectTo：重定向到某个页面。替换掉当前页面  
> wx.switchTab：切换tab  
> wx.reLaunch:重新启动。清空之前的所有页面  
> 在小程序中有层级的限制，只能打开十个页面，因此要合理利用跳转的方式。

在需要重启应用时只需要设置reLaunch为true即可

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">this.$router.push({path:'/pages/index/main',reLaunch: true})
复制代码
```

在需要切换tab时只需要设置switchTab为true即可

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">this.$router.push({path:'/pages/index/main',switchTab: true})
复制代码
```

附完整api地址[github.com/F-loat/mpvu…](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FF-loat%2Fmpvue-router-patch)

## flyio

axios不兼容小程序，flyio兼容[Node.js](https://link.juejin.im?target=https%3A%2F%2Fnodejs.org%2F) 、[微信小程序](https://link.juejin.im?target=https%3A%2F%2Fmp.weixin.qq.com%2Fcgi-bin%2Fwx) 、[Weex](https://link.juejin.im?target=http%3A%2F%2Fweex.apache.org%2F) 、[React Native](https://link.juejin.im?target=http%3A%2F%2Ffacebook.github.io%2Freact-native%2F) 、[Quick App](https://link.juejin.im?target=https%3A%2F%2Fwww.quickapp.cn%2F) 和浏览器。以下的代码是对flyio的使用进行封装

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import Config from '@/config'
import {CacheUtil} from '@/services/WxApi' 
let Fly=require("flyio/dist/npm/wx") 
let fly=new Fly;

class Response {

    constructor (res) {
        this.rawData = res
        this.code = res.code
        this.messages = res.messages
        this.data = res.data
        this.wwRequestEntity = res.wwRequestEntity
    }

    resolve () {
        if (this.isSuccess()) {
            return Promise.resolve(this.rawData)
        }
        if (this.isError()) {
            let message = Config.defErrorMessage
        }
        return Promise.reject(this.messages)
    }

    isSuccess () {
        return this.code === 1
    }

    isError () {
        return this.code === 0
    }
}


class ApiManager {

  constructor (apiPrefix) {
      fly.config.baseURL = apiPrefix || Config.apiPrefix;
      fly.config.timeout = 120000
      fly.interceptors.request.use(request => {
          const token = CacheUtil.getStorage('token');
          if (token) {  // 判断是否存在token，如果存在的话，则每个http header都加上token
              request.headers.Authorization = `Bearer ${token}`;
          }
          return request
      })


      fly.interceptors.response.use(res => {
          if (res.status >= 200 && res.status < 300) {
              let response = new Response(res.data)
              return response.resolve()
          }
          return Promise.reject(res)
      }, error => {
          const { response } = error;
          if (!response) return Promise.reject(error)
          if (response.status === 401) {
              //没有权限跳转到授权页面
              return Promise.reject(null)
          }
          return Promise.reject(error)
      })
  }
  post(uri, data, config){
    return fly.post(uri, data, config);
  }

  get(uri, data){
    return fly.get(uri, data);
  }

  put (uri, data) {
      return fly.put(uri, data)
  }

  delete (uri, data) {
      return fly.delete(uri, data)
  }
}

export function httpManager(baseURL){
    return new ApiManager(baseURL)
}

export let apiManager = httpManager()复制代码
```

在main.js引用并挂载

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">Vue.prototype.apiManager = apiManager;
复制代码
```

之后在页面中就可以这样使用

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">this.apiManager.post复制代码
```

## 二次封装Storage

小程序的Storage没有设置过期时间的参数，我们可以对他进行二次封装

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">export const CacheUtil = {
  getStorage(key){
    let data = wx.getStorageSync(key);
    if (!data) {
      return null;
    }else{
      let now = new Date().getTime()
      if (now >= parseInt(data.expired)) {
        wx.removeStorageSync(key)
        return null;
      }else{
        return data.data;
      }
    }
    return 
  },
  setStorage(key,data,expired){
    let time = new Date().getTime() + expired;
    wx.setStorageSync(key,{data:data,expired:time})
  },
  removeStorage(key){
    wx.removeStorageSync(key)
  }
}
复制代码
```

## 表单验证

小程序的表单验证似乎不多，常用的就是[WxValidate](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fskyvow%2Fwx-extend%2Fblob%2Fmaster%2Fdocs%2Fcomponents%2Fvalidate.md)。但是我觉得不是那么好用，提示判断还要自己写。所以采用[we-validator](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FChanceYu%2Fwe-validator),它支持微信小程序、支付宝小程序、浏览器以及Nodejs端使用。我们可以在plugins文件夹中新建一个js用于添加自定义的校验规则

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import WeValidator from 'we-validator'
WeValidator.addRule('Mobile', function(value, param){
     const reg = 11 && /^(((13[0-9])|(14[5,7])|(15[0-3,5-9])|(17[0,3,5-8])|(18[0-9])|166|198|199|(147))\d{8})$/;
    return reg.test(value) 
})

export default{
    WeValidator
}
复制代码
```

然后再main.js中引用并挂载

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">Vue.prototype.$WeValidator = WeValidator.WeValidator;
复制代码
```

在页面中

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">    mounted(){
      this.oValidator = new this.$WeValidator({
        rules: {
          sbmc: {
            required: true
          },
          sbxh: {
            required: true
          },
          fx: {
            required: true
          },
          yxqk: {
            required: true
          },
          azrq: {
            required: true
          },
          ssqx: {
            required: true
          },
          azdz: {
            required: true
          }
        },
        messages: {
          sbmc: {
            required: '请选择设备名称'
          },
          fx: {
            required: '请选择方向'
          },
          sbmc: {
            required: '请选择设备名称'
          },
          yxqk: {
            required: '请选择运行情况'
          },
          azrq: {
            required: '请选择安装日期'
          },
          ssqx: {
            required: '请选择所属区县'
          },
          azdz: {
            required: '请输入安装地址'
          }
        }
      })
    },
    methods:{
     submit(){ 
        if(this.oValidator.checkData(this.form)){
          
        } 
      }
    }复制代码
```

## 获取当前位置

小程序的api获取当前位置返回的信息比较少，如有需要获取到地址需要引入地图的地址解析。我用的是腾讯地图，在引用的时候会报错，是因为它不是用export导出的，所以可以在最后修改为export default QQMapWX。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import QQMapWX from '../plugins/qqmap-wx-jssdk.js'
let qqmapsdk = new QQMapWX({
                  key: config.txMapKey
              });
export function getPosition(){
  return new Promise((resolve,reject)=>{
    wx.showLoading({
      title:'正在定位',
      mask:true
    })
    wx.getLocation({
      success:function(res){
        qqmapsdk.reverseGeocoder({
            coord_type:0,
            location: {
                latitude: res.latitude,
                longitude: res.longitude
            },
            success: function(data) {
                resolve({
                  latitude: res.latitude,
                  longitude: res.longitude,
                  adcode: data.result.ad_info.adcode,
                  addr:data.result.address
                })
            },
            fail: function(err) {
                reject(err);
            },
            complete: function(err) {
              wx.hideLoading()
            }
        });
      },
      fail:function(err){
        reject(err)
      }
    })
  }) 
}复制代码
```

## 封装input组件

我们可以将input做成一个组件，可以去配置输入框的标题，清空输入框等功能。isShowArea如下一条所说的作用。在向父页面传递值时需要一个settimeout，如果不添加会有闪烁的bug。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><template>
  <div class="yg-input">
    <label :placeholder="placeholder"  :style="{width:labelWidth}">{{label}}</label>
    <div class="yg-input-control yg-textarea-control" v-if="type=='textarea'">
      <textarea v-if="isShowArea" :maxlength="maxlength" v-bind="$attrs" :cursor-spacing="100" :placeholder="placeholder" :disabled="disabled||readonly" :value="currentVal" @input="updateVal($event.target.value)"></textarea>
      <div v-if="!isShowArea">{{currentVal}}</div>
    </div>
    <div class="yg-input-control" v-else>
      <input :placeholder="placeholder" :maxlength="maxlength" :cursor-spacing="100" :disabled="disabled||readonly" :value="currentVal" v-bind="$attrs" @input="updateVal($event.target.value)" :type="type" />
      <i v-if="value && $attrs.readonly!==!!'true'" @click="clear" class="iconfont iconfont-close"></i> 
    </div>
  </div>
</template>
<script>
  export default {
    inheritAttrs: false,
    data() {
      return {
        currentVal:this.value
      }
    },
    computed:{
      isShowArea(){
        return this.$store.state.mask;
      }
    },
    methods:{
      clear(){
        this.currentVal = '';
        this.$emit('input', '');
      },
      updateVal(val) {
        this.currentVal = val;
        setTimeout(()=>{
          this.$emit('input', val);
        },0)  
      }
    },
    watch:{
      value: {
　　　　handler(newValue, oldValue){
          this.currentVal = newValue;   
　　　　}
　　　}
    },
    props: {
      label:{
        default:'',
        type:String
      },
      labelWidth:{
        default:'75px',
        type:String
      },
      type:{
        default:'text',
        type:String
      },
      value:{
        default:'',
        type:String
      },
      placeholder:{
        default:'',
        type:String
      },
      maxlength:{
        default:'-1',
        type:String
      },
      disabled:{
        default:false,
        type:Boolean
      },
      readonly:{
        default:false,
        type:Boolean
      }
    }
  }
</script>
<style lang="scss">
  
</style>
复制代码
```

可以将它定义为全局组件，在main.js中

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">Vue.component('yg-input',YgInput)
复制代码
```

在父页面使用

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><yg-input :readonly="status==3" v-model="form.sbxh" label="设备型号" placeholder="请输入设备型号"></yg-input>
复制代码
```

## textarea的bug

textarea是原生组件因此层级最高，因此在页面上自定义的弹窗会被textarea的内容覆盖。因此我们可以在弹窗是将它隐藏，在关闭弹窗时显示。可以在vuex中定义一个变量控制，然后在弹窗组件中对这个变量赋值。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><textarea v-if="isShowArea"></textarea>
<div v-if="!isShowArea">{{currentVal}}</div>//在textarea隐藏时显示textarea填写的内容复制代码
```

还有一个问题是textarea在ios真机上会有一个默认的padding导致和其他表单元素不对齐。这个官方还是没修复。

## 封装select组件

原生的picker不能自定义样式，而却选项的文字多了会显示…，项目中还要显示中英文所以自己写了一个

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><template>
  <div class="yg-input yg-select">
    <label :style="{width:labelWidth}">{{label}}</label>
    <div class="yg-input-control" @click="showPicker">
      <input :value="selectVal" :placeholder="placeholder" v-bind="$attrs" :disabled="true"/>
      <i class="iconfont iconfont-arrow-r"></i>
    </div>
    <div class="popup-mark" v-if="toggle">
      <ul class="yg-popup">
        <li class="yg-popup-head">
          <span @click.stop="cancel">取消</span>
          <span @click.stop="confirm">确定</span>
        </li>
        <li v-for="(item,$index) in selectOption" :class="{'active':item.active}" :key="index" @click.stop="itemClick(item)">{{item.text}}</li>
      </ul>
    </div>
  </div>
</template>
<script>
  export default {
    inheritAttrs: false,
    data() {
      return {
        selectVal:'',
        toggle:false,
        cache_selectVal:'',
        cache_selectTxt:'',
        isFirstEnter:true,
        selectOption:[]
      }
    },
    methods:{
      cancel(){
        this.selectOption = [...this.setActive(this.value,this.option)];
        this.hide();
      },
      confirm(){
        if(this.selectVal != this.cache_selectTxt&&this.cache_selectTxt){
          this.selectVal = this.cache_selectTxt;
          this.$emit('input', this.cache_selectVal);
          this.$emit('change', this.cache_selectVal,this.cache_selectTxt);
        }
        this.hide();
      },
      itemClick(item){
        this.cache_selectVal = item.value;
        this.cache_selectTxt = item.text;
        this.selectOption = [...this.setActive(item.value,this.option)];
      },
      setActive(value,arr){
        for(let item of arr){
          if(value==item.value){
            item.active=true;
          }else{
            item.active=false;
          }
        }
        return arr;
      },
      hide(){
        this.$store.commit('SET_MARK',true);
        this.toggle = false;
      },
      showPicker(){
        if(this.disabled||this.readonly){
          return false;
        }

        if(!this.option.length){
          this.$message('没有相关选项');
          return false;
        }
        this.$store.commit('SET_MARK',false);
        this.toggle = true;
      }
    },
    props: {
      label:{
        default:'',
        type:String
      },
      disabled:{
        default:false,
        type:Boolean
      },
      readonly:{
        default:false,
        type:Boolean
      },
      labelWidth:{
        default:'75px',
        type:String
      },
      value:{
        default:'',
        type:String
      },
      placeholder:{
        default:'',
        type:String
      },
      option:{//
        default:function(){
          return []
        },
        type:Array
      },
      defaultFirst:{
        default:false,
        type:Boolean
      }
    },
    watch:{
      value: {
　　　　handler(newValue, oldValue){
          this.selectOption = this.selectOption.map((item)=>{
            if(this.value==item.value){
              item.active=true;
              this.selectVal=item.text;
            }else{
              item.active=false;
            }
            return item;
          })
　　　　},
        immediate: true
  　　},
      option: {
　　　　handler(newValue, oldValue){
          this.selectOption = [...newValue];

          let _value = '';
          for(let item of newValue){
            if(this.value==item.value){
              _value = item.value;
              break;
            }
          }

          if(_value){
            this.$emit('input', _value);
          }

          if(this.option.length!==0&&!_value&&this.defaultFirst){
            this.$emit('input', this.option[0].value);
          }
          
          if(this.option.length==0&&!_value&&!this.isFirstEnter){
            this.$emit('input', '');
            this.isFirstEnter = false;
          }          
　　　　},
　　　　deep: true,
        immediate: true
  　　}
    }
  }
</script>
<style lang="scss">
  @keyframes popup{
    0%{
      transform:translate(0,100%);
    }
    100%{
      transform:translate(0,0)
    }
  }
  .popup-mark{
    position:fixed;
    z-index:9999;
    background:rgba(0,0,0,0.4);
    top:0;
    left:0;
    right:0;
    bottom:0;
    display:flex;
    align-items:flex-end;
    .yg-popup{
      animation:popup .3s;
      flex:1;
      max-height:800px;
      min-height:500px;
      overflow:auto;
      background:#fff;
      li{
        &.yg-popup-head{
          background:#fbf9fe;
          color:#999;
          line-height: 1;
          padding:0;
          overflow:hidden;
          padding:30px;
          span:nth-of-type(1){
            float: left;
          }
          span:nth-of-type(2){
            float: right;
            color:#2b79fb;
          }
        }
        background:#fff;
        border-bottom:1px solid #eee;
        line-height: 1;
        padding:30px 50px 30px 30px;
        font-size:32px;/*no*/
        color:#666;
        position:relative;
        &.active{
          color:#2b79fb;
          &:before{
            content:'';
            position:absolute;
            width:8PX;
            height:8PX;
            border-radius:50%;
            top:50%;
            background:#2b79fb;
            margin-top: -4PX;
            right: 40px;
          }
        }
      }
    }
  }
</style>复制代码
```

## 下拉刷新上拉加载

### 1.上拉加载更多

在列表的下面添加以下节点，loadTip默认为空，用于加载时的提示

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><div class="page-list-loading">{{loadTip}}</div>
复制代码
```

定义maxPage表示最后一页

在onReachBottom钩子里加载下一页

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">onReachBottom(){
      this.loadTip = '正在加载中';
      this.form.page++;
      this.loadData();
    },
复制代码
```

在loadData的回调里

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">this.list = this.list.concat(res.data);
this.loadTip = '';
this.maxPage = Math.ceil(res.total/this.form.size);//算出最后一页
复制代码
```

### 2.下拉刷新

在需要下拉刷新的页面同级新建main.json

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
  "enablePullDownRefresh": true
}
复制代码
```

在onPullDownRefresh钩子里加载数据

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">this.maxPage = null;
this.form.page = 1;
this.list = [];
this.loadTip = '';
wx.showNavigationBarLoading();//显示标题栏的加载动画

//请求成功后
wx.hideNavigationBarLoading();
wx.stopPullDownRefresh();//停止下拉复制代码
```

截至2018-10-15，下拉刷新还存在bug，ios上头部head设置position为fixed时会挡住加载动画，在安卓上头部head又会跟随下滑

## 粘性导航

在h5中可以方便地利用window对象获取滚动距离，元素距离顶部距离等。但是在小程序中并没有window对象只能使用小程序的api获取相关数据。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><template>
  <div class="Home">
      <div class="com-part-class">
        //导航
        <div class="affix">
          <div :class="{'fixed':isfixed,'nav-list-wrap':true}">
           <div class="nav-list">
              <div v-for="(item,index) in affixArr" @click="changeSt(index)" :class="{'active':navActive==index,'nav-item':true}" :key="index">
               {{item}}
             </div>
           </div>
         </div>
        //内容
         <div class="affix-pane-wrap">
           <div v-for="(item, index) in indexDataLists" :key="index" :class="item.style">
              <div class="com-bar-ti">
                {{item.label}}
              </div>
              <app-list @jump="toPage" :parent-index="index" @loadMore="loadMore" :more="true" :data="item.data"></app-list>
            </div>
         </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
function getNavActiveIndex(num,arr){//返回第几个高亮
  if(num===0){
    return 0;
  }
  for(var i=0;i<arr.length;i++){
    if(num>=arr[i]){
      if (!arr[i + 1]) {
        return arr.length - 1;
      } else if (num < arr[i + 1]) {
        return i;
      }
    }else if (num < arr[i]) {
      if (!arr[i - 1]) {
        return 0;
      } else if (num >= arr[arr - 1]) {
        return i-1;
      }
    }
  }
}
import AppList from './AppList.vue'
export default {
  data () {
    return {
      offsetTopArr:[],
      indexDataLists:[],
      affixArr:[],
      navActive:0,
      isfixed:false
    }
  },
  components: {
    AppList
  },
  methods: {
    changeSt(index){//点击导航跳转对应内容区
      this.navActive = index;
      wx.pageScrollTo({
        scrollTop: this.offsetTopArr[index]
      })
    }
  },
  onPageScroll(e){
    const query = wx.createSelectorQuery();
    query.select('.affix').boundingClientRect((res)=>{//判断相对于显示区域的距离是否小于0即页面滚动到导航的位置
            if(res){
              if(res.top<0){
                this.isfixed = true;
              }else{
                this.isfixed = false;
                this.navActive = 0;
              }
            }
    }).exec();
    this.navActive = getNavActiveIndex(e.scrollTop,this.offsetTopArr);
  },
  mounted () {
    this.$showLoading();
    Promise.all([this.apiManager.post('/home/indexLists'),this.apiManager.post('/home/frequentlyUsed'),this.apiManager.post('/gaywtz/wrapNotice')]).then(res=>{
      this.affixArr = res[0].data[0].indexLabelLists
      this.indexDataLists=res[0].data[0].indexDataLists;
      setTimeout(()=>{
        const query = wx.createSelectorQuery();
        query.selectAll('.affix-pane').boundingClientRect((res)=>{
            res.map((item)=>{
              this.offsetTopArr.push(item.top-45);//45是导航的高度，用px布局
            })
        }).exec();
        this.$hideLoading();
      }, 200)
    })
  }
}
</script>


复制代码
```

## px2rpx

rpx 是微信小程序解决自适应屏幕尺寸的尺寸单位。以iPhone6为例，屏幕宽度为375px，把它分为750rpx后， 1rpx = 0.5px。px2rpx可以很方便地把px转成rpx。文档中说和px2rem使用一样，但是当某个属性并不需要将px转成rpx时，设置/\*no\*/并没有用，解决办法是把px改为PX。

## mpvue-wxparse

后台返回的富文本编辑器中的内容是html节点，这在小程序中是解析不出来的。因此需要[mpvue-wxparse](https://link.juejin.im?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fmpvue-wxparse)去转换

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">js
import wxParse from 'mpvue-wxparse'
components:{
  wxParse
}

html
<wxParse :content="content" @preview="preview" @navigate="navigate" />
复制代码
```

## 所有页面的created钩子函数在小程序打开时就全部执行

改用mouted钩子

## 重新进入页面会保留之前的数据

假设有三个页面：列表页A,表单页一B,表单页二C。当填写完表单页C后跳转到列表页，这时如果再从列表页进入表单页B会发现B还是之前填写的数据。这是因为mpvue在离开页面时并没有调用destroyed钩子，因此目前的解决方案是在小程序的onUnload中重置data函数里的数据。可以将它封装成一个插件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const resetPageData = {
  install: function (Vue) {
    Vue.mixin({
      onUnload() {
        if (this.$options.data) {
            Object.assign(this.$data, this.$options.data())
        }
      }
    })
  }
}

export default resetPageData;
复制代码
```

然后在main.js中使用

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">Vue.use(resetPageData);
复制代码
```

## 其他需要注意的

1.background不支持本地路径

2.不能使用v-show需替换成v-if

3.在map中使用cover-view需要直接使用cover-view，如使用div会有问题，文档中写到目前cover-view支持动画，开发者工具中有效实际在真机无效，且不支持单边border,rotate等

4.solt不支持动态渲染，在封装业务组件时很是蛋疼

5.不支持自定义指令