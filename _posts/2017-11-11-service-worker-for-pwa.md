---
id: 587
title: 'Service Worker实现离线应用PWA的简单介绍'
date: '2017-11-11T15:28:10+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=587'
permalink: /2017/11/service-worker-for-pwa
categories:
    - Javascript
---

# 什么是Service Worker

> Service workers essentially act as proxy servers that sit between web applications, and the browser and network (when available). They are intended to (amongst other things) enable the creation of effective offline experiences, intercepting network requests and taking appropriate action based on whether the network is available and updated assets reside on the server. They will also allow access to push notifications and background sync APIs.
> 
> — from [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)

翻译过来就是：Service workers 本质上充当Web应用程序与浏览器之间的代理服务器，也可以在网络可用时作为浏览器和网络间的代理。它们旨在（除其他之外）使得能够创建有效的**离线体验**，**拦截网络请求**并基于网络是否可用以及更新的资源是否驻留在服务器上来采取适当的动作。他们还允许访问推送通知和后台同步API。

service worker运行在非主线程的其他线程上，所以不会阻塞主线。，有自己独立的上下文，不能访问DOM。只能使用异步api。并且为了安全，service worker只能运行在https之上。部分浏览器的隐私模式也无法使用。

由于service workers是由chrome提出推广的技术，所以chrome支持最好。其他浏览器的支持情况就参考`Can I Use`了:

<figure class="wp-block-image">![clipboard.png](https://segmentfault.com/img/bVX0yo?w=2518&h=960)</figure>## 生命周期

盗用MDN的一张图。

<figure class="wp-block-image">![clipboard.png](https://segmentfault.com/img/bVX0yB?w=1042&h=1550)</figure># 离线应用相关接口

`CacheStorage` 在浏览器上的引用名叫 caches，CacheStorage 是多个 Cache 的集合，而每个 Cache 可以存储多个 Response 对象。虽然它是被定义在 ServiceWorker 的规范中，但可以在其他worker和window中使用。

在`caches`上调用 `open` 方法就可以异步地得到一个`Cache`对象的引用。

`Cache.match(request, options)`

返回一个`Promise`对象，resolve的结果是跟`Cache`对象匹配的第一个已经缓存的请求。

`Cache.matchAll(request, options)`

返回一个`Promise` 对象，resolve的结果是跟`Cache`对象匹配的所有请求组成的数组。

`Cache.add(request)`

抓取这个URL, 检索并把返回的response对象添加到给定的Cache对象.这在功能上等同于调用 fetch(), 然后使用 Cache.put() 将response添加到cache中.

`Cache.addAll(requests)`

抓取一个URL数组，检索并把返回的response对象添加到给定的Cache对象。

`Cache.put(request, response)`

同时抓取一个请求及其响应，并将其添加到给定的cache。

`Cache.delete(request, options)`

搜索key值为request的`Cache`条目。如果找到，则删除该`Cache`条目，并且返回一个resolve为true的`Promise`对象；如果未找到，则返回一个resolve为false的`Promise`对象。

`Cache.keys(request, options)`

返回一个`Promise`对象，resolve的结果是`Cache`对象key值组成的数组。

# 代码

以下是一个实现离线应用的demo – [ServiceWorkerDemo](https://github.com/lxzxl/ServiceWorkerDemo)

这个demo是一个简陋的离线应用，会缓存所有静态资源请求，即使你修改了index.js和index.css文件，刷新页面还是没有变化。要想看到新的变化，必须更改`CACHE_KEY`或者修改`fetch`事件的处理逻辑。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const CACHE_KEY = 'demo';
const CACHE_FILES = [
    '/',
    'bg.jpg',
    'index.js',
    'index.css'
];


self.addEventListener('install', function(event) { // 监听worker的install事件
    event.waitUntil( // 延迟install事件直至缓存初始化完成
        caches.open(CACHE_KEY)
            .then(function(cache) {
                console.log('Cache created');
                return cache.addAll(CACHE_FILES);
            })
    );
});

self.addEventListener('activate', function(event) { // 监听worker的activate事件
    event.waitUntil( // 延迟activate事件直到cache初始化完成
        caches.keys().then(function(keys) {
            return Promise.all(keys.map(function(key, i) { // 清除旧版本缓存
                if (key !== CACHE_KEY) {
                    return caches.delete(keys[i]);
                }
            }))
        })
    )
});

self.addEventListener('fetch', function(event) { // 拦截资源请求
    event.respondWith( // 返回资源请求
        caches.match(event.request).then(function(res) { // 判断是否命中缓存
            if (res) {  // 返回缓存的资源
                return res;
            }
            fallback(event); // 执行请求备份操作
        })
    )
});

function fallback(event) {  // 恢复原始请求
    const url = event.request.clone();
    return fetch(url).then(function(res) { // 请求资源
        //if not a valid response send the error
        if (!res || res.status !== 200 || res.type !== 'basic') {
            return res;
        }

        const response = res.clone();

        caches.open(CACHE_KEY).then(function(cache) { // 缓存从刚刚下载的资源
            cache.put(event.request, response);
        });

        return res;
    })
}
```

# 其他用途

1. 消息推送
2. 后台消息传递
3. 网络代理，转发请求，伪造响应