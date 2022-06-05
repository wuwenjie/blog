---
id: 211
title: “备份集中的数据库备份与现有的数据库不同”解决方法
date: '2010-04-01T12:57:44+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=211'
permalink: /2010/04/restore-database-different-from-existing-database
categories:
    - 数据库
---

最主要就是要在“选项”中选择“覆盖现有数据库”，否则就会出现“备份集中的数据库备份与现有的数据库”的问题。

<figure class="wp-block-image">[![](http://hiphotos.baidu.com/handboy/pic/item/0607270a90d50c5994ca6b71.jpg)](http://p.blog.csdn.net/images/p_blog_csdn_net/maolixian/EntryImages/20090718/2.jpg)</figure>以前一直使用SQL Server2000，现在跟潮流都这么紧，而且制定要求使用SQL Server2005，就在现在的项目中使用它了。 对于SQL Server 2005，有几个地方是要注意的，比方在还原数据库时，不像2000里边将数据库和文件区分的很细，统一均为文件，这就使还原的数据库文件制定为. bak。那么想还原2000的数据库（备份数据库文件，无后缀名的），就需要自己手工选择。

<figure class="wp-block-image">![](https://www.cnblogs.com/upload/20090719013920752.jpg)</figure><figure class="wp-block-image">![](https://www.cnblogs.com/upload/20090719013920752.jpg)</figure><figure class="wp-block-image">[![](http://hiphotos.baidu.com/handboy/pic/item/777d951f3751f88ea6866971.jpg)](http://p.blog.csdn.net/images/p_blog_csdn_net/maolixian/EntryImages/20090718/4.jpg)</figure>选择下拉框中的“所有文件”，这时就会显示“备份数据库文件”了，选择-确定

最主要就是要在“选项”中选择“覆盖现有数据库”，否则就会出现“备份集中的数据库备份与现有的数据库”的问题。