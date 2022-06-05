---
id: 217
title: SQL触发器的使用及语法
date: '2010-04-10T17:27:49+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=217'
permalink: /2010/04/sql-trigger
categories:
    - 数据库
---

定义： 何为触发器？在SQL Server里面也就是对某一个表的一定的操作，触发某种条件，从而执行的一段程序。触发器是一个特殊的存储过程。   
常见的触发器有三种：分别应用于Insert , Update , Delete 事件。

我为什么要使用触发器？比如，这么两个表：

Create Table Student( –学生表   
StudentID int primary key, –学号   
….   
)

Create Table BorrowRecord( –学生借书记录表   
BorrowRecord int identity(1,1), –流水号   
StudentID int , –学号   
BorrowDate datetime, –借出时间   
ReturnDAte Datetime, –归还时间   
…   
)

用到的功能有:   
1.如果我更改了学生的学号,我希望他的借书记录仍然与这个学生相关(也就是同时更改借书记录表的学号);   
2.如果该学生已经毕业，我希望删除他的学号的同时，也删除它的借书记录。   
等等。

这时候可以用到触发器。对于1，创建一个Update触发器：

Create Trigger truStudent   
On Student –在Student表中创建触发器   
for Update –为什么事件触发   
As –事件触发后所要做的事情   
if Update(StudentID)   
begin

Update BorrowRecord   
Set StudentID=i.StudentID   
From BorrowRecord br , Deleted d ,Inserted i –Deleted和Inserted临时表   
Where br.StudentID=d.StudentID

end

理解触发器里面的两个临时的表：Deleted , Inserted 。注意Deleted 与Inserted分别表示触发事件的表“旧的一条记录”和“新的一条记录”。   
一个数据库系统中有两个虚拟表用于存储在表中记录改动的信息，分别是：   
虚拟表Inserted 虚拟表Deleted

在表记录新增时 存放新增的记录 不存储记录   
修改时 存放用来更新的新记录 存放更新前的记录   
删除时 不存储记录 存放被删除的记录

一个Update 的过程可以看作为：生成新的记录到Inserted表，复制旧的记录到Deleted表，然后删除Student记录并写入新纪录。

对于2，创建一个Delete触发器   
Create trigger trdStudent   
On Student   
for Delete   
As   
Delete BorrowRecord   
From BorrowRecord br , Delted d   
Where br.StudentID=d.StudentID

从这两个例子我们可以看到了触发器的关键：A.2个临时的表；B.触发机制。

**SQL触发器实例2**

/\*   
建立虚拟测试环境，包含：表\[卷烟库存表\]，表\[卷烟销售表\]。   
请大家注意跟踪这两个表的数据，体会触发器到底执行了什么业务逻辑，对数据有什么影响。   
为了能更清晰的表述触发器的作用，表结构存在数据冗余，且不符合第三范式，这里特此说明。   
\*/   
USE Master   
GO   
  
IF EXISTS (SELECT NAME FROM SYSOBJECTS WHERE XTYPE = ’U’ AND NAME = ’卷烟库存表’)   
DROP TABLE 卷烟库存表   
GO   
IF EXISTS (SELECT NAME FROM SYSOBJECTS WHERE XTYPE = ’U’ AND NAME = ’卷烟销售表’)   
DROP TABLE 卷烟销售表   
GO   
  
–业务规则：销售金额 = 销售数量 \* 销售单价 业务规则。   
  
CREATE TABLE 卷烟销售表   
(   
卷烟品牌 VARCHAR(40) PRIMARY KEY NOT NULL,   
购货商 VARCHAR(40) NULL,   
销售数量 INT NULL,   
销售单价 MONEY NULL,   
销售金额 MONEY NULL   
)   
GO   
  
–业务规则：库存金额 = 库存数量 \* 库存单价 业务规则。   
  
CREATE TABLE 卷烟库存表   
(   
卷烟品牌 VARCHAR(40) PRIMARY KEY NOT NULL,   
库存数量 INT NULL,   
库存单价 MONEY NULL,   
库存金额 MONEY NULL   
)   
GO   
  
–创建触发器，示例1   
  
/\*   
创建触发器\[T\_INSERT\_卷烟库存表\]，这个触发器较简单。   
说明： 每当\[卷烟库存表\]发生 INSERT 动作，则引发该触发器。   
触发器功能： 强制执行业务规则，保证插入的数据中，库存金额 = 库存数量 \* 库存单价。   
注意： \[INSERTED\]、\[DELETED\]为系统表，不可创建、修改、删除，但可以调用。   
重要： 这两个系统表的结构同插入数据的表的结构。   
\*/   
IF EXISTS (SELECT NAME FROM SYSOBJECTS WHERE XTYPE = ’TR’ AND NAME = ’T\_INSERT\_卷烟库存表’)   
DROP TRIGGER T\_INSERT\_卷烟库存表   
GO   
  
CREATE TRIGGER T\_INSERT\_卷烟库存表   
ON 卷烟库存表   
FOR INSERT   
AS   
–提交事务处理   
BEGIN TRANSACTION   
–强制执行下列语句，保证业务规则   
UPDATE 卷烟库存表   
SET 库存金额 = 库存数量 \* 库存单价   
WHERE 卷烟品牌 IN (SELECT 卷烟品牌 from INSERTED)   
COMMIT TRANSACTION   
GO   
  
/\*   
针对\[卷烟库存表\]，插入测试数据：   
注意，第一条数据（红塔山新势力）中的数据符合业务规则，   
第二条数据（红塔山人为峰）中，\[库存金额\]空，不符合业务规则，   
第三条数据（云南映像）中，\[库存金额\]不等于\[库存数量\]乘以\[库存单价\]，不符合业务规则。   
第四条数据库存数量为0。   
请注意在插入数据后，检查\[卷烟库存表\]中的数据是否 库存金额 = 库存数量 \* 库存单价。   
\*/   
  
INSERT INTO 卷烟库存表(卷烟品牌,库存数量,库存单价,库存金额)   
SELECT ’红塔山新势力’,100,12,1200 UNION ALL   
SELECT ’红塔山人为峰’,100,22,NULL UNION ALL   
SELECT ’云南映像’,100,60,500 UNION ALL   
SELECT ’玉溪’,0,30,0   
GO   
  
–查询数据   
  
SELECT \* FROM 卷烟库存表   
GO   
/\*   
  
结果集   
  
RecordId 卷烟品牌 库存数量 库存单价 库存金额   
——– ———— ——– ——- ———   
1 红塔山新势力 100 12.0000 1200.0000   
2 红塔山人为峰 100 22.0000 2200.0000   
3 云南映像 100 60.0000 6000.0000   
4 玉溪 0 30.0000 .0000   
  
（所影响的行数为 4 行）   
  
\*/   
  
–触发器示例2   
  
/\*   
创建触发器\[T\_INSERT\_卷烟销售表\]，该触发器较复杂。   
说明: 每当\[卷烟库存表\]发生 INSERT 动作，则引发该触发器。   
触发器功能： 实现业务规则。   
业务规则: 如果销售的卷烟品牌不存在库存或者库存为零，则返回错误。   
否则则自动减少\[卷烟库存表\]中对应品牌卷烟的库存数量和库存金额。   
\*/   
IF EXISTS (SELECT NAME FROM SYSOBJECTS WHERE XTYPE = ’TR’ AND NAME = ’T\_INSERT\_卷烟销售表’)   
DROP TRIGGER T\_INSERT\_卷烟销售表   
GO   
  
CREATE TRIGGER T\_INSERT\_卷烟销售表   
ON 卷烟销售表   
FOR INSERT   
AS   
BEGIN TRANSACTION   
–检查数据的合法性：销售的卷烟是否有库存，或者库存是否大于零   
IF NOT EXISTS (   
SELECT 库存数量   
FROM 卷烟库存表   
WHERE 卷烟品牌 IN (SELECT 卷烟品牌 FROM INSERTED)   
)   
BEGIN   
–返回错误提示   
RAISERROR(’错误！该卷烟不存在库存，不能销售。’,16,1)   
–回滚事务   
ROLLBACK   
RETURN   
END   
  
IF EXISTS (   
SELECT 库存数量   
FROM 卷烟库存表   
WHERE 卷烟品牌 IN (SELECT 卷烟品牌 FROM INSERTED) AND   
库存数量 &lt;= 0   
)   
BEGIN   
–返回错误提示   
RAISERROR(’错误！该卷烟库存小于等于0，不能销售。’,16,1)   
–回滚事务   
ROLLBACK   
RETURN   
END   
  
–对合法的数据进行处理   
  
–强制执行下列语句，保证业务规则   
UPDATE 卷烟销售表   
SET 销售金额 = 销售数量 \* 销售单价   
WHERE 卷烟品牌 IN (SELECT 卷烟品牌 FROM INSERTED)   
  
DECLARE @卷烟品牌 VARCHAR(40)   
SET @卷烟品牌 = (SELECT 卷烟品牌 FROM INSERTED)   
  
DECLARE @销售数量 MONEY   
SET @销售数量 = (SELECT 销售数量 FROM INSERTED)   
  
UPDATE 卷烟库存表   
SET 库存数量 = 库存数量 – @销售数量,   
库存金额 = (库存数量 – @销售数量)\*库存单价   
WHERE 卷烟品牌 = @卷烟品牌   
COMMIT TRANSACTION   
GO   
  
–请大家自行跟踪\[卷烟库存表\]和\[卷烟销售表\]的数据变化。   
–针对\[卷烟销售表\]，插入第一条测试数据，该数据是正常的。   
  
INSERT INTO 卷烟销售表(卷烟品牌,购货商,销售数量,销售单价,销售金额)   
SELECT ’红塔山新势力’,’某购货商’,10,12,1200   
GO   
  
–针对\[卷烟销售表\]，插入第二条测试数据，该数据 销售金额 不等于 销售单价 \* 销售数量。   
–触发器将自动更正数据，使 销售金额 等于 销售单价 \* 销售数量。   
  
INSERT INTO 卷烟销售表(卷烟品牌,购货商,销售数量,销售单价,销售金额)   
SELECT ’红塔山人为峰’,’某购货商’,10,22,2000   
GO   
  
–针对\[卷烟销售表\]，插入第三条测试数据，该数据中的卷烟品牌在 卷烟库存表中找不到对应。   
–触发器将报错。   
  
INSERT INTO 卷烟销售表(卷烟品牌,购货商,销售数量,销售单价,销售金额)   
SELECT ’红河V8’,’某购货商’,10,60,600   
GO   
  
/\*   
结果集   
服务器: 消息 50000，级别 16，状态 1，过程 T\_INSERT\_卷烟销售表，行 15   
错误！该卷烟不存在库存，不能销售。   
\*/   
  
–针对\[卷烟销售表\]，插入第三条测试数据，该数据中的卷烟品牌在 卷烟库存表中库存为0。   
–触发器将报错。   
  
INSERT INTO 卷烟销售表(卷烟品牌,购货商,销售数量,销售单价,销售金额)   
SELECT ’玉溪’,’某购货商’,10,30,300   
GO   
  
/\*   
结果集   
服务器: 消息 50000，级别 16，状态 1，过程 T\_INSERT\_卷烟销售表，行 29   
错误！该卷烟库存小于等于0，不能销售。   
\*/   
–查询数据   
SELECT \* FROM 卷烟库存表   
  
SELECT \* FROM 卷烟销售表   
GO   
  
/\*   
补充：   
1、本示例主要通过一个简单的业务规则实现来进行触发器使用的说明，具体的要根据需要灵活处理；   
2、关于触发器要理解并运用好 INSERTED ，DELETED 两个系统表；   
3、本示例创建的触发器都是 FOR INSERT ,具体的语法可参考：   
/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

 Trigger语法

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////  
  
  
CREATE TRIGGER trigger\_name   
ON { table | view }   
\[ WITH ENCRYPTION \] –用于加密触发器   
{   
{ { FOR | AFTER | INSTEAD OF } { \[ INSERT \] \[ , \] \[ UPDATE \] }   
\[ WITH APPEND \]   
\[ NOT FOR REPLICATION \]   
AS   
\[ { IF UPDATE ( column )   
\[ { AND | OR } UPDATE ( column ) \]   
\[ …n \]   
| IF ( COLUMNS\_UPDATED ( ) { bitwise\_operator } updated\_bitmask )   
{ comparison\_operator } column\_bitmask \[ …n \]   
} \]   
sql\_statement \[ …n \]   
}   
}   
  
4、关于触发器，还应该注意   
(1)、DELETE 触发器不能捕获 TRUNCATE TABLE 语句。   
(2)、触发器中不允许以下 Transact-SQL 语句：   
ALTER DATABASE CREATE DATABASE DISK INIT   
DISK RESIZE DROP DATABASE LOAD DATABASE   
LOAD LOG RECONFIGURE RESTORE DATABASE   
RESTORE LOG   
(3)、触发器最多可以嵌套 32 层。   
  
\*/   
  
–修改触发器   
–实质上，是将 CREATE TRIGGER … 修改为 ALTER TRIGGER …即可。   
  
–删除触发器   
DROP TRIGGER xxx   
GO   
  
–删除测试环境   
DROP TABLE 卷烟库存表   
GO   
DROP TABLE 卷烟销售表   
GO   
DROP TRIGGER T\_INSERT\_卷烟库存表   
GO   
DROP TRIGGER T\_INSERT\_卷烟销售表   
GO   
\##################################################################   
触发器的基础知识和例子   
：create trigger tr\_name   
on table/view   
{for | after | instead of } \[update\]\[,\]\[insert\]\[,\]\[delete\]   
\[with encryption\]   
as {batch | if update (col\_name) \[{and|or} update (col\_name)\] }   
  
说明：   
1 tr\_name ：触发器名称   
2 on table/view ：触发器所作用的表。一个触发器只能作用于一个表   
3 for 和after ：同义   
4 after 与instead of :sql 2000新增项目afrer 与 instead of 的区别   
After   
在触发事件发生以后才被激活,只可以建立在表上   
Instead of   
代替了相应的触发事件而被执行,既可以建立在表上也可以建立在视图上   
5 insert、update、delete：激活触发器的三种操作，可以同时执行，也可选其一   
6 if update (col\_name)：表明所作的操作对指定列是否有影响，有影响，则激活触发器。此外，因为delete 操作只对行有影响，   
所以如果使用delete操作就不能用这条语句了(虽然使用也不出错，但是不能激活触发器，没意义)。   
7 触发器执行时用到的两个特殊表：deleted ,inserted   
deleted 和inserted 可以说是一种特殊的临时表，是在进行激活触发器时由系统自动生成的，其结构与触发器作用的表结构是一   
样的，只是存放 的数据有差异。   
  
续   
下面表格说明deleted 与inserted 数据的差异   
deleted 与inserted 数据的差异   
Inserted   
存放进行insert和update 操作后的数据   
Deleted   
存放进行delete 和update操作前的数据   
注意：update 操作相当于先进行delete 再进行insert ,所以在进行update操作时，修改前的数据拷贝一条到deleted 表中，修改后   
的数据在存到触发器作用的表的同时，也同时生成一条拷贝到insered表中

/////////

CREATE TRIGGER \[TRIGGER admixture\_receive\_log\] ON dbo.chl\_lydj   
FOR UPDATE  
AS  
begin  
declare @djsfxg char(10) declare @wtbh char(20)  
select @wtbh=wtbh from inserted  
update ly\_tzk set djsfxg=’已修改’ where   
end  
if (select data\_sfjl from t\_logsetup)=’是’  
begin  
declare @oldcjmc char (100) declare @oldlyrq datetime  
declare @oldbzbh char (60) declare @oldzl char (20)  
declare @olddj char (10)

declare @newcjmc char (100) declare @newlyrq datetime  
declare @newbzbh char (60) declare @newzl char (20)  
declare @newdj char (10)

 declare @xgr char (20)

 select @oldcjmc=cjmc,@oldlyrq=lyrq,@oldbzbh=bzbh,@oldzl=zl,@olddj=dj from deleted  
select @newcjmc=cjmc,@newlyrq=lyrq,@newbzbh=bzbh,@newzl=zl,@newdj=dj from inserted  
select @xgr=xgr from t\_modifyuser where @wtbh=wtbh  
  
if @oldcjmc&lt;&gt;@newcjmc  
begin  
insert into t\_modifylog (wtbh, mod\_time, mod\_table, mod\_field, ori\_value, now\_value, mod\_people) values  
(@wtbh,getdate(), ‘chl\_lydj’,’cjmc’, @oldcjmc, @newcjmc, @xgr)  
end

  
end  
//////////修改时，直接把‘create’改为‘alter’即可

/////////////////////////

CREATE TRIGGER \[TRIGGER ly\_tzk\_syf\] ON dbo.ly\_tzk   
FOR insert   
AS  
begin  
declare @clmc char(100) declare @dwbh char(100) declare @syf char(100) declare @dwgcbh char(100) declare @wtbh char(50)   
declare @dj\_1 money declare @feiyong\_z money declare @feiyong\_xf money declare @feiyong\_sy money   
declare @dj char(20)  
select @wtbh=wtbh , @clmc=clmc , @dwbh=dwbh ,@syf=syf from inserted  
select @dj=dj from feihao\_bz where   
select @feiyong\_z=feiyong\_z, @feiyong\_xf=feiyong\_xf, @feiyong\_sy=feiyong\_sy from gongchengxinxi where

 set @dj\_1=convert(money ,@dj)  
if @dj\_1 &lt;&gt;0   
begin  
set @feiyong\_xf=@feiyong\_xf+@dj\_1  
set @feiyong\_sy=@feiyong\_sy-@dj\_1  
  
update ly\_tzk set where   
update gongchengxinxi set , where   
end  
else update ly\_tzk set syf=convert(char , 0.0) where

end

//////////////////////

CREATE TRIGGER \[TRIGGER ly\_tzk\_syf\_shanchu\] ON dbo.ly\_tzk   
FOR delete   
AS  
begin  
declare @clmc char(100) declare @dwbh char(100) declare @dwgcbh char(100) declare @wtbh char(50)   
declare @feiyong\_z money declare @feiyong\_xf money declare @feiyong\_sy money   
declare @syf char(100) declare @syf\_1 money  
–declare @dj char(20) declare @dj\_1 money   
select @wtbh=wtbh , @clmc=clmc , @dwbh=dwbh ,@syf=syf from inserted  
–select @dj=dj from feihao\_bz where   
select @feiyong\_z=feiyong\_z, @feiyong\_xf=feiyong\_xf, @feiyong\_sy=feiyong\_sy from gongchengxinxi where

 set @syf\_1=convert(money ,@syf)  
if @syf\_1 &lt;&gt;0   
begin  
set @feiyong\_xf=@feiyong\_xf-@syf\_1  
set @feiyong\_sy=@feiyong\_sy+@syf\_1   
update gongchengxinxi set , where   
end  
end

//////////////////////