---
id: 670
title: '防止你的GraphQL API被恶意查询'
date: '2019-04-22T16:11:07+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=670'
permalink: /2019/04/securing-your-graphql-api-from-malicious-queries
categories:
    - Javascript
---

原英文： <https://blog.apollographql.com/securing-your-graphql-api-from-malicious-queries-16130a324a6b>

Max Stoiber是Spectrum的在线社区实时聊天平台的首席技术官。 在这篇文章中，他描述了他们是如何在攻击中保护GraphQL API。

使用GraphQL，你可以随时查询想要的内容。 这对于使用API来说是惊人的，但也具有复杂的安全隐患。 恶意攻击者可能会提交耗时的嵌套查询来超载你的服务器，数据库，网络或所有这些，而不是要求提供合法有用的数据。 如果没有正确的保护措施，你就会面临DoS（拒绝服务）攻击。

例如，在Spectrum的GraphQL API中，我们有这样的关系：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">type Thread {
  messages(first: Int, after: String): [Message]
}

type Message {
  thread: Thread
}

type Query {
  thread(id: ID!): Thread
}
```

如你所见，你可以查询主题的消息或消息的主题。 这个循环关系会让不怀好意的人构建一个费时的嵌套查询，如下所示：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">query maliciousQuery {
  thread(id: "some-id") {
    messages(first: 99999) {
      thread {
        messages(first: 99999) {
          thread {
            messages(first: 99999) {
              thread {
                # ...repeat times 10000...
              }
            }
          }
        }
      }
    }
  }
}
```

让这种查询通过是非常糟糕，因为它会以指数方式增加所加载对象的数量，并且会使整个服务器崩溃。 虽然在其他应用层有一些缓解措施使在开始发送查询变得困难（如CORS），但它们无法完全防止发生。

### 大小限制

我们考虑的第一种天真的方法是通过原始字节来限制传入查询的大小。 由于查询是以字符串形式发送的，因此快速检查就足够了：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">app.use('*', (req, res, next) => {
  const query = req.query.query || req.body.query || '';
  if (query.length > 2000) {
    throw new Error('Query too large');
  }
  next();
});
```

不幸的是，在实践中这样做效果并不佳：该检查可能会允许使用短字段名称进行讨厌的查询，或者使用长字段名称或嵌套片段来防止合法查询。

### 查询白名单

我们考虑的第二种方法是在我们自己的应用程序中使用已批准查询的白名单，告诉服务器除了名单里的查询外，禁止任何其他的查询。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">app.use('/api', graphqlServer((req, res) => {
  const query = req.query.query || req.body.query;
  // TODO: Get whitelist somehow
  if (!whitelist[query]) {
    throw new Error('Query is not in whitelist.');
  }
  /* ... */
}));
```

手动维护已批准查询的列表显然是一件痛苦的事情，但幸运的是，Apollo团队创建了[persistgraphql](https://github.com/apollographql/persistgraphql)，它会自动从你的客户端代码中提取所有查询，并生成一个不错的JSON文件。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{
  "scripts": {
    "postbuild": "persistgraphql src api/query-whitelist.json"
  }
}
```

这项技术可以很好地工作，并可靠地阻止所有恶意查询。 不幸的是，它还有两个主要的折衷：

1、**我们永远不能更改或删除查询，只能添加新的查询**：如果任何用户运行过时的客户端，我们不能阻止他们的请求。 我们可能不得不保留所有在生产中使用的查询的历史，这是非常复杂的。

2、**我们无法向公众开放我们的API**：在未来的某个时候，我们希望向公众开放我们的API，以便其他开发人员可以构建他们对Spectrum界面外观的呈现。 如果我们只通过查询白名单，已经严重限制了他们的选择，并且破坏了拥有GraphQL API的重要性。

那些限制是我们无法使用的，所以我们得重新设计。

### 深度限制

上述恶意查询的一个有害方面是嵌套，按其深度分类，这使得查询成倍增加。 每层都会给后端增加更多的工作，当与列表结合使用时，增加的更快。

我们查找了一下，发现了[graphql-depth-limit](https://github.com/stems/graphql-depth-limit)，这是Andrew Carlson写的一个可爱模块，它使我们能够轻松限制传入查询的最大深度。 我们检查了我们的客户端，我们使用的最深的查询有7个级别，所以我们使用了（相当宽松的）最大深度为10的值，并将其添加到我们的验证规则中：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">app.use('/api', graphqlServer({
  validationRules: [depthLimit(10)]
}));
```

这就是简单的深度限制！

### 数量限制

上述查询的第二个有害方面是获取99999个对象。 无论这个对象是什么，取一吨它总是很耗时的。 （尽管DataLoader可能会缓解数据库压力，但网络和处理压力不会）

我们没有将第一个参数的类型设置为Int（允许任意数量），而是使用[graphql-input-number](https://github.com/joonhocho/graphql-input-number)创建了一个自定义标量，该标量将最大值限制为100：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const PaginationAmount = GraphQLInputInt({
  name: 'PaginationAmount',
  min: 1,
  max: 100,
});
```

如果任何人查询超过100个对象，这将抛出错误。 然后，我们可以在任何有连接的地方使用该API：

现在我们完全阻止了上面的恶意查询！

### 查询成本分析

不幸的是，在正确的条件下仍然有可能压倒服务器：有一些特定于应用程序的查询既不太深也不要求太多的对象，但仍然非常耗时。 对于我们Spectrum来说，这样的查询可能如下所示：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">query evilQuery {
  thread(id: "54887141-57a9-4386-807c-ed950c4d5132") {
    messageConnection(first: 100) { ... }
    participants(first: 100) {
      threadConnection(first: 100) { ... }
      communityConnection { ... }
      channelConnection { ... }
      everything(first: 100) { ... }
    }
  }
}
```

在这个查询中，深度和个别金额都不是特别高，所以它会通过我们当前的保护。 然而，它可能会提取数以万计的记录，这意味着它在数据库，服务器和网络上是最为严重的情况，这是最糟糕的情况。

为了防止这种情况，我们需要分析查询，然后再运行它们来计算它们的复杂性，如果它们太耗时，则会阻止它。虽然这比我们以前的两项保护措施都要做得更好，但它可以确保没有恶意查询可以到达我们的解决方案。

**在你要花大量时间实施查询成本分析之前，请确定你的确需要它。** 尝试使用糟糕的查询来崩溃或放慢API，来检测它的承受能力 – 也许你的API是没有这些嵌套关系，或者它可以处理一次获取数千条记录，而且没有问题，这是不需要做查询成本分析的！

我在最新发布的2017年MacBook Pro上本地运行了上述查询，并且我们的API服务器花费了10-15秒的时间来响应1M字节的JSON。 我们真的需要它，因为我们永远不希望有人用我们的API来轰炸我们的API。 （[The GitHub GraphQL API also uses Query Cost Analysis](https://developer.github.com/v4/guides/resource-limitations/)）

#### **实施查询成本分析**

在npm上有几个包来实现查询成本分析。 前两名是[graphql-validation-complexity](https://github.com/4Catalyzer/graphql-validation-complexity)，一个即插即用模块，或[graphql-cost-analysis](https://github.com/pa-bru/graphql-cost-analysis)，它可以让你指定@cost指令，从而获得更多的控制。 还有[graphql-query-complexity](https://github.com/ivome/graphql-query-complexity)，但与graphql-cost-analysis相比，我是不推荐选择它的，因为它是没有指令或乘法支持。

我们使用graphql-cost-analysis进行分析，因为我们最快的解析器（20μs）和最慢的解析器（10s +）之间存在很大差异，所以我们需要从中获得控制。 也就是说，也许graphql-validation-complexity对你来说已经足够了，试试吧！

它的工作方式是指定解析特定字段或类型的相对成本。 它也有乘法支持，所以如果你要求列表中的任何嵌套字段将乘以分页数量，这是非常整洁。

这就是@cost指令在实践中的样子：

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">type Participant {
  # The complexity of getting one thread in a thread connection is 3, and multiply that by the amount of threads fetched
  threadConnection(first: PaginationAmount, after: String): ThreadConnection @cost(complexity: 3, multipliers: ["first"])
}

type Thread {
  author: Author @cost(complexity: 1)
  participants(first: PaginationAmount,...): [Participant] @cost(complexity: 2, multipliers: ["first"])
}
```

这只是我们API类型的代码片段，但你应该明白了。 你可以指定某个字段的复杂程度，乘以哪个参数以及最大成本，而graphql-cost-analysis会为你完成其余的工作。

我通过[Apollo Engine](https://www.apollographql.com/engine)公开的性能跟踪数据确定了某些解析器的复杂程度。 我看了整个模式，并根据p99服务时间分配了一个值。 然后，我们查看了我们客户的所有查询，找出最耗时的一个，其中有500点复杂度。 为了给我们一点未来的余地，我们将最大复杂度设置为750。

运行上面的evilQuery，现在我们添加了graphql-cost-analysis，我收到一条错误消息，告诉我“GraphQL查询超过最大复杂度，请删除一些嵌套或字段，然后重试。 （最大750，实际1010319）“

**100万点复杂度？ 拒绝！**

### **总结**

总而言之，我建议使用深度和数量限制作为任何GraphQL API的最低保护 – 它们很容易实现，并且会提供足够的安全性。 根据您的特定安全要求和架构，您可能还需要做查询成本分析。 虽然它比其他工具的工作多一点，但它确实提供了针对恶意行为者的全面覆盖。