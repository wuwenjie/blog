---
id: 135
title: 具有依赖关系的并行操作执行
date: '2009-10-20T13:50:29+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=135'
permalink: /2009/10/relation-problems-in-charp
categories:
    - 'net&amp;c#'
---

今天看到看到一篇MSDN文章《[Parallelizing Operations With Dependencies](http://msdn.microsoft.com/en-us/magazine/dd569760.aspx)》,作者是微软Parallel Computing Platform团队的一个开发经理。文中提供出一种用于并行执行一组具有依赖关系的操作的解决方案，这不由得想起我在一年之前写的一个具有相同的功能的组件。于是翻箱倒柜找了出来，进行了一些加工，与大家分享一下。

**一、问题分析

我们知道，较之串行化的操作，并行计算将多个任务同时执行，从而充分利用了资源，提高了应用的整体性能。对于多个互不相干的操作，我们可以直接按照异步的方式执行就可以。但是，我们遇到的很多情况下是，部分操作之间具有相互依赖的关系，一个操作需要在其他依赖的操作执行完成后方可执行。 以下图为例，每一个圆圈代表要执行的操作，操作之间的肩头代表它们之间的依赖关系。

<figure class="wp-block-image">[![clip_image002](https://images.cnblogs.com/cnblogs_com/artech/WindowsLiveWriter/799856d3fd88_14A22/clip_image002_thumb.jpg)](http://images.cnblogs.com/cnblogs_com/artech/WindowsLiveWriter/799856d3fd88_14A22/clip_image002_2.jpg)</figure>我们需要一个组件，帮助我们完成这样的工作：将相应的操作和依赖关系直接添加到一个容器中，我们的组件能够自动分析操作之间的依赖关系，在执行的时候根据依赖编排执行顺序。

**二、采用并行操作执行器

使用我所提供的这样一个并行操作执行器（ParallelExecutor），可以帮我们解决这个问题。首先对操作本身进行抽象，用以下三个属性来描述一个并行计算场景中的操作：

- Operation ID: 操作的唯一标识，字符类型
- Action：操作具体执行的功能，使用Action代理表示
- Depedencies：依赖操作列表

在使用ParallelExecutor对操作进行并行执行之前，我们需要通过ParallelExecutor的两个AddOperation方法添加需要执行的操作。AddOperation定义如下。其中dependencies代表以来操作ID数组，返回值为当前创建的操作ID。

```
<pre class="wp-block-preformatted">   1: public class ParallelExecutor
```

```
<pre class="wp-block-preformatted">   2: {
```

```
<pre class="wp-block-preformatted">   3:  
```

```
<pre class="wp-block-preformatted">   4:     public string AddOperation(string id, Action action)
```

```
<pre class="wp-block-preformatted">   5:     {
```

```
<pre class="wp-block-preformatted">   6:         //省略实现
```

```
<pre class="wp-block-preformatted">   7:     }
```

```
<pre class="wp-block-preformatted">   8:  
```

```
<pre class="wp-block-preformatted">   9:     public string AddOperation(string id, Action action, string[] dependencies)
```

```
<pre class="wp-block-preformatted">  10:     {
```

```
<pre class="wp-block-preformatted">  11:         //省略实现
```

```
<pre class="wp-block-preformatted">  12:     }
```

```
<pre class="wp-block-preformatted">  13: }
```

```
<pre class="wp-block-preformatted">  14:  
```

对于上图中的操作的依赖结构，我们通过下面的代码将所有的操作添加到创建的ParallelExecutor之中并执行。在这里的具体实现的操作仅仅是打印出操作的ID，以便我们清楚地知道操作执行的先后顺序是否满足依赖关系：

```
<pre class="wp-block-preformatted">   1: static void Main(string[] args)
```

```
<pre class="wp-block-preformatted">   2: {
```

```
<pre class="wp-block-preformatted">   3:     Action<string> action = id=> {Console.WriteLine(id);}; 
```

```
<pre class="wp-block-preformatted">   4:  
```

```
<pre class="wp-block-preformatted">   5:     var executor = new ParallelExecutor();
```

```
<pre class="wp-block-preformatted">   6:     var a1 = executor.AddOperation("A1", () => action("A1"));
```

```
<pre class="wp-block-preformatted">   7:     var a2 = executor.AddOperation("A2", () => action("A2"));
```

```
<pre class="wp-block-preformatted">   8:     var a3 = executor.AddOperation("A3", () => action("A3")); 
```

```
<pre class="wp-block-preformatted">   9:  
```

```
<pre class="wp-block-preformatted">  10:     var b1 = executor.AddOperation("B1", () => action("B1"), new string[] { a1, a2 });
```

```
<pre class="wp-block-preformatted">  11:     var b2 = executor.AddOperation("B2", () => action("B2"), new string[] { a3 }); 
```

```
<pre class="wp-block-preformatted">  12:  
```

```
<pre class="wp-block-preformatted">  13:     var c1 = executor.AddOperation("C1", () => action("C1"), new string[] { b1,b2 });
```

```
<pre class="wp-block-preformatted">  14:     var c2 = executor.AddOperation("C2", () => action("C2")); 
```

```
<pre class="wp-block-preformatted">  15:  
```

```
<pre class="wp-block-preformatted">  16:     executor.Execute();
```

```
<pre class="wp-block-preformatted">  17: Console.Read();
```

```
<pre class="wp-block-preformatted">  18: }
```

```
<pre class="wp-block-preformatted">  19:  
```

由于是操作的并行执行，线程调度的不确定性使每次输出的结果各有不同。但是无论如何，需要满足上图中展现的依赖关系。下面是其中一种执行结果，可以看出这是合理的执行顺序。

```
<pre class="wp-block-preformatted">   1: A3
```

```
<pre class="wp-block-preformatted">   2: B2
```

```
<pre class="wp-block-preformatted">   3: A1
```

```
<pre class="wp-block-preformatted">   4: A2
```

```
<pre class="wp-block-preformatted">   5: C2
```

```
<pre class="wp-block-preformatted">   6: B1
```

```
<pre class="wp-block-preformatted">   7: C1
```

**三、操作是如何被执行的

实现这样的并行计算有很多种解决方案。不同的解决方案大都体现在对于单一的操作该如何执行上。在我们提供这个解决方案中，我按照这样的方案来执行任意一个操作：

**直接执行无依赖的操作

如果需要执行的操作并不依赖于任何一个操作（比如C2），那么我们直接运行就好了，这没有什么好说的。

**先执行依赖操作，通过注册事件的方式执行被依赖的操作

如果一个操作依赖于一组操作，在执行之前注册依赖操作的结束事件实现，被依赖操作的执行发生在某个一个依赖操作的Completed事件触发后。具体来讲，上图中C1具有两个以来操作B1和B2，在初始化时，C1上会有一个用于计算尚未执行的依赖操作的个数，并注册B1和B2得操作结束事件上面。当B1和B2执行结束后，会触发该事件。每次事件触发，C1上的计数器将会减1，如果计数器为0，则表明所有的依赖操作执行结束，则执行C1相应的操作。

**四、具体实现

现在我们来看看详细设计和具体实现。首先通过下面的类图看看涉及到的所有类型。其中Operation类型是最为重要的一个类型，它代表一个具体的操作。

<figure class="wp-block-image">[![clip_image004](https://images.cnblogs.com/cnblogs_com/artech/WindowsLiveWriter/799856d3fd88_14A22/clip_image004_thumb.jpg)](http://images.cnblogs.com/cnblogs_com/artech/WindowsLiveWriter/799856d3fd88_14A22/clip_image004_2.jpg)</figure>**操作的属性

一个操作具有如下属性：

- ID：String类型，操作的唯一标识
- Action：Action类型，操作具体是实现的功能
- Dependencies：Operation数组，依赖的操作
- Status：Operation枚举，操作当前的状态
- ExecutionContext：ExecutionContext类型，用于传递线程执行的上下文

```
<pre class="wp-block-preformatted">   1: public class Operation
```

```
<pre class="wp-block-preformatted">   2: {    
```

```
<pre class="wp-block-preformatted">   3:     //其他成员
```

```
<pre class="wp-block-preformatted">   4:     public string ID
```

```
<pre class="wp-block-preformatted">   5:     { get; private set; } 
```

```
<pre class="wp-block-preformatted">   6:  
```

```
<pre class="wp-block-preformatted">   7:     public Action Action
```

```
<pre class="wp-block-preformatted">   8:     { get; private set; } 
```

```
<pre class="wp-block-preformatted">   9:  
```

```
<pre class="wp-block-preformatted">  10:     public Operation[] Dependencies
```

```
<pre class="wp-block-preformatted">  11:     { get; private set; } 
```

```
<pre class="wp-block-preformatted">  12:  
```

```
<pre class="wp-block-preformatted">  13:     public OperationStatus Status
```

```
<pre class="wp-block-preformatted">  14:     { get; private set; } 
```

```
<pre class="wp-block-preformatted">  15:  
```

```
<pre class="wp-block-preformatted">  16:     public ExecutionContext ExecutionContext
```

```
<pre class="wp-block-preformatted">  17:     { get; private set; } 
```

```
<pre class="wp-block-preformatted">  18:  
```

```
<pre class="wp-block-preformatted">  19:     public Operation(string id, Action action)
```

```
<pre class="wp-block-preformatted">  20:     {
```

```
<pre class="wp-block-preformatted">  21:         if (string.IsNullOrEmpty(id))
```

```
<pre class="wp-block-preformatted">  22:         {
```

```
<pre class="wp-block-preformatted">  23:             throw new ArgumentNullException("id");
```

```
<pre class="wp-block-preformatted">  24:         } 
```

```
<pre class="wp-block-preformatted">  25:  
```

```
<pre class="wp-block-preformatted">  26:         if (null == action)
```

```
<pre class="wp-block-preformatted">  27:         {
```

```
<pre class="wp-block-preformatted">  28:             throw new ArgumentNullException("action");
```

```
<pre class="wp-block-preformatted">  29:         }
```

```
<pre class="wp-block-preformatted">  30:         this.Status = OperationStatus.Created;
```

```
<pre class="wp-block-preformatted">  31:         this.ID = id;
```

```
<pre class="wp-block-preformatted">  32:         this.Action = action;
```

```
<pre class="wp-block-preformatted">  33:         this.Dependencies = new Operation[0];
```

```
<pre class="wp-block-preformatted">  34:     } 
```

```
<pre class="wp-block-preformatted">  35:  
```

```
<pre class="wp-block-preformatted">  36:     public Operation(string id, Action action, Operation[] dependencies)
```

```
<pre class="wp-block-preformatted">  37:         : this(id, action)
```

```
<pre class="wp-block-preformatted">  38:     {
```

```
<pre class="wp-block-preformatted">  39:         if (null == dependencies)
```

```
<pre class="wp-block-preformatted">  40:         {
```

```
<pre class="wp-block-preformatted">  41:             throw new ArgumentNullException("dependencies");
```

```
<pre class="wp-block-preformatted">  42:         } 
```

```
<pre class="wp-block-preformatted">  43:  
```

```
<pre class="wp-block-preformatted">  44:         this.Dependencies = dependencies;
```

```
<pre class="wp-block-preformatted">  45:     }     
```

```
<pre class="wp-block-preformatted">  46: }
```

```
<pre class="wp-block-preformatted">  47:  
```

**操作事件**

当前操作执行的状态通过OperationStatus表示，四个枚举值分别表示被创建、正在运行、运行结束和失败（抛出异常）。

```
<pre class="wp-block-preformatted">   1: public enum OperationStatus
```

```
<pre class="wp-block-preformatted">   2: {
```

```
<pre class="wp-block-preformatted">   3:     Created,
```

```
<pre class="wp-block-preformatted">   4:     Running,
```

```
<pre class="wp-block-preformatted">   5:     Completed,
```

```
<pre class="wp-block-preformatted">   6: Failed
```

```
<pre class="wp-block-preformatted">   7: }
```

操作还具有三个时间，分别在开始执行、结束执行和执行失败时触发。这三个事件名称分别为OperationStarted、OperationCompleted和OperationFailed。

```
<pre class="wp-block-preformatted">   1: public class Operation
```

```
<pre class="wp-block-preformatted">   2: {
```

```
<pre class="wp-block-preformatted">   3:     //其他成员
```

```
<pre class="wp-block-preformatted">   4:     public event EventHandler<OperationEventArgs> OperationStarted;
```

```
<pre class="wp-block-preformatted">   5:     public event EventHandler<OperationFailedEventArgs> OperationFailed;
```

```
<pre class="wp-block-preformatted">   6:     public event EventHandler<OperationEventArgs> OperationCompleted;     
```

```
<pre class="wp-block-preformatted">   7: }
```

```
<pre class="wp-block-preformatted">   8:  
```

OperationStarted和OperationCompleted事件对应的参数类型为OperationEventArgs。OperationEventArgs直接继承自EventArgs，并定义了一个Operation属性代表对应的Operation对象。

```
<pre class="wp-block-preformatted">   1: public class OperationEventArgs : EventArgs
```

```
<pre class="wp-block-preformatted">   2: {
```

```
<pre class="wp-block-preformatted">   3:     public OperationEventArgs(Operation operation)
```

```
<pre class="wp-block-preformatted">   4:     {
```

```
<pre class="wp-block-preformatted">   5:         if (null == operation)
```

```
<pre class="wp-block-preformatted">   6:         {
```

```
<pre class="wp-block-preformatted">   7:             throw new ArgumentNullException("operation");
```

```
<pre class="wp-block-preformatted">   8:         }
```

```
<pre class="wp-block-preformatted">   9:  
```

```
<pre class="wp-block-preformatted">  10:         this.Operation = operation;
```

```
<pre class="wp-block-preformatted">  11:     }
```

```
<pre class="wp-block-preformatted">  12:  
```

```
<pre class="wp-block-preformatted">  13:     public Operation Operation
```

```
<pre class="wp-block-preformatted">  14:     { get; private set; }
```

```
<pre class="wp-block-preformatted">  15: }
```

```
<pre class="wp-block-preformatted">  16:  
```

OperationFailed的事件参数类型为OperationFailedEventArgs。继承自OperationEventArgs，在此基础上添加了一个Exception类型的Error属性，表示抛出的异常。

**操作初始化和事件注册

在第三节中已经谈到过了，被依赖操作的执行通过的依赖操作执行完成后触发OperationCompleted事件的是实现。事件注册必须在ParallelExecutor执行之前完成，在这里我定义了一个Initialize方法，在里面完成事件注册工作：

```
<pre class="wp-block-preformatted">   1: public class Operation
```

```
<pre class="wp-block-preformatted">   2: {
```

```
<pre class="wp-block-preformatted">   3:     //其他成员
```

```
<pre class="wp-block-preformatted">   4:     private int _remainingDependencies;
```

```
<pre class="wp-block-preformatted">   5:     private List<Operation> _registeredParentOps = new List<Operation>();     
```

```
<pre class="wp-block-preformatted">   6:  
```

```
<pre class="wp-block-preformatted">   7:     private static void RegisterCompletedEvents(Operation operation)
```

```
<pre class="wp-block-preformatted">   8:     {
```

```
<pre class="wp-block-preformatted">   9:         operation._remainingDependencies = operation.Dependencies.Length;
```

```
<pre class="wp-block-preformatted">  10:         foreach (var op in operation.Dependencies)
```

```
<pre class="wp-block-preformatted">  11:         {
```

```
<pre class="wp-block-preformatted">  12:             if (op._registeredParentOps.Contains(operation))
```

```
<pre class="wp-block-preformatted">  13:             {
```

```
<pre class="wp-block-preformatted">  14:                 continue;
```

```
<pre class="wp-block-preformatted">  15:             }
```

```
<pre class="wp-block-preformatted">  16:             RegisterCompletedEvents(op);
```

```
<pre class="wp-block-preformatted">  17:             op.OperationCompleted += (sender, args) =>
```

```
<pre class="wp-block-preformatted">  18:                 {
```

```
<pre class="wp-block-preformatted">  19:                     operation._remainingDependencies--;
```

```
<pre class="wp-block-preformatted">  20:                     if (operation._remainingDependencies <= 0)
```

```
<pre class="wp-block-preformatted">  21:                     {
```

```
<pre class="wp-block-preformatted">  22:                         operation.DoExecute();
```

```
<pre class="wp-block-preformatted">  23:                     }
```

```
<pre class="wp-block-preformatted">  24:                 };
```

```
<pre class="wp-block-preformatted">  25:             op._registeredParentOps.Add(operation);
```

```
<pre class="wp-block-preformatted">  26:         }            
```

```
<pre class="wp-block-preformatted">  27:     } 
```

```
<pre class="wp-block-preformatted">  28:  
```

```
<pre class="wp-block-preformatted">  29:     public void Initialize()
```

```
<pre class="wp-block-preformatted">  30:     {
```

```
<pre class="wp-block-preformatted">  31:         RegisterCompletedEvents(this);
```

```
<pre class="wp-block-preformatted">  32:    }
```

```
<pre class="wp-block-preformatted">  33: }
```

**操作执行

ParallelExecutor通过调用Operation的Execute方法执行相应的操作。在Execute方法中，如果是独立的操作，则执行执行，否则异步执行依赖操作，这是一个递归的过程。操作的具体实现定义在DoExecute方法中。

```
<pre class="wp-block-preformatted">   1: public class Operation
```

```
<pre class="wp-block-preformatted">   2: {   
```

```
<pre class="wp-block-preformatted">   3:     //其他成员
```

```
<pre class="wp-block-preformatted">   4:     private void DoExecute()
```

```
<pre class="wp-block-preformatted">   5:     {
```

```
<pre class="wp-block-preformatted">   6:         if (this.Status != OperationStatus.Created)
```

```
<pre class="wp-block-preformatted">   7:         {
```

```
<pre class="wp-block-preformatted">   8:             return;
```

```
<pre class="wp-block-preformatted">   9:         } 
```

```
<pre class="wp-block-preformatted">  10:  
```

```
<pre class="wp-block-preformatted">  11:         if (null != this.OperationStarted)
```

```
<pre class="wp-block-preformatted">  12:         {
```

```
<pre class="wp-block-preformatted">  13:             this.OperationStarted(this, new OperationEventArgs(this));
```

```
<pre class="wp-block-preformatted">  14:         } 
```

```
<pre class="wp-block-preformatted">  15:  
```

```
<pre class="wp-block-preformatted">  16:         this.Status = OperationStatus.Running;
```

```
<pre class="wp-block-preformatted">  17:         try
```

```
<pre class="wp-block-preformatted">  18:         {
```

```
<pre class="wp-block-preformatted">  19:             if (null != this.ExecutionContext)
```

```
<pre class="wp-block-preformatted">  20:             {
```

```
<pre class="wp-block-preformatted">  21:                 ExecutionContext.Run(this.ExecutionContext.CreateCopy(), state => this.Action(), null);
```

```
<pre class="wp-block-preformatted">  22:             }
```

```
<pre class="wp-block-preformatted">  23:             else
```

```
<pre class="wp-block-preformatted">  24:             {
```

```
<pre class="wp-block-preformatted">  25:                 this.Action();
```

```
<pre class="wp-block-preformatted">  26:             } 
```

```
<pre class="wp-block-preformatted">  27:  
```

```
<pre class="wp-block-preformatted">  28:             this.Status = OperationStatus.Completed;
```

```
<pre class="wp-block-preformatted">  29:             if (null != this.OperationCompleted)
```

```
<pre class="wp-block-preformatted">  30:             {
```

```
<pre class="wp-block-preformatted">  31:                 this.OperationCompleted(this, new OperationEventArgs(this));
```

```
<pre class="wp-block-preformatted">  32:             }
```

```
<pre class="wp-block-preformatted">  33:         }
```

```
<pre class="wp-block-preformatted">  34:         catch (Exception ex)
```

```
<pre class="wp-block-preformatted">  35:         {
```

```
<pre class="wp-block-preformatted">  36:             this.Status = OperationStatus.Failed;
```

```
<pre class="wp-block-preformatted">  37:             if (null != this.OperationFailed)
```

```
<pre class="wp-block-preformatted">  38:             {
```

```
<pre class="wp-block-preformatted">  39:                 this.OperationFailed(this, new OperationFailedEventArgs(this, ex));
```

```
<pre class="wp-block-preformatted">  40:             }
```

```
<pre class="wp-block-preformatted">  41:         }
```

```
<pre class="wp-block-preformatted">  42:     } 
```

```
<pre class="wp-block-preformatted">  43:  
```

```
<pre class="wp-block-preformatted">  44:     [MethodImplAttribute(MethodImplOptions.Synchronized)]
```

```
<pre class="wp-block-preformatted">  45:     public void Execute()
```

```
<pre class="wp-block-preformatted">  46:     {
```

```
<pre class="wp-block-preformatted">  47:         if (this.Dependencies.Length == 0)
```

```
<pre class="wp-block-preformatted">  48:         {
```

```
<pre class="wp-block-preformatted">  49:             this.DoExecute();
```

```
<pre class="wp-block-preformatted">  50:         }           
```

```
<pre class="wp-block-preformatted">  51:  
```

```
<pre class="wp-block-preformatted">  52:         foreach (var operation in this.Dependencies)
```

```
<pre class="wp-block-preformatted">  53:         {
```

```
<pre class="wp-block-preformatted">  54:             var op = operation;
```

```
<pre class="wp-block-preformatted">  55:             ThreadPool.UnsafeQueueUserWorkItem(state => op.Execute(), null);
```

```
<pre class="wp-block-preformatted">  56:         }        
```

```
<pre class="wp-block-preformatted">  57:  
```

```
<pre class="wp-block-preformatted">  58:     } 
```

```
<pre class="wp-block-preformatted">  59: }
```

```
<pre class="wp-block-preformatted">  60:  
```

**ParallelExecutor**

ParallelExecutor提供操作的添加和整体执行。添加操作实现在两个重载的AddOperation方法中，逻辑并不复杂。当执行Execute方法对所有的操作进行并行执行的时候，需要调用Initialize方法对每个操作进行初始化。然后异步调用每个操作的Execute方法即可。

```
<pre class="wp-block-preformatted">   1: public class ParallelExecutor
```

```
<pre class="wp-block-preformatted">   2: {
```

```
<pre class="wp-block-preformatted">   3:     public ParallelExecutor()
```

```
<pre class="wp-block-preformatted">   4:     {
```

```
<pre class="wp-block-preformatted">   5:         this.Operations = new Dictionary<string, Operation>();
```

```
<pre class="wp-block-preformatted">   6:     }
```

```
<pre class="wp-block-preformatted">   7:  
```

```
<pre class="wp-block-preformatted">   8:     public IDictionary<string, Operation> Operations
```

```
<pre class="wp-block-preformatted">   9:     { get; private set; }
```

```
<pre class="wp-block-preformatted">  10:  
```

```
<pre class="wp-block-preformatted">  11:     public void Execute()
```

```
<pre class="wp-block-preformatted">  12:     {
```

```
<pre class="wp-block-preformatted">  13:         foreach (var operation in this.Operations.Values)
```

```
<pre class="wp-block-preformatted">  14:         {
```

```
<pre class="wp-block-preformatted">  15:             operation.Initialize();
```

```
<pre class="wp-block-preformatted">  16:         }
```

```
<pre class="wp-block-preformatted">  17:  
```

```
<pre class="wp-block-preformatted">  18:         foreach (var operation in this.Operations.Values)
```

```
<pre class="wp-block-preformatted">  19:         {
```

```
<pre class="wp-block-preformatted">  20:             var op = operation;
```

```
<pre class="wp-block-preformatted">  21:             ThreadPool.UnsafeQueueUserWorkItem(state => op.Execute(), null);
```

```
<pre class="wp-block-preformatted">  22:         }
```

```
<pre class="wp-block-preformatted">  23:     }
```

```
<pre class="wp-block-preformatted">  24:  
```

```
<pre class="wp-block-preformatted">  25:     public string AddOperation(string id, Action action)
```

```
<pre class="wp-block-preformatted">  26:     {
```

```
<pre class="wp-block-preformatted">  27:         ValidateOperation(id, action);
```

```
<pre class="wp-block-preformatted">  28:         this.Operations.Add(id, new Operation(id, action));
```

```
<pre class="wp-block-preformatted">  29:         return id;
```

```
<pre class="wp-block-preformatted">  30:     }
```

```
<pre class="wp-block-preformatted">  31:  
```

```
<pre class="wp-block-preformatted">  32:     private void ValidateOperation(string id, Action action)
```

```
<pre class="wp-block-preformatted">  33:     {
```

```
<pre class="wp-block-preformatted">  34:         if (null == action)
```

```
<pre class="wp-block-preformatted">  35:         {
```

```
<pre class="wp-block-preformatted">  36:             throw new ArgumentNullException("action");
```

```
<pre class="wp-block-preformatted">  37:         }
```

```
<pre class="wp-block-preformatted">  38:  
```

```
<pre class="wp-block-preformatted">  39:         if (this.Operations.ContainsKey(id))
```

```
<pre class="wp-block-preformatted">  40:         {
```

```
<pre class="wp-block-preformatted">  41:             throw new ArgumentException(string.Format("There is an existing operation whose ID is \"{0}\"", id));
```

```
<pre class="wp-block-preformatted">  42:         }
```

```
<pre class="wp-block-preformatted">  43:     }
```

```
<pre class="wp-block-preformatted">  44:  
```

```
<pre class="wp-block-preformatted">  45:     public string AddOperation(string id, Action action, string[] dependencies)
```

```
<pre class="wp-block-preformatted">  46:     {
```

```
<pre class="wp-block-preformatted">  47:         ValidateOperation(id, action);
```

```
<pre class="wp-block-preformatted">  48:         if (null == dependencies)
```

```
<pre class="wp-block-preformatted">  49:         {
```

```
<pre class="wp-block-preformatted">  50:             throw new ArgumentNullException("dependencies");
```

```
<pre class="wp-block-preformatted">  51:         }
```

```
<pre class="wp-block-preformatted">  52:  
```

```
<pre class="wp-block-preformatted">  53:         foreach (var op in dependencies)
```

```
<pre class="wp-block-preformatted">  54:         {
```

```
<pre class="wp-block-preformatted">  55:             if (!this.Operations.ContainsKey(op))
```

```
<pre class="wp-block-preformatted">  56:             {
```

```
<pre class="wp-block-preformatted">  57:                 throw new ArgumentException(string.Format("The operation whose ID is \"{0}\" does not exist!", op));
```

```
<pre class="wp-block-preformatted">  58:             }
```

```
<pre class="wp-block-preformatted">  59:         }
```

```
<pre class="wp-block-preformatted">  60:  
```

```
<pre class="wp-block-preformatted">  61:         var operation = new Operation(id, action,
```

```
<pre class="wp-block-preformatted">  62:                this.Operations.Values.
```

```
<pre class="wp-block-preformatted">  63:                Where(op => Array.Exists<string>(dependencies, opId => opId == op.ID)).ToArray<Operation>());
```

```
<pre class="wp-block-preformatted">  64:  
```

```
<pre class="wp-block-preformatted">  65:         this.Operations.Add(id, operation);
```

```
<pre class="wp-block-preformatted">  66:         return id;
```

```
<pre class="wp-block-preformatted">  67:     }
```

```
<pre class="wp-block-preformatted">  68: }
```

```
<pre class="wp-block-preformatted">  69:  
```

- - - - - -