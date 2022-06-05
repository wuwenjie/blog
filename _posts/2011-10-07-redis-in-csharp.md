---
id: 227
title: 'C# Redis 使用教程'
date: '2011-10-07T17:54:43+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=227'
permalink: /2011/10/redis-in-csharp
categories:
    - 'net&amp;c#'
---

环境准备

- [Redis (使用Windows版本做测试，运营环境建议使用Linux版本)](https://github.com/dmajkic/redis/downloads)
- [ServiceStack.Redis-v3.00](https://github.com/ServiceStack/ServiceStack.Redis/downloads)

在Windows上运行Redis服务器作开发和测试是很好的，但是在运营环境还是Linux版本靠谱，下面我们就先解压Redis到一个目录下：

<figure class="wp-block-image">[![image](https://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316035910.png)](http://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316017046.png)</figure>运行**redis-server.exe** 看到如下Windows控制台：

<figure class="wp-block-image">[![image](https://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316093679.png)](http://images.cnblogs.com/cnblogs_com/shanyou/201111/20111110231606944.png)</figure>上面我们可以看到Redis运行的端口是6372

我们先玩一下Redis的客户端控制台，在相同目录下运行**redis-cli.exe**会弹出另一个控制台程序，可以参考[Try Redis tutorial](http://try.redis-db.com/)开始你的交互之旅。

输入命令 set car.make “Ford” 添加了一个car.make为Key，Value是Ford的数据进入Redis，输入命令get car.make就可以取回Ford

<figure class="wp-block-image">[![image](https://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316132411.png)](http://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316118116.png)</figure>下面我们进入正题，讲主角ServiceStack.Redis ：

首先创建一个控制台程序，然后解压缩[ServiceStack.Redis-v3.00.zip](https://github.com/downloads/ServiceStack/ServiceStack.Redis/ServiceStack.Redis-v3.00.zip) ，然后添加下面的四个引用

- ServiceStack.Common
- ServiceStack.Interfaces
- ServiceStack.Redis
- ServiceStack.Text

<figure class="wp-block-image">[![image](https://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316196243.png)](http://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316164622.png)</figure>我们下面来写些代码，创建一个Car类并存储几个实例到Redis，然后让一个对象5秒后过期，等待6秒钟后输出Car的实例数

using System;   
using System.Collections.Generic;   
using System.Linq;   
using System.Text;   
using ServiceStack.Redis;   
using System.Threading;

namespace RedisTutorial   
{   
 class Program   
 {   
 static void Main(string\[\] args)   
 {   
 var redisClient = new RedisClient(“localhost”);

 using (var cars = redisClient.GetTypedClient&lt;Car&gt;())   
 {   
 if (cars.GetAll().Count &gt; 0)   
 cars.DeleteAll();

 var dansFord = new Car   
 {   
 Id = cars.GetNextSequence(),   
 Title = “Dan’s Ford”,   
 Make = new Make { Name = “Ford” },   
 Model = new Model { Name = “Fiesta” }   
 };   
 var beccisFord = new Car   
 {   
 Id = cars.GetNextSequence(),   
 Title = “Becci’s Ford”,   
 Make = new Make { Name = “Ford” },   
 Model = new Model { Name = “Focus” }   
 };   
 var vauxhallAstra = new Car   
 {   
 Id = cars.GetNextSequence(),   
 Title = “Dans Vauxhall Astra”,   
 Make = new Make { Name = “Vauxhall” },   
 Model = new Model { Name = “Asta” }   
 };   
 var vauxhallNova = new Car   
 {   
 Id = cars.GetNextSequence(),   
 Title = “Dans Vauxhall Nova”,   
 Make = new Make { Name = “Vauxhall” },   
 Model = new Model { Name = “Nova” }   
 };

 var carsToStore = new List&lt;Car&gt; { dansFord, beccisFord, vauxhallAstra, vauxhallNova };   
 cars.StoreAll(carsToStore);

 Console.WriteLine(“Redis Has-&gt; ” + cars.GetAll().Count + ” cars”);

 cars.ExpireAt(vauxhallAstra.Id, DateTime.Now.AddSeconds(5)); //Expire Vauxhall Astra in 5 seconds

 Thread.Sleep(6000); //Wait 6 seconds to prove we can expire our old Astra

 Console.WriteLine(“Redis Has-&gt; ” + cars.GetAll().Count + ” cars”);

 //Get Cars out of Redis   
 var carsFromRedis = cars.GetAll().Where(car =&gt; car.Make.Name == “Ford”);

 foreach (var car in carsFromRedis)   
 {   
 Console.WriteLine(“Redis Has a -&gt;” + car.Title);   
 }

 }   
 Console.ReadLine();   
 }   
 }

 public class Car   
 {   
 public long Id { get; set; }   
 public string Title { get; set; }   
 public string Description { get; set; }   
 public Make Make { get; set; }   
 public Model Model { get; set; }   
 }

 public class Make   
 {   
 public int Id { get; set; }   
 public string Name { get; set; }   
 }

 public class Model   
 {   
 public int Id { get; set; }   
 public Make Make { get; set; }   
 public string Name { get; set; }   
 }

}

<figure class="wp-block-image">[![image](https://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316238289.png)](http://images.cnblogs.com/cnblogs_com/shanyou/201111/201111102316214617.png)</figure>