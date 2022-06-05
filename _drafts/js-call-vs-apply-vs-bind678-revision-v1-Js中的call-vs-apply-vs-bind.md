---
id: 682
title: 'Js中的call vs apply vs bind'
date: '2019-08-19T18:22:30+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/678-revision-v1/'
permalink: '/?p=682'
---

**前言**

js中的call(), apply()和bind()是Function.prototype下的方法，都是用于改变函数运行时上下文，最终的返回值是你调用的方法的返回值，若该方法没有返回值，则返回undefined。

**apply()**

使用 apply， 你可以继承其他对象的方法：

注意这里apply()的第一个参数是null，在非严格模式下，第一个参数为null或者undefined时会自动替换为指向全局对象，apply()的第二个参数为数组或类数组。

**call()**

call()是apply()的一颗语法糖，作用和apply()一样，同样可实现继承，唯一的区别就在于call()接收的是参数列表，而apply()则接收参数数组。

<figure class="wp-block-image">![](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=697679812,1742532990&fm=173&app=49&f=JPEG?w=640&h=184&s=1096EC3287624D205C7CACD0000050B3)</figure>**bind()**

bind()的作用与call()和apply()一样，都是可以改变函数运行时上下文，区别是call()和apply()在调用函数之后会立即执行，而bind()方法调用并改变函数运行时上下文后，返回一个新的函数，供我们需要时再调用。

<figure class="wp-block-image">![](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=2114694925,2909890943&fm=173&app=49&f=JPEG?w=640&h=732&s=11307833110EE54D48F909DB0000D0B3)</figure>用apply()模拟实现bind()：

<figure class="wp-block-image">![](https://ss2.baidu.com/6ONYsjip0QIZ8tyhnq/it/u=2751873707,1910850157&fm=173&app=49&f=JPEG?w=640&h=722&s=10106C33190EC54D52FD01DA0000C0B3)</figure>在返回的新函数内部，self.apply(context, arguments)才是执行原来的getName函数，相当于执行getName.apply(person);

**如何选用**

如果不需要关心具体有多少参数被传入函数，选用apply()；如果确定函数可接收多少个参数，并且想一目了然表达形参和实参的对应关系，用call()；如果我们想要将来再调用方法，不需立即得到函数返回结果，则使用bind();

**总结**

call()、apply()和bind()都是用来改变函数执行时的上下文，可借助它们实现继承；call()和apply()唯一区别是参数不一样，call()是apply()的语法糖；bind()是返回一个新函数，供以后调用，而apply()和call()是立即调用。

**我是怎么记的**

apply是a开头跟array的a开头一致，所以apply的参数是数组的，其余两个call 和 bind的参数就不需要记忆了，都是多参数类型。