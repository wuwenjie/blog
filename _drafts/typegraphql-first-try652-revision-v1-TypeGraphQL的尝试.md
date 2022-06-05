---
id: 653
title: TypeGraphQL的尝试
date: '2019-08-18T23:23:25+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/2019/08/652-revision-v1/'
permalink: '/?p=653'
---

## 前言

GraphQL 在我们之前的项目中的使用情况非常不错，后端可以只需要专注于合理的 Schema 设计与开发，并不需要太关心界面上的功能交互，在前端我们用 Apollo GraphQL 替代了 Redux 结合 React 也获得了很好的开发体验 ([还在用 Redux，要不要试试 GraphQL 和 Apollo？](https://zhuanlan.zhihu.com/p/34238617)) 。

我们在准备使用 TypeScript 来写 GraphQL 的时候，我们会有面临一个最大的问题

**GraphQL Schema Type DSL 和数据 Modal 需要写两份么？**

TypeGraphQL ( [type-graphql](https://link.zhihu.com/?target=https%3A//github.com/19majkel94/type-graphql) ) 是我们今天介绍的重点，它通过一些 decorator 帮我们解决了这个问题。下面我会先介绍如何构建一个基于 egg.js 的 TypeScript + GraphQL 工程，然后会介绍一些 TypeGraphQL 常见用法。

## 构建

## 初始化工程

egg.js 对 TypeScript 现在已经有了比较好的支持 ([参考](https://link.zhihu.com/?target=https%3A//eggjs.org/zh-cn/tutorials/typescript.html))，下面我们先创建一个基于 TypeScript 的 egg.js 工程。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">npx egg-init --type=ts type-graphql-demo
cd type-graphql-demo
yarn && yarn dev
```

通过 egg.js 提供的脚手架生成后，可以得到下面的一个工程目录结构

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">├── app
│   ├── controller
│   │   └── home.ts
│   ├── service
│   │   └── news.ts
│   └── router.ts
├── config
│   ├── config.default.ts
│   ├── config.local.ts
│   ├── config.prod.ts
│   └── plugin.ts
├── test
│   └── **/*.test.ts
├── typings
│   └── **/*.d.ts
├── README.md
├── package.json
├── tsconfig.json
└── tslint.json
```

## 安装依赖

1. 安装依赖

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">yarn add type-graphql 
```

2\. 安装 reflect-metadata

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">yarn add reflect-metadata
```

3\. reflect-metadata 需要在入口或者使用 type-graphql 之前被引入，建议在 app.ts 中引入

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// ~/app.ts    
import "reflect-metadata";
```

4\. 安装 apollo-server-koa , 处理请求路由( egg.js 是基于 koa )

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">yarn add apollo-server-koa
```

## 集成中间件路由

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">// ~/app/graphql/index.ts
import * as path from "path";

import { ApolloServer } from "apollo-server-koa";
import { Application } from "egg";
import { GraphQLSchema } from "graphql";
import { buildSchema } from "type-graphql";

export interface GraphQLConfig {
  router: string;
  graphiql: boolean;
}

export default class GraphQL {
  private readonly app: Application;
  private graphqlSchema: GraphQLSchema;
  private config: GraphQLConfig;

  constructor(app: Application) {
    this.app = app;
    this.config = app.config.graphql;
  }

  getResolvers() {
    const isLocal = this.app.env === "local";
    return [path.resolve(this.app.baseDir, `app/graphql/schema/**/*.${isLocal ? "ts" : "js"}`)];
  }

  async init() {
    this.graphqlSchema = await buildSchema({
      resolvers: this.getResolvers(),
      dateScalarMode: "timestamp"
    });
    const server = new ApolloServer({
      schema: this.graphqlSchema,
      tracing: false,
      context: ({ ctx }) => ctx,   // 将 egg 的 context 作为 Resolver 传递的上下文
      playground: {
        settings: {
          "request.credentials": "include"
        }
      } as any,
      introspection: true
    });
    server.applyMiddleware({
      app: this.app,
      path: this.config.router,
      cors: false
    });
    this.app.logger.info("graphql server init");
  }

  // async query({query, var})

  get schema(): GraphQLSchema {
    return this.graphqlSchema;
  }
}
```

~/app/extend/application.ts

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import { Application } from "egg";
import GraphQL from "../graphql";

const TYPE_GRAPHQL_SYMBOL = Symbol("Application#TypeGraphql");

export default {
  get graphql(this: Application): GraphQL {
    if (!this[TYPE_GRAPHQL_SYMBOL]) {
      this[TYPE_GRAPHQL_SYMBOL] = new GraphQL(this);
    }
    return this[TYPE_GRAPHQL_SYMBOL];
  }
};
```

~/app.ts

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import "reflect-metadata";
import { Application } from "egg";

export default async (app: Application) => {
  await app.graphql.init();
  app.logger.info("started");
}
```

## 使用 TypeGraphQL 创建 Schema

下面简单介绍一下 TypeGraphQL 的一些基本的用法。[详细文档链接](https://link.zhihu.com/?target=https%3A//19majkel94.github.io/type-graphql/docs/introduction.html)

## 定义 Schema

TypeGraphQL 提供了一些 decorator 来帮助我们通过 class 类来声明 graphql DSL。

## ObjectType &amp; InputType

- **@ObjectType** 创建 GraphQLObjectType
- **@InputType** 创建 GraphQLInputType
- **@Field** 声明对象的哪些字段作为 GraphQL 的字段，复杂类型的字段需要通过 `type => Rate`声明

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@ObjectType({ description: "The recipe model" })
class Recipe {
  @Field(type => ID)
  id: string;

  @Field({ description: "The title of the recipe" })
  title: string;

  @Field(type => [Rate])
  ratings: Rate[];

  @Field({ nullable: true })
  averageRating?: number;
}
@InputType({ description: "New recipe data" })
class AddRecipeInput implements Partial<Recipe> {
  @Field()
  title: string;

  @Field({ nullable: true })
  description?: string;
}
```

## 接口与继承

TypeScript 的接口只是在编译时存在，所以对于 GraphQL 的 interface，我们需要借助于抽象类来声明。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">abstract class IPerson {
  @Field(type => ID)
  id: string;

  @Field()
  name: string;

  @Field(type => Int)
  age: number;
}
@ObjectType({ implements: IPerson })
class Person implements IPerson {
  id: string;
  name: string;
  age: number;
}
```

对于继承，子类和父类必须有相同的 ObjectType 或者 InputType。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@ObjectType()
class Person {
  @Field()
  age: number;
}

@ObjectType()
class Student extends Person {
  @Field()
  universityName: string;
}
```

**⚠️注意：**在使用继承后，Resolver 里面返回 Plain Object 作为结果的时候会报错，这个 Bug ([\#160](https://link.zhihu.com/?target=https%3A//github.com/19majkel94/type-graphql/issues/160)) 还未修复。

## Resolvers

对于 Resolver 的处理，TypeGraphQL 提供了一些列的 decorator 来声明和处理数据。通过 Resolver 类的方法来声明 Query 和 Mutation，以及动态字段的处理 FieldResolver。

- **@Resolver**：来声明当前类是数据处理的
- **@Query**：声明改方法是一个 Query 查询操作
- **@Mutation**：声明改方法是一个 Mutation 修改操作
- **@FieldResovler**：对 `@Resolver(of => Recipe)` 返回的对象添加一个字段处理

方法参数：

- **@Root**：获取当前查询对象
- **@Ctx**：获取当前上下文，这里可以拿到 egg 的 Context （见上面中间件集成中的处理）
- **@Arg**：定义 input 参数

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@Resolver(of => Recipe)
class RecipeResolver {
  // ...
  @Query(returns => [Recipe])
  async recipes(
    @Arg("title", { nullable: true }) title?: string,
    @Arg("servings", { defaultValue: 2 }) servings: number,
  ): Promise<Recipe[]> {
    // ...
  }

  @FieldResolver()
  averageRating(@Root() recipe: Recipe, @Ctx() ctx: Context) {
    // handle with egg context
  }

  @Mutation()
  addRecipe(
    @Arg("data") newRecipeData: AddRecipeInput,
    @Ctx() ctx: Context,
  ): Recipe {
    // handle with egg context
  }
}
```

## Scalars &amp; Enums &amp; Unions

GraphQL 的其他特性比如 scalar、enum、union、subscriptions 等，TypeGraphQL 都做了很好的支持，在使用 TypeScript 编写的时候更加方便。

## Scalars

默认提供了 3 个基本类型的别名

- Int –&gt; GraphQLInt;
- Float –&gt; GraphQLFloat;
- ID –&gt; GraphQLID;

默认提供了日期类型 `Date` 的 scalar 处理，它支持两种时间格式：

- timestamp based (`"timestamp"`) – `1518037458374`
- ISO format (`"isoDate"`) – `"2018-02-07T21:04:39.573Z"`

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import { buildSchema } from "type-graphql";

const schema = await buildSchema({
  resolvers,
  dateScalarMode: "timestamp", // "timestamp" or "isoDate"
});
```

## Enums

TypeScript 支持 enum 类型，可以和 GraphQL 的 enum 进行复用。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT",
}
import { registerEnumType } from "type-graphql";

registerEnumType(Direction, {
  name: "Direction", // this one is mandatory
  description: "The basic directions", // this one is optional
});
```

## Unions

TypeGraphQL 提供了 `createUnionType` 方法来创建一个 union 类型。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@ObjectType()
class Movie {
  ...fields
}

@ObjectType()
class Actor {
  ...fields
}

import { createUnionType } from "type-graphql";

const SearchResultUnion = createUnionType({
  name: "SearchResult",  // the name of the GraphQL union
  types: [Movie, Actor], // array of object types classes
});
```

## 其他新特性

## Authorization

TypeGraphQL 默认提供了拦截器 `AuthChecker` 和注解 `@Authorized()` 来进行权限校验。如果校验不通过，则会返回 null 或者报错，取决于当前字段或者操作是否支持 nullable。

实现一个 `AuthChecker` :

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">export const customAuthChecker: AuthChecker<ContextType> = 
  ({ root, args, context, info }, roles) => {
    // here you can read user from context
    // and check his permission in db against `roles` argument
    // that comes from `@Authorized`, eg. ["ADMIN", "MODERATOR"]

    return true; // or false if access denied
  }
```

配合 `@Authorized` 使用

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@ObjectType()
class MyObject {
  @Field()
  publicField: string;

  @Authorized()
  @Field()
  authorizedField: string;

  @Authorized("ADMIN")
  @Field()
  adminField: string;
}
```

## Validation

TypeGraphQL 默认集成了 `class-validator` 来做数据校验。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">import { MaxLength, Length } from "class-validator";

@InputType()
export class RecipeInput {
  @Field()
  @MaxLength(30)
  title: string;

  @Field({ nullable: true })
  @Length(30, 255)
  description?: string;
}
```

## Middlewares

TypeGraphQL 提供了类似于 `koa.js` 的中间件处理。

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">export const ResolveTime: MiddlewareFn = async ({ info }, next) => {
  const start = Date.now();
  await next();
  const resolveTime = Date.now() - start;
  console.log(`${info.parentType.name}.${info.fieldName} [${resolveTime} ms]`);
};
```

## Global middlewares

全局中间件会拦截所有的 query、mutation、subscription、field resolver。可以来做一些全局相关的事情，比如异常拦截，请求跟踪（数据量大小，深度控制）等

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">const schema = await buildSchema({
  resolvers: [RecipeResolver],
  globalMiddlewares: [ErrorInterceptor, ResolveTime],
});
```

## Attaching middlewares

配合 `@UseMiddleware()` 对单个字段做拦截

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">@Resolver()
export class RecipeResolver {
  @Query()
  @UseMiddleware(ResolveTime, LogAccess)
  randomValue(): number {
    return Math.random();
  }
}
```

## Query complexity

TypeGraphQL 默认提供了查询复杂度控制，来防止一些恶意或者无意的过度复杂查询消耗大量的服务端资源，比如数据库连接等。

详细使用方法参考[文档](https://link.zhihu.com/?target=https%3A//19majkel94.github.io/type-graphql/docs/complexity.html)

## 总结

虽然 TypeGraphQL 还没有正式 `1.0.release` ，但是目前的版本已经是 MVP (Minimum Viable Product)。我们在正式使用中目前也没有遇到大的问题，该项目目前也比较活跃，很多新的特性也在开发中，建议可以做一些尝试。