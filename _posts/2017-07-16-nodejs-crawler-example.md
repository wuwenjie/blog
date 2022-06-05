---
id: 641
title: Nodejs写的爬虫及模拟提交知识分享
date: '2017-07-16T22:24:59+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=641'
permalink: /2017/07/nodejs-crawler-example
categories:
    - Javascript
---

**这篇文章 主要学习这两个模块的使用：request-promise-native ： <https://github.com/request/request-promise-native>cheerio ：<https://github.com/cheeriojs/cheerio>**

nodejs有个request模块，专门处理这些网络请求方面的。 就像.NET也有request，webclient，httpclient啥的。。。

nodejs的request使用方法在这，自己查一下： https://github.com/request/request

而我喜欢用async和await的写法，因此我还引入了request-promise-native 模块， https://github.com/request/request-promise-native ，这个就相当于.NET中httpclient吧。

好了背景就讲这么多，我们就开始简单的使用request-promise-native，进行模拟提交。

我用淘宝镜像安装模块，会比较快比较快一些，注册淘宝镜像方式：

```
<pre class="wp-block-preformatted">npm install -g cnpm --registry=https://registry.npm.taobao.org
```

然安装模块：

```
<pre class="wp-block-preformatted">cnpm install --save request
cnpm install --save request-promise-native
```

我们登陆后 ，尝试发一篇文章，然我们分析一下提交的东西：

<figure class="wp-block-image">![](http://images2015.cnblogs.com/blog/170939/201707/170939-20170716012652191-1390536827.png)</figure><figure class="wp-block-image">![](http://images2015.cnblogs.com/blog/170939/201707/170939-20170716012706503-486030262.png)</figure>主要是 Cookie以及 FormData。

好了，然后我们写一个 cnblogs，来处理提交，代码：

```
<pre class="wp-block-preformatted">const request = require('request-promise-native');
// const proxy = 'http://127.0.0.1:8888';
const url = 'https://i.cnblogs.com/EditPosts.aspx?opt=1';
class Cnblogs {
  static async save({ title, content, postdate }) {
    let response = await request({
      url: url,
      method: 'POST',
      headers: {
        Cookie: '[隐私隐私隐私]',
      },
      form: {
        __VIEWSTATE: '===========',
        __VIEWSTATEGENERATOR: 'FE27D343',
        Editor$Edit$txbTitle: title,
        Editor$Edit$EditorBody: `<p style="color: red; font-weight: bold;">原文发布时间为：${postdate} —— 来源于本人的百度文章 [由搬家工具导入]</p>${content}`,
        Editor$Edit$APOptions$Advancedpanel1$cklCategories$0: '1031596',
        Editor$Edit$Advanced$ckbPublished: 'on',
        Editor$Edit$Advanced$chkDisplayHomePage: 'on',
        Editor$Edit$Advanced$chkComments: 'on',
        Editor$Edit$Advanced$chkMainSyndication: 'on',
        Editor$Edit$lkbPost: '发布'
      }
    });
    return response;
  }
}

module.exports = Cnblogs;
```

嗯，我们建立一个 app.js 调用一下测试吧：

```
<pre class="wp-block-preformatted">const cnblogs = require('./cnblogs');

const main = async () => {
  try {
    let response = await cnblogs.save('测试','测试内容','2018-01-01');
    console.log(response);
  } catch (err) {
    console.error('[ERROR]', err);
  }
};

main();
```

执行node app，我们发现， 导入成功了。。。。我用类似的方法 从自己百度文章抓取文章，然后调用 cnblogs.save(); 进行导入：

<figure class="wp-block-image">![](http://images2015.cnblogs.com/blog/170939/201707/170939-20170716013221394-466683723.png)</figure>抓取文章也是很简单的，为了方便从response查找dom，我们可以用这个模块 cheerio ： https://github.com/cheeriojs/cheerio ， 就类似于我们做.NET的时候会用 HtmlAgilityPack 来查找dom一样。

cheerio 可以去看看，他的语法跟jquery一样，使用起来很方便。