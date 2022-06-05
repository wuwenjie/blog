---
id: 520
title: 'nodejs 文件操作函数'
date: '2016-08-17T23:12:15+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=520'
permalink: /2016/08/nodejs-file-methods
categories:
    - Javascript
---

### 定义文件系统:

var fs = require(‘fs’)

### 一、文件夹操作

- 文件系统：文件夹和文件操作

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var fs ＝ require('fs');
```

- 文件夹操作
- 创建文件夹

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.mkdir(dirname,callback)
     path:
     callback:回调函数 callback(err)
```

- 读取文件夹

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.readdir(dirname,callback)
     path:文件路径
     callback:回调函数 callback(err,files) files:文件数组列表
```

- 删除空文件夹

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.rmdir(dirname,callback)
     path:文件路径
     callback:回调函数  callback(err)
```

### 二、文件操作

- 普通文件操作
- 读取文件内容

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.readFile(filename,callback)
     filename:文件名
     callback(err,data)
```

- 向文件中写入内容

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.writeFile(filename,data,callback)
     filename:写入文件名
     data:写入文件的数据(String|Buffer)
     callback(err):错误信息参数
```

- 向文件中追加内容

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.appendFile(filename,data,callback)
    callback(err)
```

- 删除文件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.unlink(path,callback)
    callback(err)
```

- 文件字节操作（更灵活，也更复杂）
- 打开文件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.open(filename, flags[,mode],callback)
     path:文件路径
     flags:打开方式
          r(只读) r+(读写) 文件不存在则抛出异常 
          w(只写) w+(读写) 文件不存在则创建
          wx(只写) wx+(读写) 文件存在，写入失败
          a(追加写) a+(读取追加) 文件不存在则创建
          ax(追加写) ax+(读取追加) 文件存在，追加失败
     mode:设置文件权限，默认0666
     callback:callback(err,fd)
```

- 读取文件内容

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.read(fd,buffer,offset,length,position,callback)
     fd:文件描述符
     buffer:存放读取文件内容后的数据
     offset:写入buffer的起始位置
     length:文件中读取的字节数
     position:文件读取的起始位置，如果position的值为null，则会从当前文件指针的位置读取
     callback(err,bytesRead,buffer)
          err:错误信息
          bytesRead:读取的字节数
          buffer:缓冲区对象
```

- 写入文件内容 -buffer

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.write(fd,buffer,offset,length[,position],callback)
        buffer:写入文件的buffer数据(Buffer)
        offset:读取buffer的起始位置
        length:写入文件数据的长度
        position:写入文件的起始位置
     callback(err,written,buffer)
          err
          written:实际写入的字节数
          buffer:写入的buffer对象
```

- 写入文件内容 -data数据格式

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.write(fd,data[,position,callback)
    fd:文件描述符
    data:写入文件的数据(String)
    callback(err, written, buffer)
            err:
            written:实际写入的字节数
            buffer:写入的buffer对象
```

- 截断文件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.truncate(fd,len,callback)
     fd:文件描述符
     len:文件内容截取长度
     callback(err)
```

- 关闭文件内容

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.close(fd,callback)
```

- 文件和文件夹共同操作
- 查看文件和文件夹的状态

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.stat(path,callback)
     path:路径|文件
     callback:callback(err,stats)
          stats:打印信息
          stats.isFile()
          stats.isDirectory()
```

- 查看文件与目录是否存在

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.exists(path,callback)
    callback(bool):存在返回true，不存在返回false
已经废弃 建议采用fs.access(path, callback) 实现
```

- 移动/重命名文件或目录

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.rename(oldPath, newPath, callback)
    callback(err)
```

- 监视文件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.watchFile(filename[,options],listener)
     filename:完整路径及文件名
     options:persistent:true表示持续监视，不退出程序（如果设置为false意义何在）；interval单位毫秒，表示每隔多少毫秒监视一次文件
     linstener:文件发生变化时回调 callback(curr,prev)
          curr:fs.stat对象，被修改后文件
          prev:fs.stat对象，被修改前对象
```

- 取消监视文件

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.unwatchFile(filename[,listener])
```

- 监视文件或目录
- filename 可以是一个文件或一个目录

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">fs.watch(filename[,options][,listener])
```