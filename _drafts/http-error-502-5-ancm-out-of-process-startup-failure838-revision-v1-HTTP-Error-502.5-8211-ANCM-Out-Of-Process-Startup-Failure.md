---
id: 856
title: 'HTTP Error 502.5 &#8211; ANCM Out-Of-Process Startup Failure'
date: '2019-10-20T22:37:00+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/10/838-revision-v1'
permalink: '/?p=856'
---

#### 在新电脑安装了VS2019，运行旧的.net core项目，报错，如下：

HTTP Error 502.5 – ANCM Out-Of-Process Startup Failure

Common solutions to this issue:

- The application process failed to start
- The application process started but then stopped
- The application process started but failed to listen on the configured port

Troubleshooting steps:

- Check the system event log for error messages
- Enable logging the application process’ stdout messages
- Attach a debugger to the application process and inspect

For more information visit: [https://go.microsoft.com/fwlink/?LinkID=808681](https://go.microsoft.com/fwlink/?linkid=808681)

#### 解决方法很简单，就是去以下链接下载.net core对应的版本，重新安装就可以了：

<https://dotnet.microsoft.com/download/dotnet-core>