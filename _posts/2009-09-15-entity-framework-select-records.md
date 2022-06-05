---
id: 110
title: 'Entity Framework（EF）数据查询'
date: '2009-09-15T21:59:00+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=110'
permalink: /2009/09/entity-framework-select-records
categories:
    - 'net&amp;c#'
tags:
    - EF
    - sql
---

Entity Framework（EF）存取Entity的三种方式。

| LINQ to Entities | 直接通过LINQ存取，可完全将程序与数据库分离，由LINQ在内部自动使用Object Service进行数据库操作 |
|---|---|
| Object Service | 可以透过Entity SQL(eSQL)来存取Entity，并且直接以对象的方式来存取结果集（因为结果集本身就是对象的集合）。 |
| EntityClient | 通过过类似ADO.NET 的方法，以及 Entity SQL 存取 Entity。 |

EF，有一个容器管理着里面所有附着在其上的对象。它们通过一种叫Object Tracking的机制来跟踪对象的变化，以便于在用户需要的时候把这些变化持久化到数据库中去。有时候，我们可能并不需要改动数据（比如我们只是简单地取出一个Entity然后把它绑定到UI上面去），那么在这个时候，Tracking机制就比较多余了。在EF中，我们可以以MergeOption.NoTracking=false来取得同样的效果。

在EF中，有个Query Plan Caching的功能，它可以Cache编译后的ESQL。如果使用Objective Service，可以用[System.Data.EntityClient.EntityCommand.EnablePlanCaching](http://msdn.microsoft.com/en-us/library/system.data.entityclient.entitycommand.enableplancaching.aspx)将它设置为打开。如果使用EntityClient，可以用[System.Data.Objects.ObjectQuery.EnablePlanCaching](http://msdn.microsoft.com/en-us/library/bb738709.aspx)将它设置为打开。默认情况下，这两个设置都是为True的，不需要我们过多操心。不过要注意的是只有要执行的语句与已缓存的语句完全精确匹配的时候才能使用缓存（但是查询参数可变，其实这个原理跟SQL Server的执行计划缓存原理差不多）。另外，缓存的ESQL是基于App-Domain的,而且即使是ObjectQuery&lt;T&gt;的实例被销毁了，其余的ObjectQuery&lt;T&gt;实例照样可以使用缓存计划。

最后一个是CompiledQuery会在第一次运行时进行编译，所以在第一次运行时，它比正常的LINQ语句还要慢。CompiledQuery的一般用法是声明一个static的变量来存储它。

还有就是第一次创建ObjectContext并查询数据时耗费了大量的时间。下面这个饼状图给出了第一次创建ObjectContext并用其访问数据库时各种操作所占的时间比

<figure class="wp-block-image">![](http://blogs.msdn.com/photos/dpblogs/images/7441629/original.aspx)</figure>从中可以看出仅仅View Generation一个操作就占用了56%的时间，不过令人欣慰的是，这个操作只出现在第一次查询的时候，之后生成好的View会被缓存起来供以后使用。

我们可以使用[EDMGen2.exe](http://code.msdn.microsoft.com/EdmGen2)来自己生成View.cs，然后把它加入到工程中编译，这样会大大缩减View Generation操作所占的时间比。根据ADO.NET TEAM 的测试，自己编译View大概会节省28%的时间。不过我在自己电脑上测试的结果没有那么理想，大概是8%左右。

关于EF查询性能方面更详细的内容参看ADO.NET Team blog的文章：[ADO.NET Entity Framework Performance Comparison](http://blogs.msdn.com/adonet/archive/2008/03/27/ado-net-entity-framework-performance-comparison.aspx)