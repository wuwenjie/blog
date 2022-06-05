---
id: 190
title: ThreadPool怎样判断子线程全部执行完毕
date: '2010-10-11T12:22:45+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=190'
permalink: /2010/10/threadpool-check-all-threads-are-complete
categories:
    - 'net&amp;c#'
---

多线程执行顺序是不确定的， 那使用ThreadPool怎样判断子线程全部执行完毕？

看看这个结果

static void Main(string\[\] args)  
 {  
 try  
 {  
 ThreadPool.SetMaxThreads(3, 3); //设置最大线程数  
 for (int i = 0; i &lt; 10; i++)  
 {  
 ThreadPool.QueueUserWorkItem(new WaitCallback(Auto), i);//线程池指定线程执行Auto方法  
 }

 }  
 catch (Exception ex)  
 {  
 Console.WriteLine(ex.Message);  
 }

 Console.WriteLine(“结束了”); //这句要改   
 Console.ReadLine();  
 }

 public static void Auto(object i)//多线程执行的方法  
 {  
 if (string.Equals(i, 2))  
 {  
 Thread.Sleep(2000);  
 }  
 Console.WriteLine(i.ToString());  
 }

结束了 这三个字不一定 真正在 最后一行输出。因为这时是 主线程+子线程 这些线程的执行顺序不确定，可能主线程老早就执行了。也就说可能结束了 这三个字很早就会输出。

**3、主题 保证 结束了 在最后输出。**

**方法1：**

//这是主线程，一直都会执行。目前一直在进行的是 一个主线程+多个子线程  
 while (true)  
 {  
 Thread.Sleep(1000);//这句写着，主要是没必要循环那么多次。去掉也可以。  
 int maxWorkerThreads, workerThreads;  
 int portThreads;  
 ThreadPool.GetMaxThreads(out maxWorkerThreads, out portThreads);  
 ThreadPool.GetAvailableThreads(out workerThreads, out portThreads);  
 if (maxWorkerThreads – workerThreads == 0)  
 {  
 Console.WriteLine(“结束了”);  
 break;  
 }  
 }

GetAvailableThreads()：检索由 GetMaxThreads 返回的线程池线程的最大数目和当前活动数目之间的差值。  
而GetMaxThreads 检索可以同时处于活动状态的线程池请求的数目。  
通过最大数目减可用数目就可以得到当前活动线程的数目，如果为零，那就说明没有活动线程，说明所有线程运行完毕。

**方法2 :** Monitor

**见下篇文章：**[**http://hi.baidu.com/handboy/blog/item/681d093875d6e6cdd56225ae.html**](http://hi.baidu.com/handboy/blog/item/681d093875d6e6cdd56225ae.html)

class Program  
 {  
 static object locker = new object();  
 static int runningThreads = 0;

 static void Main(string\[\] args)  
 {  
 try  
 {  
 ThreadPool.SetMaxThreads(4, 4); //设置最大线程数 using System.Threading;

 runningThreads = 10;  
 for (int i = 0; i &lt; runningThreads; i++)  
 {  
   
 ThreadPool.QueueUserWorkItem(new WaitCallback(Auto), i);//线程池指定线程执行Auto方法  
 }

 }  
 catch (Exception ex)  
 {  
 Console.WriteLine(ex.Message);  
 }  
 lock (locker)  
 {  
 while (runningThreads &gt; 0)  
 {  
 Monitor.Wait(locker);  
 }  
 }  
 Console.WriteLine(“结束了”);  
 Console.ReadLine();  
 }

 public static void Auto(object i)//多线程执行的方法  
 {  
 if (string.Equals(i, 2))  
 {  
 Thread.Sleep(2000);  
 }  
 lock (locker)  
 {  
 runningThreads–;  
 Monitor.Pulse(locker);  
 }  
 Console.WriteLine(i.ToString());  
 }  
 }

**方法3：WaitHandle**

**见文章吧。<http://tech.it168.com/a2009/1209/821/000000821782.shtml>**public void testThreads(){ ManualResetEvent\[\] \_ManualEvents = new ManualResetEvent\[10\]; for (int i = 0; i &lt; 10; i++) { \_ManualEvents\[i\] = new ManualResetEvent(false); System.Threading.ThreadPool.QueueUserWorkItem(new WaitCallback(testMethod), \_ManualEvents\[i\]); } WaitHandle.WaitAll(\_ManualEvents); // 线程结束后执行后面的主线程代码 Console.WriteLine(“结束了”); Console.ReadLine(); } public void testMethod(object objEvent){ //TODO: Add your code here ManualResetEvent e = (ManualResetEvent)objEvent; e.Set();}