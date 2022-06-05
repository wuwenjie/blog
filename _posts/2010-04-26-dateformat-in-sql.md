---
id: 215
title: Sql常用日期格式
date: '2010-04-26T13:01:29+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=215'
permalink: /2010/04/dateformat-in-sql
categories:
    - 数据库
---

[SQL](https://www.cnblogs.com/html/cgTutorials/Website/SQL/SQL.shtml) [Server](https://www.cnblogs.com/html/cgTutorials/Website/SERVER/Server.shtml)中文版的默认的日期字段datetime格式是yyyy-mm-dd Thh:mm:ss.mmm   
例如:   
select getdate()   
2004-09-12 11:06:08.177   
整理了一下[SQL](https://www.cnblogs.com/html/cgTutorials/Website/SQL/SQL.shtml) [Server](https://www.cnblogs.com/html/cgTutorials/Website/SERVER/Server.shtml)里面可能经常会用到的日期格式转换方法:   
举例如下:   
select CONVERT(varchar, getdate(), 120 )   
2004-09-12 11:06:08

select replace(replace(replace(CONVERT(varchar, getdate(), 120 ),’-‘,”),’ ‘,”),’:’,”)   
20040912110608

select CONVERT(varchar(12) , getdate(), 111 )   
2004/09/12

select CONVERT(varchar(12) , getdate(), 112 )   
20040912

select CONVERT(varchar(12) , getdate(), 102 )   
2004.09.12

select CONVERT(varchar(12) , getdate(), 101 )   
09/12/2004

select CONVERT(varchar(12) , getdate(), 103 )   
12/09/2004

select CONVERT(varchar(12) , getdate(), 104 )   
12.09.2004

select CONVERT(varchar(12) , getdate(), 105 )   
12-09-2004

select CONVERT(varchar(12) , getdate(), 106 )   
12 09 2004

select CONVERT(varchar(12) , getdate(), 107 )   
09 12, 2004

select CONVERT(varchar(12) , getdate(), 108 )   
11:06:08

select CONVERT(varchar(12) , getdate(), 109 )   
09 12 2004 1

select CONVERT(varchar(12) , getdate(), 110 )   
09-12-2004

select CONVERT(varchar(12) , getdate(), 113 )   
12 09 2004 1

select CONVERT(varchar(12) , getdate(), 114 )   
11:06:08.177