---
id: 846
title: cnpm在VSCode中提示无法加载文件
date: '2019-10-20T00:30:17+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=846'
permalink: /2019/10/cnpm-not-working-in-vscode-ps
categories:
    - 'HTML&amp;CSS'
    - Javascript
---

在新电脑上安装完cnpm后，vscode中的PowerShell下使用cnpm报错，如下：

PS C:\\Users\\NJ&gt; cnpm -v

cnpm : 无法加载文件 C:\\Users\\NJ\\AppData\\Roaming\\npm\\cnpm.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.c   
om/fwlink/?LinkID=135170 中的 about\_Execution\_Policies。  
所在位置 行:1 字符: 1

- cnpm -v
    - CategoryInfo : SecurityError: (:) \[\]，PSSecurityException
    - FullyQualifiedErrorId : UnauthorizedAccess

解决方法是：

系统搜索powershell，右击powershell使用管理员身份运行，然后输入以下命令执行，

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">set-ExecutionPolicy RemoteSigned
```

输入Y