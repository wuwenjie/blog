---
id: 563
title: '注意IE下Ajax Get缓存的问题'
date: '2017-03-20T11:05:43+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=563'
permalink: /2017/03/ajax-get-cache-problem-in-ie
categories:
    - Javascript
---

在前端项目开发中，使用get请求时，一定要考虑到 IE浏览器的缓存问题。。。

## 起源：

 ​ 常见的是ajax请求过一次以后，以后的相同url的get请求会存在下面这种情况：

- 第一种情况：有时返回304，有时返回200;
- 第二种情况：有时无论后台数据是否变化始终返回304，有时却始终返回200；同一套代码在不同浏览器间结果不同。

## 禁止浏览器缓存常用的方法：

#### 1. 在get请求的URL 参数后面加时间戳或者随机数

$.ajax({  
 url: ‘http://localhost/api/list’,  
 type: ‘get’,  
 data: {  
 \_t: new Date().valueOf() //加时间戳  
 //\_t: Math.random() 加随机数  
 },  
 success: function ( res ) {  
 console.log(res);  
 }  
 })  
 结果：这种方式虽然能解决IE始终返回304的问题，但实际上每个ajax都会去请求服务器，对web优化并非最佳的解决方案。

#### 2. 用post请求替代get请求

结果： 有其他方式解决现场尝试其他方式解决，一是因为这种做法不符合RESTful API设计，二是因为这种方式同样会每次请求服务器，可能会没有利用到浏览器自带的缓存功能，但是可以解决这个问题。