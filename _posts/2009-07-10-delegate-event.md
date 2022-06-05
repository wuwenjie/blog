---
id: 122
title: 委托和事件
date: '2009-07-10T08:26:02+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=122'
permalink: /2009/07/delegate-event
categories:
    - 'net&amp;c#'
---

我们继续思考上面的程序：上面的三个方法都定义在Programe类中，这样做是为了理解的方便，实际应用中，通常都是 GreetPeople 在一个类中，ChineseGreeting和 EnglishGreeting 在另外的类中。现在你已经对委托有了初步了解，是时候对上面的例子做个改进了。假设我们将GreetingPeople()放在一个叫GreetingManager的类中，那么新程序应该是这个样子的：

namespace Delegate {  
 //定义委托，它定义了可以代表的方法的类型  
 public delegate void GreetingDelegate(string name);  
   
 //新建的GreetingManager类  
 public class GreetingManager{  
 public void GreetPeople(string name, GreetingDelegate MakeGreeting) {  
 MakeGreeting(name);  
 }  
 }

 class Program {  
 private static void EnglishGreeting(string name) {  
 Console.WriteLine(“Morning, ” + name);  
 }

 private static void ChineseGreeting(string name) {  
 Console.WriteLine(“早上好, ” + name);  
 }

 static void Main(string\[\] args) {  
 // … …  
 }  
 }  
}

这个时候，如果要实现前面演示的输出效果，Main方法我想应该是这样的：

static void Main(string\[\] args) {  
 GreetingManager gm = new GreetingManager();  
 gm.GreetPeople(“Jimmy Zhang”, EnglishGreeting);  
 gm.GreetPeople(“张子阳”, ChineseGreeting);  
}

我们运行这段代码，嗯，没有任何问题。程序一如预料地那样输出了：  
Morning, Jimmy Zhang  
早上好, 张子阳

现在，假设我们需要使用上一节学到的知识，将多个方法绑定到同一个委托变量，该如何做呢？让我们再次改写代码：

输出：  
Morning, Jimmy Zhang  
早上好, Jimmy Zhang

到了这里，我们不禁想到：面向对象设计，讲究的是对象的封装，既然可以声明委托类型的变量(在上例中是delegate1)，我们何不将这个变量封装到 GreetManager类中？在这个类的客户端中使用不是更方便么？于是，我们改写GreetManager类，像这样：

现在，我们可以这样使用这个委托变量：

尽管这样达到了我们要的效果，但是似乎并不美气，光是第一个方法注册用“=”，第二个用“+=”就让人觉得别扭。此时，轮到Event出场了，C# 中可以使用事件来专门完成这项工作，我们改写GreetingManager类，它变成了这个样子：

很容易注意到：MakeGreet 事件的声明与之前委托变量delegate1的声明唯一的区别是多了一个event关键字。看到这里，你差不多明白到：**事件其实没什么不好理解的，声明一个事件不过类似于声明一个委托类型的变量而已**。

我们想当然地改写Main方法：

这次，你会得到编译错误：事件“Delegate.GreetingManager.MakeGreet”只能出现在 += 或 -= 的左边(从类型“Delegate.GreetingManager”中使用时除外)。

static void Main(string\[\] args) {  
 GreetingManager gm = new GreetingManager();  
 gm.MakeGreet = EnglishGreeting; // 编译错误1  
 gm.MakeGreet += ChineseGreeting;

 gm.GreetPeople(“Jimmy Zhang”, gm.MakeGreet); //编译错误2  
}

public class GreetingManager{  
 //这一次我们在这里声明一个事件  
 public event GreetingDelegate MakeGreet;

public void GreetPeople(string name, GreetingDelegate MakeGreeting) {  
 MakeGreeting(name);  
 }  
}

static void Main(string\[\] args) {  
 GreetingManager gm = new GreetingManager();  
 gm.delegate1 = EnglishGreeting;  
 gm.delegate1 += ChineseGreeting;

 gm.GreetPeople(“Jimmy Zhang”, gm.delegate1);  
}

public class GreetingManager{  
//在GreetingManager类的内部声明delegate1变量  
 public GreetingDelegate delegate1;

public void GreetPeople(string name, GreetingDelegate MakeGreeting) {  
 MakeGreeting(name);  
 }  
}

static void Main(string\[\] args) {  
 GreetingManager gm = new GreetingManager();  
 GreetingDelegate delegate1;  
 delegate1 = EnglishGreeting;  
 delegate1 += ChineseGreeting;

 gm.GreetPeople(“Jimmy Zhang”, delegate1);  
}