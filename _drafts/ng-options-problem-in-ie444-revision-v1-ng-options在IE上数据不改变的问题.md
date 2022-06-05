---
id: 445
title: ng-options在IE上数据不改变的问题
date: '2019-08-17T17:20:10+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/444-revision-v1/'
permalink: '/?p=445'
---

最近遇见angularjs 在IE上当使用ng-options作为select的选项数据源，并且被套在ng-switch（ng-transclude）之类的，当angular上得ng-options数据源model改变后，在IE上并不渲染。

在一阵的测试和阅读相关文档后最后确认为：因为ng-switch（ng-transclude）是为了使其scope为原来的父scope，在父scope上生成了DOM后才克隆(cloneNode)到指定的指令位置。然而IE在对于select克隆的节点，不会主动去触发重绘，所以才有了上面的issue。

问题确定了，那我们所需要做的就是手动的去触发让IE对Select重绘，尝试了很多办法后最终确认有效的是：首先在options上用原生js去添加一个option，在马上移除掉这个option，所以解决方案如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="js" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">angular.module('ie.select', [])
 
.directive('ieSelectFix', [
 
    function () {
 
        return {
 
            restrict: 'A',
 
            require: 'select',
 
            link: function (scope, element, attributes) {
 
                var isIE = document.attachEvent;
 
                if (!isIE) return;
 
  
 
                var control = element[0];
 
                scope.$watch(attributes.ieSelectFix, function () {
 
                    //it should be use javascript way, not jquery way.
 
                    var option = document.createElement("option");
 
                    control.add(option, null);
 
                    control.remove(control.options.length - 1);
 
                });
 
            }
 
        }
 
    }
 
]);
```

使用方式如下：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><select ie-select-fix="options" ng-model="demos" class="form-control"
 
        ng-options="currOption.value as currOption.text for currOption in options">
 
    <option value="" default>Select</option>
 
</select>
```