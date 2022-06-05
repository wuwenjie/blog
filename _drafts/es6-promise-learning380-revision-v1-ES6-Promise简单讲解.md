---
id: 381
title: 'ES6 Promise简单讲解'
date: '2019-08-17T12:52:27+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/380-revision-v1/'
permalink: '/?p=381'
---

Promises are a simple concept, and even if you haven’t had a chance to use them, you may have already read up on them. They are a valuable construct that enables asynchronous code to be structured in a more readable fasion, rather than as a mess of nested anonymous functions. This article touches on six things that you might not know about promises.

Before diving into the list, here is a quick reminder of what JavaScript promises look like:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var p = new Promise(function(resolve, reject) {
  resolve("hello world");
});

p.then(function(str) {
  alert(str);
});
```

## 1. `then()` Returns a Forked Promise

What is the difference between the following two blocks of code?

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// Exhibit A
var p = new Promise(/*...*/);
p.then(func1);
p.then(func2);
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// Exhibit B
var p = new Promise(/*...*/);
p.then(func1)
.then(func2);
```

If you think both code blocks are equivalent, you may be thinking that promises are nothing more than one-dimensional arrays of callbacks. However, that is not actually the case. Every call to `then()` returns a forked promise. So, in Exhibit A, if `func1()`throws an exception, `func2()` will still be called as normal.

In Exhibit B, if `func1()` throws an exception, `func2()` won’t be called, because the first call to `then()` returned a new promise, which was rejected due to the exception in `func1()`. The result is that `func2()` is skipped over.

The takeaway: promises can fork into multiple paths like a complex flow chart.

## 2. Callbacks Should Pass Results

What gets alerted when you run the following code?

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var p = new Promise(function(resolve, reject) {
  resolve("hello world");
});

p.then(function(str) {})
.then(function(str) {
  alert(str);
});
```

The alert in the second `then()` does not display anything. This is because callbacks, in the context of promises, aren’t so much callbacks as they are transformers of results. The promise expects your callback to either return the same result or a replacement, which then gets passed on to the next callback.

This idea is similar to using adapters to transform a result, as shown in the following example.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var feetToMetres = function(ft) { return ft*12*0.0254 };

var p = new Promise(/*...*/);

p.then(feetToMetres)
.then(function(metres) {
  alert(metres);
});
```

## 3. Only Exceptions From Previous Levels are Caught

What is the difference between these two code blocks:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// Exhibit A
new Promise(function(resolve, reject) {
  resolve("hello world");
})
.then(
  function(str) {
    throw new Error("uh oh");
  },
  undefined
)
.then(
  undefined,
  function(error) {
    alert(error);
  }
);
```

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// Exhibit B
new Promise(function(resolve, reject) {
  resolve("hello world");
})
.then(
  function(str) {
    throw new Error("uh oh");
  },
  function(error) {
    alert(error);
  }
);
```

In Exhibit A, when an exception is thrown in the first `then()`, it is caught in the second `then()` and “uh oh” is alerted. This follows the rule that only exceptions from previous levels are caught.

In Exhibit B, the callback and the error callback are at the same level, meaning when the exception is thrown in the callback, it won’t be caught. In fact, Exhibit B’s error callback will only execute if the promise is  
in a rejected state or if the promise itself throws an exception.

## 4. Errors Can Be Recovered From

Inside an error callback, if you don’t re-throw the error, the promise will assume that you’ve recovered from the error and will revert to the resolved state. In the following example, “I am saved” is displayed because the error callback in the first `then()` did not re-throw the exception.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var p = new Promise(function(resolve, reject) {
  reject(new Error("pebkac"));
});

p.then(
  undefined,
  function(error) { }
)
.then(
  function(str) {
    alert("I am saved!");
  },
  function(error) {
    alert("Bad computer!");
  }
);
```

Promises can be seen as layers on an onion. Each `then()` adds another layer to the onion. Each layer represents one activity that can be handled. After that layer is over, the result is assumed to be fixed and ready for the next layer.

## 5. Promises Can Be Paused

Just because you are already executing inside a `then()` function, doesn’t mean you can’t pause it to complete something else first. To pause the current promise, or to have it wait for the completion of another promise, simply return another promise from within `then()`.

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var p = new Promise(/*...*/);

p.then(function(str) {
  if(!loggedIn) {
    return new Promise(/*...*/);
  }
})
.then(function(str) {
  alert("Done.");
})
```

In the previous code sample, the alert will not be shown until the new promise has been resolved. This is a convenient way to introduce additional dependencies inside an existing asynchonous code path. For example, you may find that the user session has timed out and you may want to initiate a secondary login before continuing on with the previous code path.

## 6. Resolved Promises Don’t Execute Immediately

What gets alerted when you run the following code?

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">function runme() {
  var i = 0;

  new Promise(function(resolve) {
    resolve();
  })
  .then(function() {
    i += 2;
  });
  alert(i);
}
```

You may think it will alert 2, since the promise is resolved immediately and the `then()` function is executed immediately (synchronously). However, the promise specification requires all calls to be forcefully asynchronous in an effort to be uniform. Therefore, the alert is called before the value of `i` is modified.

Links:  
Download various implementations of the [Promise/A+ API](https://github.com/promises-aplus/promises-spec/blob/master/implementations.md).