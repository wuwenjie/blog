---
id: 640
title: 'docker删除所有容器 镜像 数据卷等批量操作命令'
date: '2019-08-18T22:23:04+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/639-revision-v1/'
permalink: '/?p=640'
---

```
<pre class="wp-block-preformatted"> 停止容器
 docker stop $(docker ps -a -q)
 删除容器
 docker rm $(docker ps -a -q)
 删除镜像
 docker image rm $(docker image ls -a -q)
 删除数据卷：
 docker volume rm $(docker volume ls -q)
 删除 network：
 docker network rm $(docker network ls -q)
 最直接并全面清理的的就是以下命令
 $docker stop $(docker ps -a -q) && docker system prune --all --force
```