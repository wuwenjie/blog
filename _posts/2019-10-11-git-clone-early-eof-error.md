---
id: 849
title: 'git clone early EOF error'
date: '2019-10-11T00:34:32+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=849'
permalink: /2019/10/git-clone-early-eof-error
categories:
    - 教程
---

这几天用smartgit 获取项目，一直获取不下来，总是报git clone early EOF error，网上搜索都是说使用 git config –global core.compression -1 等等解决方案，通通不起作用。其实最后发现这些错误的原因是 在国内访问github网速过慢引起的，解决方案的目标就是加快访问GitHub速度：

1\. 我在host中增加以下内容

151.101.113.194 github.global.ssl.fastly.net  
192.30.253.112 github.com

2\. 我开启某灯（VPN）翻强代理

 终于速度快了一些，也不会出现错误了, 成功获取项目。。。

顺便分享一个SmartGit破解补丁 🙂

链接: https://pan.baidu.com/s/1IkNYd0R9YDpox7vmhPxldw 提取码: rn94

新版补丁： <https://gitee.com/pedoc/crackSmartGit/releases>

想了解补丁制作方法，可以参考这个： <https://gitee.com/pedoc/crackSmartGit>

### 设置hosts 屏蔽smart git

127.0.0.1 [www.syntevo.com](http://www.syntevo.com/)

### 修改java运行配置

win +R 输入 %APPDATA%\\syntevo\\SmartGit 进入文件夹 ,把 crackSmartGit.jar 拷贝到这个文件夹中  
编辑smartgit.vmoptions,添加如下内容  
-javaagent:C:\\Users\\用户名\\AppData\\Roaming\\syntevo\\SmartGit\\crackSmartGit.jar

### 注册

重新启动启动smartgit-&gt;帮助-&gt;注册（选择license.zip文件）就行了。