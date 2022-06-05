---
id: 650
title: GraphQL的了解
date: '2019-03-12T23:16:22+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=650'
permalink: /2019/03/about-graphql
categories:
    - Javascript
---

## 什么是GraphQL？

GraphQL是Facebook开源的API查询语言，类似于数据库中的SQL。作为比较，RESTful API依赖于后端隐式的被动的数据约定，GraphQL更加显式，在获取数据和更新数据时更加主动，所见即所得。[GraphQL官方网址](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Ffacebook%2Fgraphql)

## RESTful的一些不足

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">1. 扩展性，单个RESTful接口返回数据越来越臃肿

比如获取用户信息/users/:id，最初可能只有id、昵称，但随着需求的变化，用户所包含的字段可能会越来越多，年龄、性别、头像、经验、等级，等等。

而具体到某个前端页面，可能只需要其中一小部分数据，这样就会增加网络传输量，前端获取了大量不必要的数据。

2. 某个前端展现，实际需要调用多个独立的RESTful API才能获取到足够的数据

比如一个文章详情页，最初可能只需要文章内容，那么前端就调用/articles/:aid获取到文章内容来展现就行了

但随着需求的演进，产品可能会希望加上作者信息（昵称、头像等），这时前端又需要在获取文章详情后，根据其中的作者id字段继续获取作者相关的信息，/user/:uid

然后，需求又变化了，产品希望在加上这篇文章的评论，这时前端需要继续调用/comment/:aid来拉取评论列表

对于Web前端而言，由于ajax技术的存在，这种的请求数据方式，也就开发上稍微麻烦些，并不会造成太大的问题；但对于App来说，渲染的方式不同，必须要拉取的全部的数据之后，才能绘制界面，就会导致这个界面必须要等到所有3个RESTful接口的返回数据都拿到，才能进行绘制。
```

## GraphQL优点

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">1. 所见即所得

查询的返回结果就是输入的查询结构的精确映射

2. 减少网络请求次数

如果设计的数据结构是从属的，直接就能在查询语句中指定;即使数据结构是独立的，也可以在查询语句中指定上下文，只需要一次网络请求，就能获得资源和子资源的数据。

3. 代码即文档

GraphQL会把schema定义和相关的注释生成可视化的文档，从而使得代码的变更，直接就反映到最新的文档上，避免RESTful中手工维护可能会造成代码、文档不一致的问题。

4. 参数类型强校验

RESTful方案本身没有对参数的类型做规定，往往都需要自行实现参数的校验机制，以确保安全。

但GraphQL提供了强类型的schema机制，从而天然确保了参数类型的合法性。
```

## GraphQL适用场景

从Facebook最初开发GraphQL的目的，和笔者实际使用的情况而言，GraphQL还是存在一些缺点的，完全替代RESTful作为一种新的接口规范还有些为时过早。

GraphQL作为RESTful的一种辅助工具，尤其是针对前端App在复杂页面，本来要调用有上下文关系的多次RESTful请求时，采用GraphQL，只需要一次请求，就可以拿回所需的全部数据（有点JSON直出的意思），还是可以起到非常好的效果，大大提升App的性能。