# 处理 GraphQL 中的错误

> 原文：<https://dev.to/andre/handling-errors-in-graphql--2ea3>

最近有一些关于如何处理 GraphQL 解析器中的错误的讨论。我提到[阿波罗失误](https://github.com/thebigredgeek/apollo-errors)，因为我有非常好的体验。在本文中，我想借此机会描述我在 GraphQL API 中处理错误的方法。

## 对错误的剖析

在深入探讨如何建立正确的错误处理之前，我想稍微区分一下我们在这里讨论的是什么类型的错误。基本上，在所有面向用户的系统中，有两种可能的错误类型:

*   `Controlled errors`:异常，表示用户做错了什么(如登录凭证错误等)。)
*   `Uncontrolled errors`:对方。表示发生了非常糟糕的事情(例如，存储系统不可用等)的异常。)

我们最感兴趣的是`controlled errors`。这些是你作为软件工程师定义的，当特殊情况发生时抛出的。`Uncontrolled errors`顾名思义，就是随时都有可能发生的。即使它们不是*可控的*，你也会学会如何优雅地处理它们。

## 共同的方法

在阅读有关 GraphQL 的内容时，您会经常看到下面的例子:

```
if (!areCredentialsValid) {
    throw new Error("Authentication required");
} 
```

这是一种简单的方法，在大多数情况下可能就足够了。不利的一面是，相应的客户端很难判断这实际上是哪种错误。客户端里的一个`err.message === "Authentication required"`一点都不酷。

如果有一种错误类型就太好了，对吗？这就是阿波罗失误的原因。我们走吧！

## 更稳健的方法

让我们考虑下面的场景:我们有一个登录突变，我们想在用户输入错误的凭证时抛出一个错误。一种可能的类型是`WrongCredentialsError`。要做的第一步是创建实际的错误类型:

```
// path: resolvers/mutation/login/errors/WrongCredentialsError.ts

import { createError } from "apollo-errors";

const WrongCredentialsError = createError("WrongCredentialsError", {
    message: "The provided credentials are invalid."
});

export { WrongCredentialsError } 
```

现在我们有了我们的错误类型，我们可以从我们的`login`突变解析器抛出它:

```
// path: resolvers/mutation/login/index.ts

import { WrongCredentialsError } from "./errors/WrongCredentialsError";

interface LoginInput {
  username: string;
  password: string;
}

const login = await (parent, args: LoginInput, context: Context, info) {
    const user = await context.db.query.user({where: {username: args.username}});

    const areCredentialsValid = checkCredentials(user, args.password);

    if (!areCredentialsValid) {
        throw new WrongCredentialsError();
    }
}; 
```

因此，当这种变异被执行并且用户输入了错误的凭证时，GraphQL API 将会响应:

```
{  "data":  {},  "errors":  [  {  "message":"The provided credentials are invalid.",  "name":"WrongCredentialsError",  "time_thrown":"2018-02-14T00:40:50.954Z",  }  ]  } 
```

很漂亮，不是吗？因此，理论上 GraphQL API 会对此做出响应。有一块拼图不见了。由于不同的错误结构，我们必须告诉 GraphQL API 端点，这些错误的格式应该不同。

但是不用担心，安装格式化程序是一次性的，很容易完成。下面描述了如何在基于 [graphql-yoga](https://github.com/graphcool/graphql-yoga) 的应用程序上连接格式化程序。

```
import { GraphQLServer, Options } from "graphql-yoga";
import { formatError } from "apollo-errors";

const options: Options = {
  formatError
};

const server = new GraphQLServer({ typeDefs, resolvers })
server.start(options, () => console.log('GraphQL API is running on localhost:4000')) 
```

就是这样！你现在可以投掷名为`controlled errors`的东西了。

## 优雅地处理不受控制的错误

正如上面承诺的，我提到给你一个优雅地处理`uncontrolled errors`的方法。你可能已经从代码片段中读到过，我使用 [Prisma](https://www.prismagraphql.com/) 与我的数据库进行交互。让我们假设我搞砸了一些事情(例如，向我的数据库发送了一个乏味的查询，等等。).在那些情况下，如果真的发生了不好的事情，我们希望通知客户端发生了`FatalError`。如何才能实现这一点？

一种方法是将每个 Prisma 交互放在一个`try / catch`中，并将`FatalError`放在那里。这是可行的，但是很麻烦，因为你必须在你所有的解析器中处理它。

另一种方法是将我们所有的解析器包装到一个包装器中，该包装器执行实际的解析器并检查该解析器是否没有抛出`uncontrolled error`。让我们称这个包装器为`helmet`。它可能看起来像:

```
// path: resolvers/helmet.ts

import { FatalError } from "./errors/FatalError";

const helmet = (resolver) => async (...args) => {
  try {
    //
    // Try to execute the actual resolver and return
    // the result immediately.
    //
    return await resolver(...args);
  } catch (err) {
    //
    // Due to the fact that we are using Prisma, we can assume
    // that each error from this layer has a `path` attribute.
    //
    // Note: The `FatalError` has been created before by
    // using `apollo-errors` `createError` function.
    //
    if (err.path) {
      throw new FatalError({ data: { reason: err.message } });
    } else {
      throw err;
    }
  }
};

export { helmet }; 
```

为了真正优雅地处理`uncontrolled errors`，您必须将解析器包装到`helmet`函数中。例如，我们在这里使用了上述的`login`突变:

```
// path: resolvers/mutation/index.ts

import { helmet } from "../helmet";

import { login } from "./login";

const Mutation = {
  login: helmet(login)
  // ...
};

export { Mutation }; 
```

## 结论

即使在某些情况下直接抛出一个`Error`对象可能是明智的，我还是建议考虑一种方法，就像这篇文章中针对较大的应用程序所描述的那样。记住，GraphQL 的优势之一就是它的表现力。为什么你不能这样对待你的错误呢？

我希望你喜欢这篇文章，我很高兴听到你的想法:)