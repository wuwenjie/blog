---
id: 119
title: 'Speeding up AngularJS apps with simple optimizations'
date: '2019-08-15T17:22:16+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/118-revision-v1/'
permalink: '/?p=119'
---

AngularJS is a huge framework with that already has many performance enhancements built in, but they can’t solve all our problems. No matter how fast the framework, we can all create sluggish code through bad practices and not understanding key concepts that help it perform well. The following performance pointers are some of the things I’ve learned from developing Angular applications that will hopefully enable you to keep building fast applications.

The key concept behind these performance considerations is reducing the number of `$$watchers`inside Angular to improve the `$digest` cycle’s performance, something you’ll see and hear more of as you continue working with Angular. These are crucial to keeping our application state fast and responsive for the user. Each time a Model is updated, either through user input in the View, or via service input to the Controller, Angular runs something called a `$digest` cycle.

This cycle is an internal execution loop that runs through your entire application’s bindings and checks if any values have changed. If values *have* changed, Angular will also update any values in the Model to return to a clear internal state. When we create data-bindings with AngularJS, we’re creating more `$$watchers` and `$scope` Objects, which in turn will take longer to process on each`$digest`. As we scale our applications, we need to be mindful of how many scopes and bindings we create, as these all add up quickly – each one being checked per `$digest` loop.

Let’s walk through some quick and easy performance considerations for Angular alongside some code examples that help us reduce `$$watcher` footprint and understand the `$digest` cycle a bit better.

## One-time binding syntax `{{ ::value }}`

AngularJS dropped a really interesting feature recently in the beta version of 1.3.0: the ability to render data once and let it persist without being affected by future Model updates. This is fantastic news for developers highly concerned with performance! Before this update, we’d typically render a value in the DOM like so:

1. `<h1>{{ title }}</h1>`

With the new one-time binding syntax, we introduce a double-colon before our value:

1. `<h1>{{ ::title }}</h1>`

Angular processes the DOM as usual and once the value has been resolved it removes the particular property from it’s internal `$$watchers` list. What does this mean for performance? A lot! This is a fantastic addition to helping us fine tune our applications.

It’s known that Angular becomes slower with around 2,000 bindings due to the process behind dirty-checking. The less we can add to this limit the better, as bindings can add up without us really noticing it!

Using the single binding syntax is easy and most importantly fast. The syntax is clear and concise, and a real benefit to lowering the `$$watcher` overhead. The less work Angular has to do, the more responsive our applications will become.

## `$scope.$apply()` versus `$scope.$digest()`

At some stage in your Angular career, you’ll have stumbled across the `$scope.$apply()` method. It’s often misused in a hopeful, “$scope.$apply will solve my coding between plugins, I’m just going to leave it here” type scenario, which isn’t the best way to use an API. For this reason, it’s misunderstood, but it shouldn’t be as it’s actually quite simple.

`$scope.$apply` is designed for telling Angular that a Model change has occurred outside of its lifecycle. That’s it. We just call `$scope.$apply` to let Angular update itself with those new values. It’s particularly important to remember when to use it correctly, as it’s confused me in the past and thrown uncaught errors in my JavaScript. You’ll get an error thrown from Angular if you’re calling`$scope.$apply` in the “wrong” place, usually too high up the call stack.

We all use third party plugins, and often the ones we use have their own event system and make DOM updates without Angular knowing. That’s exactly where the `$scope.$apply` method comes in to help. After these updates occur, calling `$scope.$apply` kicks off the `$digest` loop again and Angular pulls in values that were updated outside of its core.

Here’s some pseudo code example usage to demonstrate the concept:

1. `$(elem).myPlugin({<br></br>`
2. `  onchange: function (newValue) {<br></br>`
3. `    // model changes outside of Angular<br></br>`
4. `    $(this).val(newValue);<br></br>`
5. `    // tell Angular values have changed and to update via $digest<br></br>`
6. `    $scope.$apply();<br></br>`
7. `  }<br></br>`
8. `});`

When `$scope.$apply()` is called, it kicks the entire application into the `$digest` loop and in turn runs `$rootScope.$digest()`. This is what actually kicks off the internal `$digest` cycle. This cycle processes all of the watchers of the `$scope` it was called from (and its children) until no more listeners can be fired. In simple terms, it traverses all scopes and bindings of your application seeing if things have changed. At first, this process is pretty rapid, but certainly slows over time as the application scales.

Instead of `$scope.$apply`, we could turn to `$scope.$digest`, which runs the exact same`$digest` loop, but is executed from the current `$scope` downwards through its children, a much less costly venture.

The only caveat to this approach is that if you’re dependent on two-way binding between Objects from the parent `$scope`, the parent `$scope` won’t be updated until the next `$rootScope` full`$digest` cycle. This is because the `$scope.$digest` only descends rather than covering our entire `$scope` tree. If you want to update parent `$scope` values, then unfortunately we can’t use this performance tip and may as well invoke the `$scope.$apply` to run the full loop.

## Avoid `ng-repeat` where possible

Onto one of the more challenging approaches: avoiding `ng-repeat` where we can and where it makes sense. We’ve so far learned that the internals of Angular are pretty intelligent, but we can optimize where we can to help keep it performing well. We’ve learned so far that bindings create a bigger `$digest` cycle, and it’d be a great idea for directives we create to potentially be statically rendered components that aren’t tied into Angular unless really needed.

The `ng-repeat` directive is most likely the worst offender for performance concerns, which means it can easily be abused. An `ng-repeat` likely deals with Arrays of `$scope` Objects and this hammers the `$digest` cycle’s performance.

For example, instead of rendering a global navigation using `ng-repeat`, we could create our own navigation using the `$interpolate` provider to render our template against an Object and convert it into DOM nodes.

Be mindful of how many bindings and scopes you’re creating inside templates that become a repeater. Do some quick math and avoid any potential bottlenecks. I often think a little harder about how I can reduce the amount of bindings and scopes before digging into actually writing the code.

## More DOM manipulation in Directives

Another offender that will increase `$$watcher` counts are the core Angular directives such as `ng-show` and `ng-hide`. Although these might not immediately increase watcher counts dramatically, they can easily stack up in the hundreds inside an `ng-repeat`.

An `ng-repeat` leads to an increasing amount of `$$watchers` which *may* only serve a tiny purpose but are constantly looped over by Angular – things such as `true` and `false` toggling to activate `ng-show` and `ng-hide`. We can aim to remove these where it might make sense to.

If you’re doing something like this, it’s time to reconsider:

1. `<div ng-show=”something”></div><br></br>`
2. `$scope.something = false;<br></br>`
3. `$scope.someMethod = function () {<br></br>`
4. `  $scope.something = true;<br></br>`
5. `};`

If you’re building a Directive and some of the logic doesn’t need to rely on a Model, don’t use Angular for it. This logic will live inside the `link` callback; under no circumstance write DOM manipulation logic in a Controller! There are plenty of Directives floating around that set a `$scope` value to “true” and back to “false” to show and hide content, when a built-in `.hide()` and `.show()` call would be best suited. Angular also provides us with Directives such as `ng-mouseenter`, these can be more costly too as they’re not *only* binding an event listener, they become a part of the `$digest` cycle adding to the application weight. Inside the `link` callback, we should advocate the use of`addEventListener` or jQuery’s “on” method.

1. `var menu = $element.find(‘ul’);<br></br>`
2. `menu.hide();<br></br>`
3. `$scope.someMethod = function () {<br></br>`
4. `  menu.show();<br></br>`
5. `};`

This gives us better separation with things we actually require from Angular and things we don’t. In the example above, we likely aren’t reliant on Model changes to show a menu as the menu is part of our Directive and can be toggled like normal DOM. Saving `$$watchers` saves us any performance bottlenecks later on!

## Limit DOM filters

Filters are really simple to use, we insert a pipe, the filter name and we’re done. However, Angular runs every single filter *twice* per `$digest` cycle once something has changed. This is some pretty heavy lifting. The first run is from the `$$watchers` detecting any changes, the second run is to see if there are further changes that need updated values.

Here’s an example of a DOM filter, these are the slowest type of filter, preprocessing our data would be much faster. If you can, avoid the inline filter syntax.

1. `{{ filter_expression | filter : expression : comparator }}`

Angular includes a `$filter` provider, which you can use to run filters in your JavaScript before parsing into the DOM. This will preprocess our data before sending it to the View, which avoids the step of parsing the DOM and understanding the inline filter syntax.

1. `$filter('filter')(array, expression, comparator);`

## Summing up

These Angular performance tips have helped me develop applications better, with more structure and more thinking behind the code before I get stuck in. It’s not premature optimization when the Angular team provides us with great new APIs to help our apps perform better: they listen, they build, they release. It’s up to us to keep pushing Angular’s reputation forward by building lightning-fast and responsive applications.

A few key points to take away:

- Before diving into code, consider how to best architect your application to avoid performance challenges that dirty-checking faces in large quantities of Objects.
- Be mindful of ng-repeats — how much data are you expecting back? How much weight is that going to add to Angular’s `$digest` cycle?
- Not everything *needs* to be “Angular.” In Directives there are many cases we need to work with pure DOM.
- Keep checking out the Angular project on GitHub, as there are often some great hidden features that can be found from upcoming releases. That’s how I came across the “bind once” functionality.
- The more `$$watchers` there are, the slower your application will be, and with some of the performance enhancements above, even their simplicity can make a huge difference

For anything else, check the [Angular documentation](https://docs.angularjs.org/api)!