---
id: 154
title: MongoDB字段文本替换函数
date: '2019-08-15T18:19:03+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/153-revision-v1/'
permalink: '/?p=154'
---

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">function (collection,field,oldValue,newValue)
{
  var index=0;
  collection="pagango."+collection;
  var cursor = db[collection].find();
  while (cursor.hasNext()) {
      var x = cursor.next();
      if(!x[field]) {continue;}
      x[field]=x[field].replace(oldValue,newValue);
      db[collection].update({_id : x._id}, x);
      index++;
   }
   var date=new Date();
   return index+" items replace."+date.getHours()+":"+date.getMinutes()
}
```