---
id: 686
title: 'JS slice() vs splice()'
date: '2019-08-19T18:38:46+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/683-revision-v1/'
permalink: '/?p=686'
---

 <span style="font-size: 14pt;"> **slice()**</span>

<span style="font-size: 14pt;"> </span><span style="font-size: 16px;">**slice()定义：**从已有的数组中返回你选择的某段数组元素</span>

<span style="font-size: 16px;"> **slice()语法：**arrayObject.slice(start,end)</span>

 <span style="color: #ff0000;">**<span style="font-size: 16px;">注：</span>**</span>

<span style="font-size: 16px;"> <span style="color: #000000;">**①：**</span>start表示从何处开始选取，end表示从何处开始结束选取，表示一个选取的范围</span>

<span style="font-size: 16px;"> **②：**start可以为负数，此时它规定从数组尾部开始算起的位置。也就是-1 ，指最后一个元素，-2 指倒数第二个元素，以此类推</span>

<span style="font-size: 16px;"> **③：**end如果没有被指定参数，数组会包含从 start 到最后一个数组元素的所有元素</span>

<span style="font-size: 16px;"> ④：slice()方法不会修改数组本身，而是返回所选取范围的数组元素。如果想删除数组中的某一个元素，需要使用splice()</span>

 **<span style="font-size: 18px;">下面来看一些例子</span>**

 <span style="font-size: 16px;"> 这里的取值范围是0-2（start-end），因为数组的下标是从0开始，所以这里的2就是下面数组中的 javascript元素</span>

<span style="font-size: 16px;"> 这里需要注意的是0-2选取的元素是html和css，并不包括javascript</span>

 ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218165145423-1506403443.png)

 <span style="font-size: 16px;"> 如果只有start，则会选择start开始到end之间的所有数组元素</span>

 ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218165256787-800771029.png)

 <span style="font-size: 16px;"> 如果start是负数，则会从数组尾部开始算起。这里只有start没有end,且start为负数，所以只能获取到最后1个数组元素</span>

<div> ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218165554947-1763098656.png)</div> <span style="font-size: 16px;"> 如果你想获取除了最后1个元素以外的全部元素</span>

 ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218165657697-1062811430.png)

 <span style="font-size: 16px;">如果start和end都是负1，结果为空</span>

 ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218165722943-570052154.png)

 <span style="font-size: 14pt;"> **splice()**</span>

<span style="font-size: 14pt;"> **<span style="font-size: 16px;"> </span>**</span><span style="font-size: 16px;">**splice()定义：**从数组中添加或删除元素，然后返回被删除的数组元素。</span>

<span style="font-size: 16px;"> **splice()语法：**arrayObject.splice(index,howmany,item1,…..,itemX)</span>

<span style="font-size: 16px;"> <span style="color: #ff0000;"> **注：**</span></span>

<span style="font-size: 16px;"> <span style="color: #000000;">**①：**</span>index表示从什么位置开始添加或删除数组元素  
</span>

<span style="font-size: 16px;"> **②：**howmany表示删除的元素数量，如果为0，则表示不删除数组元素</span>

<span style="font-size: 16px;"> **③：**tem1,…..,itemX表示新增的数组元素</span>

<span style="font-size: 16px;"> **④：**slice()方法会改变原始数组</span>

<span style="font-size: 16px;"> **<span style="font-size: 18px;">下面来看一些例子</span>**</span>

<span style="font-size: 16px;">**<span style="font-size: 18px;"> </span>**<span style="font-size: 18px;"><span style="font-size: 16px;"> 从第3个元素（即 javascript）开始且不删除元素，并在第3个元素前面新增1个元素hello</span></span></span>

<span style="font-size: 16px;"><span style="font-size: 18px;"><span style="font-size: 16px;"> ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218173418110-609338222.png)</span></span></span>

 <span style="font-size: 16px;"> 从第3个元素开始且</span><span style="font-size: 16px;">删除1个元素**（这里删除的元素是 javascript）**，并在原来第3个元素的位置新增1个元素hello</span>

 ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218173611650-1975531769.png)

<span style="font-size: 16px;">**<span style="font-size: 18px;"> </span>**<span style="font-size: 18px;"><span style="font-size: 16px;">从最后1个元素开始并删除最后1个元素，同时在删除的最后1个元素的位置新增1个元素hello</span></span></span>

<span style="font-size: 16px;"><span style="font-size: 18px;"><span style="font-size: 16px;"> ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218173633208-1243764109.png)</span></span></span>

 <span style="font-size: 16px;">从最后1个元素开始且不删除元素，同时在最后1个元素前面新增1个元素hello</span>

<span style="font-size: 16px;"> ![](https://img2018.cnblogs.com/blog/1033257/201902/1033257-20190218173720877-626482378.png)</span>

## 总结

splice()会改变原来的数组，返回的是被改变的内容，比如说通过splice删掉了某一项，那么返回的是删掉的这一项，当然还是会以数组的形式返回。

slice不会对原数组进行改变，会返回一个新的数组。利用slice同样也可以实现根据索引删除某一项

## 我是怎么记忆的

splice的前缀是sp，sp代表着super是超级的意思，说明它有超级权限，就是说会改变原有数组的权限，会对原有数组进行删除增加的权限。sp的p念起来就是屁，屁被人体减去的东西，因此splice返回值的就是 被删除的东西。