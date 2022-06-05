---
id: 229
title: 只有mdf文件而没有ldf文件修复方法
date: '2010-05-05T17:58:27+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=229'
permalink: /2010/05/restore-database-with-mdf-but-without-ldf
categories:
    - 数据库
---

只有mdf文件而没有ldf文件修复log文件或者重新生成一个log文件的方法

EXEC sp\_attach\_single\_file\_db @dbname = ‘data’,   
@physname = ‘E:\\DataBase\\data.mdf ‘

  
只有mdf文件的恢复技术   
由于种种原因，我们如果当时仅仅备份了mdf文件，那么恢复起来就是一件很麻烦的事情了。   
如果您的mdf文件是当前数据库产生的，那么很侥幸，也许你使用sp\_attach\_db或者sp\_attach\_single\_file\_db可以恢复数据库，但是会出现类似下面的提示信息   
设备激活错误。物理文件名 ’C:\\Program Files\\Microsoft SQL Server\\MSSQL\\data\\test\_Log.LDF’ 可能有误。   
已创建名为 ’C:\\Program Files\\Microsoft SQL Server\\MSSQL\\Data\\test\_log.LDF’ 的新日志文件。   
但是，如果您的数据库文件是从其他计算机上复制过来的，那么很不幸，也许上述办法就行不通了。你也许会得到类似下面的错误信息   
服务器: 消息 1813，级别 16，状态 2，行 1   
未能打开新数据库 ’test’。CREATE DATABASE 将终止。   
设备激活错误。物理文件名 ’d:\\test\_log.LDF’ 可能有误。   
怎么办呢？别着急，下面我们举例说明恢复办法。   
A．我们使用默认方式建立一个供恢复使用的数据库(如test)。可以在SQL Server Enterprise Manager里面建立。   
B．停掉数据库服务器。   
C．将刚才生成的数据库的日志文件test\_log.ldf删除，用要恢复的数据库mdf文件覆盖刚才生成的数据库数据文件test\_data.mdf。   
D．启动数据库服务器。此时会看到数据库test的状态为“置疑”。这时候不能对此数据库进行任何操作。   
E．设置数据库允许直接操作系统表。此操作可以在SQL Server Enterprise Manager里面选择数据库服务器，按右键，选择“属性”，在“服务器设置”页面中将“允许对系统目录直接修改”一项选中。也可以使用如下语句来实现。   
use master   
go   
sp\_configure ’allow updates’,1   
go   
reconfigure with override   
go   
F．设置test为紧急修复模式   
update sysdatabases set status=-32768 where dbid=DB\_ID(’test’)   
此时可以在SQL Server Enterprise Manager里面看到该数据库处于“只读\\置疑\\脱机\\紧急模式”可以看到数据库里面的表，但是仅仅有系统表   
G．下面执行真正的恢复操作，重建数据库日志文件   
dbcc rebuild\_log(’test’,’C:\\Program Files\\Microsoft SQL Server\\MSSQL\\Data\\test\_log.ldf’)   
执行过程中，如果遇到下列提示信息：   
服务器: 消息 5030，级别 16，状态 1，行 1   
未能排它地锁定数据库以执行该操作。   
DBCC 执行完毕。如果 DBCC 输出了错误信息，请与系统管理员联系。   
说明您的其他程序正在使用该数据库，如果刚才您在F步骤中使用SQL Server Enterprise Manager打开了test库的系统表，那么退出SQL Server Enterprise Manager就可以了。   
正确执行完成的提示应该类似于：   
警告: 数据库 ’test’ 的日志已重建。已失去事务的一致性。应运行 DBCC CHECKDB 以验证物理一致性。将必须重置数据库选项，并且可能需要删除多余的日志文件。   
DBCC 执行完毕。如果 DBCC 输出了错误信息，请与系统管理员联系。   
此时打开在SQL Server Enterprise Manager里面会看到数据库的状态为“只供DBO使用”。此时可以访问数据库里面的用户表了。   
H．验证数据库一致性（可省略）   
dbcc checkdb(’test’)   
一般执行结果如下：   
CHECKDB 发现了 0 个分配错误和 0 个一致性错误（在数据库 ’test’ 中）。   
DBCC 执行完毕。如果 DBCC 输出了错误信息，请与系统管理员联系。   
I．设置数据库为正常状态   
sp\_dboption ’test’,’dbo use only’,’false’   
如果没有出错，那么恭喜，现在就可以正常的使用恢复后的数据库啦。   
J．最后一步，我们要将步骤E中设置的“允许对系统目录直接修改”一项恢复。因为平时直接操作系统表是一件比较危险的事情。当然，我们可以在SQL Server Enterprise Manager里面恢复，也可以使用如下语句完成   
sp\_configure ’allow updates’,0   
go   
reconfigure with override   
go   
  
  
其他方法:   
  
方法一   
  
备份数据文件,然后按下面的步骤处理:   
  
1.新建一个同名的数据库(数据文件与原来的要一致)   
  
2.再停掉sql server(注意不要分离数据库)   
  
3.用原数据库的数据文件覆盖掉这个新建的数据库   
  
4.再重启sql server   
  
5.此时打开企业管理器时会出现置疑，先不管，执行下面的语句（注意修改其中的数据库名)   
  
USE MASTER   
GO   
  
SP\_CONFIGURE ’ALLOW UPDATES’,1 RECONFIGURE WITH OVERRIDE   
GO   
  
UPDATE SYSDATABASES SET STATUS =32768 WHERE NAME=’置疑的数据库名’   
Go   
  
sp\_dboption ’置疑的数据库名’, ’single user’, ’true’   
Go   
  
DBCC CHECKDB(’置疑的数据库名’)   
Go   
  
update sysdatabases set status =28 where name=’置疑的数据库名’   
Go   
  
sp\_configure ’allow updates’, 0 reconfigure with override   
Go   
  
sp\_dboption ’置疑的数据库名’, ’single user’, ’false’   
Go   
  
  
6.完成后一般就可以访问数据库中的数据了,这时,数据库本身一般还要问题,解决办法是,利用   
数据库的脚本创建一个新的数据库,并将数据导进去就行了.   
  
  
方法二   
  
1、建一个同名的数据库   
2、修改服务器设置：允许多系统目录进行直接修改   
3、停止SQL Server   
4、用原mdf文件覆盖新建库的数据库文件   
5、重启SQL Server（这时数据库应该是置疑）   
6、将数据库置为紧急状态：update master.dbo.sysdatabases set status = 32768 where name = dbname   
7、重建日志：dbcc rebulid\_log(’dbname’, ’logfile’) (可能不需要这一步。)   
8、数据库重新启动，然后再还原数据库状态用以下语句   
update master.dbo.sysdatabases set status = 16 where name = dbname