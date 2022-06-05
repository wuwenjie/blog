---
id: 786
title: weinre移动真机调试工具
date: '2019-08-31T14:06:12+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/785-revision-v1'
permalink: '/?p=786'
---

官网地址： <http://people.apache.org/~pmuellr/weinre/docs/latest/Home.html>

# 1.简介

weinre是一款非常好用的远程调试工具。功能与网页浏览器的开发者工具基本类似，但这个工具更适合用于移动端web的调试。

# 2.安装

weinre基于nodejs，因此首先要安装nodejs，然后使用npm进行安装：

```
<pre class="wp-block-preformatted">npm -g install weinre
```

# 3.运行

```
<pre class="wp-block-preformatted">weinre --httpPort 8081 --boundHost -all-
```

主要参数解析：

| httpPort | 调试服务器运行的端口，默认8080 |
|---|---|
| boundHost | 调试服务器绑定的IP地址或域名，默认localhost |

# 4.修改目标文件

- 使用webkit的浏览器（NOTE:由于weinre的设计更多的是基于webkit的浏览器，因此建议使用chrome/safari）访问weinre服务器：http://localhost:8081

<figure class="wp-block-image">![](https://images0.cnblogs.com/blog/311428/201409/262122384203674.png)</figure>- 打开debug面板：http://localhost:8081/client/#anonymous

<figure class="wp-block-image">![](https://images0.cnblogs.com/blog/311428/201409/262141092951686.jpg)</figure>- 获取本机的IP地址，例如：192.168.1.101，添加如下js文件到需要调试的目标文件的头部：

```
<pre class="wp-block-preformatted"><script src="http://192.168.1.101:8081/target/target-script-min.js#anonymous"></script>
```

# 5.在移动设备上访问本机IP地址

<figure class="wp-block-image">![](https://images0.cnblogs.com/blog/311428/201409/262155045928006.png)</figure>并且在debug面板中可以监听到移动设备对目标页面的访问：

<figure class="wp-block-image">![](https://images0.cnblogs.com/blog/311428/201409/262159076545629.png)</figure># 6.Ending

之后的操作，就跟PC端浏览器debug一样简单了… 🙂