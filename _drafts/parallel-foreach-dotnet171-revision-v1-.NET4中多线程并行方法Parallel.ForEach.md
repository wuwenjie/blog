---
id: 172
title: .NET4中多线程并行方法Parallel.ForEach
date: '2019-08-15T18:51:03+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/171-revision-v1/'
permalink: '/?p=172'
---

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="csharp" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">namespace ForEachDemo
{
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Linq;
    using System.Diagnostics;

    class SimpleForEach
    {
        static void Main()
        {
            // A simple source for demonstration purposes. Modify this path as necessary.
            var watch = new Stopwatch();
            var list = Enumerable.Range(0, 10);

            //  Method signature: Parallel.ForEach(IEnumerable<TSource> source, Action<TSource> body)
            watch.Start();
            Parallel.ForEach(list, item =>
            {
                try
                {
                    var t = 2 / item;
                    Console.WriteLine("Processing {0} on thread {1}", item,
                                        Thread.CurrentThread.ManagedThreadId);
                }
                catch
                {

                }

            } //close lambda expression
                 ); //close method invocation

            watch.Stop();
            Console.WriteLine(watch.ElapsedMilliseconds);
            // Keep the console window open in debug mode.
            Console.WriteLine("Processing complete. Press any key to exit.");
            Console.ReadKey();
        }
    }
}
```