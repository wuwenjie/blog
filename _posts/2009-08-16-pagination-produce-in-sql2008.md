---
id: 148
title: 'sql 2008 分页存储过程'
date: '2009-08-16T21:14:17+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=148'
permalink: /2009/08/pagination-produce-in-sql2008
categories:
    - 数据库
---

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="sql" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">set ANSI_NULLS ON
set QUOTED_IDENTIFIER ON
go







-- [SelectBase] 1,1,'Users','username=''test'''
ALTER procedure [dbo].[SelectBase]
@PageIndex                    int,
@PageSize                    int,
@TableName       nvarchar(max),
@Where                     nvarchar(max)=''

as
Declare @rowcount          int
Declare @intStart          int
Declare @intEnd         int

Declare @SQl nvarchar(max), @WhereR nvarchar(max), @OrderBy nvarchar(max)
set @rowcount=0
set nocount on

if @Where<>''
begin
  set @Where=' and '+@Where
end

if CHARINDEX('order by', @Where)>0
begin
    set @WhereR=substring(@Where, 1, CHARINDEX('order by',@Where)-1)    --取得条件
    set @OrderBy=substring(@Where, CHARINDEX('order by',@Where), Len(@Where))    --取得排序方式(order by 字段 方式)
end
else
begin
    set @WhereR=@Where
    declare @PKName nvarchar(50)
    if(len(@TableName)>50)
    begin
        Set @PKName='ID'
    end
    else
    begin
        select top 1 @PKName=[name] from syscolumns where id=object_id(@TableName) order by colstat desc
    end
    set @OrderBy=' order by '+@PKName+' asc'
end

set @SQl='SELECT @rowcount=count(*) from '+cast(@TableName as nvarchar(3000))+' where 1=1 '+@WhereR
exec sp_executeSql @SQl,N'@rowcount int output',@rowcount output

if @PageIndex=0 and @PageSize=0    --不进行分页,查询所有数据列表
begin
    set @SQl='SELECT * from '+cast(@TableName as nvarchar(3000))+' where 1=1 '+@Where
end
else    --进行分页查询数据列表
begin
    set @intStart=(@PageIndex-1)*@PageSize+1;
    set @intEnd=@intStart+@PageSize-1

    set @SQl='select * from(select *,ROW_NUMBER() OVER('+cast(@OrderBy as nvarchar)+') as row from '
    set @SQl=@SQL+@TableName+' where 1=1 '+@WhereR+') as a where row between '+cast(@intStart as varchar)+' and '+cast(@intEnd as varchar)
end
--print @SQl
exec sp_executeSql @SQl
--select @rowcount
return @rowcount

--------------------------------------------
--print @SQl
--exec [SelectBase] 1,8,'SpaceContent','UserInfoID=45'

set nocount off
```