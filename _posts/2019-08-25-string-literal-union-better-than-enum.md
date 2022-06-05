---
id: 764
title: Typescript使用字符串联合类型代替枚举类型
date: '2019-08-25T23:39:30+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=764'
permalink: /2019/08/string-literal-union-better-than-enum
categories:
    - Javascript
---

## TypeScript宗旨

我觉得Typescript的宗旨是 任何一个 TypeScript 程序，在手动删去类型部分，将后缀改成 .js 后，都应能够正常运行。Typescript是javascript的超集，是编译期行为，不引入额外开销，不改变运行时行为，始终与 ESMAScript 语言标准一致。

但是enum类型了引入了 JavaScript 没有的数据结构（编译成一个双向 map），入侵了运行时，与 TypeScript 宗旨不符。用 字符串联合类型(‘enum1’ | ‘enum2’ | ‘enum3’)可以做到相同的事，且在调试时可读性更好。

以下 代码展示了enum违背了宗旨的证据：

## Enum实现

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// TypeScript
enum Test {
    enum1 = 2,
    enum2,
    enum3
}
const test:Test = Test.enum2; // 3
 
// 编译为javscript
var Test;
(function (Test) {
    Test[Test["enum1"] = 2] = "enum1";
    Test[Test["enum2"] = 3] = "enum2";
    Test[Test["enum3"] = 4] = "enum3";
})(Test || (Test = {}));
var test = Test.enum2; // 3
```

## 推荐联合类型（string literal union）实现

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// Typescript
type Test = 'enum1' | 'enum2' | 'enum3';
const test: Test = 'enum2';

// 编译为javscript，非常简单
var test = 'enum2';
```