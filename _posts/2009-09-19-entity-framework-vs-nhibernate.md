---
id: 114
title: 'Entity Framework 和NHibernate的区别'
date: '2009-09-19T17:19:52+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=114'
permalink: /2009/09/entity-framework-vs-nhibernate
categories:
    - 'net&amp;c#'
---

从个人感受上看，NHibernate显然是从上而下(Top-down)的方式，天然的POCO支持就是最好的佐证。而ADO.NET Entity Framework目前v1版本，应该是从下而上的方式Bottom-up)，ADO.NET Entity Data Model Wizard就是最好的佐证。Entity Framework v2支持POCO之后也就能实现垂直双方向的支持，但这如我后面所说，还不够。  
至于从里到外(Inside-out)，从外向里(Outside-in)是真正显示ORM工具或E/R模型编程模型威力的地方，因为99%的现实世界，以及数据库都不是同步也无法保持同步的，那么提供从里到外，从外向里两个方向的支持是非常重要的。 其实ORM和DP的存在就是要实现这两个方向的能力和支持，否则就没有存在的必要，因为自上而下(Top-down)和自下而上(Bottom-up)两种方式由来已久，解决这两个方向的问题，ORM工具或E/R模型编程未必是最好的方式，原因是这两种方向和思考比较不客观，那就是假设现实世界和数据有一方是不变的，或是我们自己可以控制的，事实上，这种想法是不现实的。

同样仔细想想Entity Framework(也许NHibernate也是)在从外向里(Outside-in)方向也提供了支持，这也就是Dsimmons为代表的EF大佬经常说的，Entity Framework 不是简单的ORM工具，它主要是提供一个完全贴近现实的E/R模型，这个模型可以进行数据编程(DP)，也可以建模和进行数据表现，最重要的是这个模型可以被共享和重用(目前比较成功的一个案例就是能被ADO.NET Data Service 共享和重用)，另外一个方面SQL Server的功能再逐渐增强以及新近的LINQ等数据库+DP的功能混杂和爆发，都在另外一个角度朝从下向上推进，这种情况下Mapping是可以限定在某些功能和场景的，Entity Framework 似乎也是朝这个方向发展的，事实上，这也是比较可能和有效的发展思路–从这样角度来看EF现在的ORM/Mapping方面的不足也就容易理解了。  
相反从里到外(Inside-out)的方式，我认为是比较困难的，先假设我们先拥有一种万能的Mapping/ORM技术或工具，然后我们再使用它来连接现实世界/OPP和数据存储/数据库之间的变化和gap，这个。。。。。呵呵，可能是我误解了。