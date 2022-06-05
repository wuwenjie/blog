---
id: 116
title: 'ASP.NET2.0 ObjectDataSource的使用详解'
date: '2009-10-20T20:21:11+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=116'
permalink: /2009/10/asp-net-objectdatasource
categories:
    - 'net&amp;c#'
---

<figure class="wp-block-image">![](https://images.cnblogs.com/cnblogs_com/mqingqing123/s45.JPG)</figure>  
  
 这是因为GridView 和其他数据绑定控件调用 Update 操作的自动功能需依赖此命名约定才能工作。参数的命名预期应与 SelectCommand 返回的关联字段值相同。使用此命名约定使得数据绑定控件传递给数据源的值与 SQL Update 语句中的参数相对应成为可能。

此默认命名约定的使用假设 Keys 和 Values 字典的内容相互排斥 — 即用户能够在数据绑定控件处于编辑模式时更新的字段值的命名应该与用于匹配要更新的行的字段值（对于 SqlDataSource，这些字段值在 WHERE 子句中）的命名不同。考虑这点的另一种方式是在 DataKeyNames 上设置的任何字段都应该设置为只读或在数据绑定控件中（例如在 GridView Columns 集合中）不可见。虽然键字段为只读的情况很普遍，但是存在一些有效的方案，其中您将希望能够更新同时还用于匹配要更新的数据行的字段。

例如，如果我们将Products数据库的ProductID列在设计表格结构时设置为nvarchar，它存放的是图书ISDN编号，该编号并不是递增的，因此在运行时，您可以更改ProductID的只，前提是主要不重复即可。

这样我们就需要将该ProductID列设置为ReadOnly=”false”以便允许编辑，另一方面，为了确认哪条记录被更新还需要传递该列的只到更新/删除方法，所以还需要将DataKeyNames设置为ProductID。

这样GridView 将在 Keys 字典中传递该字段的旧值，而在 Values 字典中传递该字段的新值。仍以UpdateProduct为例，当将ProductID的ReadOnly设置为”false”，并且将DataKeyNames设置为ProductID后，对于前面介绍的这条记录

ProductID ProductName CategoryID Price InStore Description

24 生物技术 7 9.0000 2 生物技术丛书