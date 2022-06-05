---
id: 436
title: 'Angular ng-class的小细节'
date: '2019-08-17T17:03:53+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/435-revision-v1/'
permalink: '/?p=436'
---

 在前面Angularjs开发一些经验总结中我们说到在angular开发中angular controller never 包含DOM元素（html/css），在controller需要一个简单的POJO（plain object javascript object），与view完全的隔离（交互angularjs框架的职责。但在某些项目中看见controller涉及DOM的元素最多的是在controller scope上定义某变量，其值为class name，形如：

```
<pre class="wp-block-preformatted">function ctr($scope){
   $scope.test =“classname”;
}

<div class=”{{test}}”></div>
```

 这种方式完全没错，是angular提供的一种改变class的方式，但是在controller涉及了classname在我看来是乎总是那么诡异，我希望的是controller是一个干净的纯javascript意义的object。  
  
在angular中为我们提供了3种方案处理class：  
1：scope变量绑定，如上例。（不推荐使用）  
2：字符串数组形式。  
3：对象key/value处理。

我们继续其他两种解决方案：  
1字符串数组形式是针对class简单变化，具有排斥性的变化，true是什么class，false是什么class，其形如;

```
<pre class="wp-block-preformatted">function Ctr($scope) { 
    $scope.isActive = true;
}

<div ng-class="{true: 'active', false: 'inactive'}[isActive]">
</div>
```

其结果是2中组合，isActive表达式为true，则 active，负责inactive。

2对象key/value处理主要针对复杂的class混合，其形如：

```
<pre class="wp-block-preformatted">function Ctr($scope) { 

}

<div ng-class {'selected': isSelected, 'car': isCar}">
</div> 
```

当 isSelected = true 则增加selected class，  
当isCar=true,则增加car class，  
所以你结果可能是4种组合。

**个人推荐用2，3两种方式，不建议将class放入controller scope之上，scope需要保持纯洁行，scope上的只能是数据和行为。**