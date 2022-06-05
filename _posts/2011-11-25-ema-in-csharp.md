---
id: 248
title: 'C#实现EMA算法'
date: '2011-11-25T18:39:51+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=248'
permalink: /2011/11/ema-in-csharp
categories:
    - 'net&amp;c#'
---

EMA表示的是指数平滑移动平均，其函数的定义为Y=EMA(X,N) 则Y=\[2\*X+(N-1)\*Y’\]/(N+1), 其中Y’表示上一周期Y值。

求X的N日指数平滑移动平均，它真正的公式表达是：当日指数平均值=平滑系数\*（当日指数值-昨日指数平均值）+昨日指数平均值；平滑系数=2/（周期单位+1）

EMA引用函数在计算机上使用递归算法很容易实现，但不容易理解。以下，列举分析说明EMA函数。

X是变量，每天的X值都不同，从远到近地标记，它们分别记为X1，X2，X3，….，Xn

当N=1，则EMA(X，1)=［2\*X1+(1-1)\*Y’］/(1+1)=X1

当N=2，则EMA(X，2)=［2\*X2+(2-1)\*Y’］/(2+1)=(2/3)\*X2+(1/3)X1

当N=3，则EMA(X，3)=［2\*X3+(3-1)\*Y’］/(3+1)=［2\*X3+2\*((2/3)\*X2+(1/3)\*X1)］/4=(1/2)\*X3+(1/3)\*X2+(1/6)\*X1

当N=4，则EMA(X，4)=［2\*X4+(4-1)\*Y’］/(4+1)=2/5\*X4+3/5\*((1/2)\*X3+(1/3)\*X2+(1/6)\*X1)=2/5\*X4+3/10\*X3+1/5\*X2+1/10\*X1

当N=5，则EMA(X，5)= (1/3)\*X5+(4/15)\*X4+(3/15)\*X3+(2/15)\*X2+(1/15)\*X1

当N=6，则EMA(X，6)=(2/7)\*X6+(5/21)\*X5+(4/21)\*X4+(3/21)\*X3+(2/21)\*X2+(1/21)\*X1

当N=7，则EMA(X，7)=(2/8)\*X7+(6/28)\*X6+(5/28)\*X5+(4/28)\*X4+(3/28)\*X3+(2/28)\*X2+(1/28)\*X1

当N=8，则EMA(X，8)=(2/9)\*X8+(36/36)\*X36+(6/36)\*X6+(5/36)\*X5+(4/36)\*X4+(3/36)\*X3+(2/36)\*X2+(1/36)\*X1

当N=9，则EMA(X，9)=(2/10)\*X9+(8/45)\*X8+(45/45)\*X45+(6/45)\*X6+(5/45)\*X5+(4/45)\*X4+(3/45)\*X3+(2/45)\*X2+(1/45)\*X1

当N=10，则EMA(X，10)=(2/11)\*X10+(9/55)\*X9+(8/55)\*X8+(55/55)\*X55+(6/55)\*X6+(5/55)\*X5+(4/55)\*X4+(3/55)\*X3+(2/55)\*X2+(1/55)\*X1

当N=11，则EMA(X，11)=(2/12)\*X11+(10/66)\*X10+(9/66)\*X9+(8/66)\*X8+(7/66)\*X7+(6/66)\*X6+(5/66)\*X5+(4/66)\*X4+(3/66)\*X3+(2/66)\*X2+(1/66)\*X1

当N=12，则EMA(X，12)=(2/13)\*X12+(11/78)\*X11+(10/78)\*X10+(9/78)\*X9+(8/78)\*X8+(7/78)\*X7+(6/78)\*X6+(5/78)\*X5+(4/78)\*X4+(3/78)\*X3+(2/78)\*X2+(1/78)\*X1

当N=13，则EMA(X，13)=(2/14)\*X13+(12/91)\*X12+(11/91)\*X11+(10/91)\*X10+(9/91)\*X9+(8/91)\*X8+(7/91)\*X7+(6/91)\*X6+(5/91)\*X5+(4/91)\*X4+(3/91)\*X3+(2/91)\*X2+(1/91)\*X1

其他依次循环可得。

其中，系数分母是N\*（N+1）/2。而且任何时候系数之和恒为1。当X是常量，每天的X值都不变，则EMA(X,N)=MA(X,N)。

以上公式可以直接作为计算公式。

从以上的列举分析中，我们可以看到时间周期越近的X值它的权重越大，说明EMA函数对近期的X值加强了权重比，更能及时反映近期X值的波动情况。 所以EMA比MA更具参考价值，而EMA也不容易出现死叉和金叉，所以一旦出现要立即作出反映！对周线处理，EMA就更加稳定了。

像EMA它的定义是 Y=\[2\*X+(N-1)\*Y’\]/(N+1) Y’是上一个周期的计算值。这样的函数是一个递归函数，不断的调用前一个周期的值来参加计算的。我们为了加快计算的速度，没有采用递归的方式。

 /// &lt;summary&gt;   
 /// Contains calculation results for EMA indicator   
 /// &lt;/summary&gt;   
 public class EMAResult   
 {   
 public List&lt;double&gt; Values { get; set; }

 /// &lt;summary&gt;   
 /// Represents the index of input signal at which the indicator starts   
 /// &lt;/summary&gt;   
 public int StartIndexOffset { get; set; }   
 }

——————————————————————————————————————————-

 /// &lt;summary&gt;   
 /// Calculates Exponential Moving Average (EMA) indicator   
 /// &lt;/summary&gt;   
 /// &lt;param name=”input”&gt;Input signal&lt;/param&gt;   
 /// &lt;param name=”period”&gt;Number of periods&lt;/param&gt;   
 /// &lt;returns&gt;Object containing operation results&lt;/returns&gt;   
 public static EMAResult EMA(IEnumerable&lt;double&gt; input, int period)   
 {   
 var returnValues = new List&lt;double&gt;();

 double multiplier = (2.0 / (period + 1));   
 double initialSMA = input.Take(period).Average();

 returnValues.Add(initialSMA);

 var copyInputValues = input.ToList();

 for (int i = period; i &lt; copyInputValues.Count; i++)   
 {   
 var resultValue = (copyInputValues\[i\] – returnValues.Last()) \* multiplier + returnValues.Last();

 returnValues.Add(resultValue);   
 }

 var result = new EMAResult()   
 {   
 Values = returnValues,   
 StartIndexOffset = period – 1   
 };

 return result;   
 }