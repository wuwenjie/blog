---
id: 157
title: 'SQL2005、SQL2008如何压缩日志文件(log) 如何清除日志'
date: '2010-11-02T18:25:31+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=157'
permalink: /2010/11/sql2005-sql2008-make-log-smaller
categories:
    - 数据库
---

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="sql" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">           ALTER DATABASE [DataBaseName] 
            SET RECOVERY SIMPLE;
             DBCC SHRINKFILE (DataBaseName_log, 1);
            ALTER DATABASE [DataBaseName] 
             SET RECOVERY full;
```