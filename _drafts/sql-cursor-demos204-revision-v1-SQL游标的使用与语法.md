---
id: 205
title: SQL游标的使用与语法
date: '2019-08-16T12:47:18+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/204-revision-v1/'
permalink: '/?p=205'
---

以\[master\].\[dbo\].\[spt\_values\] 这个表为例子  
===

declare @name nvarchar(35)   
declare @number int

declare my\_cursor cursor for –定义游标cursor1   
select TOP 5 \[name\],\[number\] from \[spt\_values\] –使用游标的对象(跟据需要填入select文)

open my\_cursor –打开游标   
fetch next from my\_cursor into @name,@number –将游标向下移1行，获取的数据放入之前定义的变量@id,@name中

while(@@fetch\_status=0) –判断是否成功获取数据   
begin

–update \[spt\_values\] set \[name\]=@name+’1′   
–where \[number\]=@number+1 –进行相应处理(跟据需要填入SQL文)  
print @name  
print @number  
print ‘====’  
fetch next from my\_cursor into @name,@number –将游标向下移1行

end

close my\_cursor –关闭游标   
deallocate my\_cursor

=====================以下是基础  
[blog.csdn.net/lejuo/archive/2008/11/12/3279340.aspx](http://blog.csdn.net/lejuo/archive/2008/11/12/3279340.aspx)  
可百度 SQL游标语法及举例 进行更深入学习

游标的定义：  
每一个游标必须有四个组成部分这四个关键部分必须符合下面的顺序；   
1.DECLARE 游标   
2.OPEN 游标   
3.从一个游标中FETCH 信息   
4.CLOSE 或DEALLOCATE 游标   
通常我们使用DECLARE 来声明一个游标声明一个游标主要包括以下主要内容：

游标名字   
数据来源（表和列）   
选取条件   
属性（仅读或可修改）   
其语法格式如下：   
DECLARE cursor\_name \[INSENSITIVE\] \[SCROLL\] CURSOR   
FOR select\_statement   
\[FOR {READ ONLY | UPDATE \[OF column\_name \[,…n\]\]}\]   
其中：   
cursor\_name   
指游标的名字。   
INSENSITIVE   
表明MS SQL SERVER 会将游标定义所选取出来的数据记录存放在一临时表内（建立在tempdb 数据库下）。对该游标的读取操作皆由临时表来应答。因此，对基本表的修改并不影响游标提取的数据，即游标不会随着基本表内容的改变而改变，同时也无法通过   
游标来更新基本表。如果不使用该保留字，那么对基本表的更新、删除都会反映到游标中。

另外应该指出，当遇到以下情况发生时，游标将自动设定INSENSITIVE 选项。   
在SELECT 语句中使用DISTINCT、 GROUP BY、 HAVING UNION 语句；   
使用OUTER JOIN；   
所选取的任意表没有索引；   
将实数值当作选取的列。   
SCROLL   
表 明所有的提取操作（如FIRST、 LAST、 PRIOR、 NEXT、 RELATIVE、 ABSOLUTE）都可用。如果不使用该保留字，那么只能进行NEXT 提取操作。由此可见，SCROLL 极大地增加了提取数据的灵活性，可以随意读取结果集中的任一行数据记录，而不必关闭再   
重开游标。   
select\_statement   
是定义结果集的SELECT 语句。应该注意的是，在游标中不能使用COMPUTE、COMPU- TE BY、 FOR BROWSE、 INTO 语句。   
READ ONLY   
表明不允许游标内的数据被更新尽管在缺省状态下游标是允许更新的。而且在UPDATE或DELETE 语句的WHERE CURRENT OF 子句中，不允许对该游标进行引用。   
UPDATE \[OF column\_name\[,…n\]\]   
定义在游标中可被修改的列，如果不指出要更新的列，那么所有的列都将被更新。当游标被成功创。