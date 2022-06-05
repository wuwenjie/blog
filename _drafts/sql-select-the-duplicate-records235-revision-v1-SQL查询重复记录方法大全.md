---
id: 236
title: SQL查询重复记录方法大全
date: '2019-08-16T18:06:03+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/235-revision-v1/'
permalink: '/?p=236'
---

查找所有重复标题的记录：

SELECT \*  
FROM t\_info a  
WHERE ((SELECT COUNT(\*)  
FROM t\_info  
WHERE Title = a.Title) &gt; 1)  
ORDER BY Title DESC

一。查找重复记录

1。查找全部重复记录

Select \* From 表 Where 重复字段 In (Select 重复字段 From 表 Group By 重复字段 Having Count(\*)&gt;1)

2。过滤重复记录(只显示一条)

Select \* From HZT Where ID In (Select Max(ID) From HZT Group By Title)

注：此处显示ID最大一条记录

二。删除重复记录

1。删除全部重复记录（慎用）

Delete FROM 表 Where 重复字段 In (Select 重复字段 From 表 Group By 重复字段 Having Count(\*)&gt;1)

2。保留一条（这个应该是大多数人所需要的 ^\_^）

Delete FROM HZT Where ID Not In (Select Max(ID) From HZT Group By Title)

注：此处保留ID最大一条记录

1、查找表中多余的重复记录，重复记录是根据单个字段（peopleId）来判断  
  
select \* from people  
  
where peopleId in (select peopleId from people group by peopleId having count(peopleId) &gt; 1)  
  
  
  
2、删除表中多余的重复记录，重复记录是根据单个字段（peopleId）来判断，只留有rowid最小的记录  
  
delete from people   
  
where peopleId in (select peopleId from people group by peopleId having count(peopleId) &gt; 1)  
  
and rowid not in (select min(rowid) from people group by peopleId having count(peopleId )&gt;1)  
  
  
  
3、查找表中多余的重复记录（多个字段）   
  
select \* from vitae a  
  
where (a.peopleId,a.seq) in (select peopleId,seq from vitae group by peopleId,seq having count(\*) &gt; 1)  
  
  
  
4、删除表中多余的重复记录（多个字段），只留有rowid最小的记录  
  
delete from vitae a  
  
where (a.peopleId,a.seq) in (select peopleId,seq from vitae group by peopleId,seq having count(\*) &gt; 1)  
  
and rowid not in (select min(rowid) from vitae group by peopleId,seq having count(\*)&gt;1)  
  
  
  
5、查找表中多余的重复记录（多个字段），不包含rowid最小的记录  
  
select \* from vitae a  
  
where (a.peopleId,a.seq) in (select peopleId,seq from vitae group by peopleId,seq having count(\*) &gt; 1)  
  
and rowid not in (select min(rowid) from vitae group by peopleId,seq having count(\*)&gt;1)

**补充：**

有两个以上的重复记录，一是完全重复的记录，也即所有字段均重复的记录，二是部分关键字段重复的记录，比如Name字段重复，而其他字段不一定重复或都重复可以忽略。  
  
  
  
1、对于第一种重复，比较容易解决，使用  
  
select distinct \* from tableName  
  
  
  
就可以得到无重复记录的结果集。  
  
  
  
如果该表需要删除重复的记录（重复记录保留1条），可以按以下方法删除  
  
select distinct \* into #Tmp from tableName  
  
drop table tableName  
  
select \* into tableName from #Tmp  
  
drop table #Tmp  
  
  
  
发生这种重复的原因是表设计不周产生的，增加唯一索引列即可解决。  
  
  
  
2、这类重复问题通常要求保留重复记录中的第一条记录，操作方法如下  
  
  
  
假设有重复的字段为Name,Address，要求得到这两个字段唯一的结果集  
  
select identity(int,1,1) as autoID, \* into #Tmp from tableName  
  
select min(autoID) as autoID into #Tmp2 from #Tmp group by Name,autoID  
  
select \* from #Tmp where autoID in(select autoID from #tmp2)  
  
  
  
最后一个select即得到了Name，Address不重复的结果集（但多了一个autoID字段，实际写时可以写在select子句中省去此列）