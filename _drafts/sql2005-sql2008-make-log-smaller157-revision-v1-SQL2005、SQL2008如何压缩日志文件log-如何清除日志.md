---
id: 158
title: 'SQL2005、SQL2008如何压缩日志文件(log) 如何清除日志'
date: '2019-08-15T18:25:31+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/157-revision-v1/'
permalink: '/?p=158'
---

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="sql" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">           ALTER DATABASE [DataBaseName] 
            SET RECOVERY SIMPLE;
             DBCC SHRINKFILE (DataBaseName_log, 1);
            ALTER DATABASE [DataBaseName] 
             SET RECOVERY full;
```