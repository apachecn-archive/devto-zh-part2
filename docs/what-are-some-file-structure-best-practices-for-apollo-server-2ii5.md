# apollo server 的一些文件结构最佳实践是什么？

> 原文：<https://dev.to/keatz55/what-are-some-file-structure-best-practices-for-apollo-server-2ii5>

人们认为 apollo 服务器中文件夹结构的最佳实践是什么？

我在想把事情组织成这样是否是个好主意

```
src/
    enum/
    input/
    interface/
    mock/
    mutation/
    query/
    resolver/
    scalar/
    subscription/
    type/
    server.ts 
```

Enter fullscreen mode Exit fullscreen mode

然后我在想，我可以像这样把所有的东西融合在一起:

```
import http from "http";
import express from "express";
import { ApolloServer } from "apollo-server-express";
import { makeExecutableSchema } from "graphql-tools";
import { mergeResolvers, mergeTypes } from "merge-graphql-schemas";
import path from "path";
import glob from "glob";
import fs from "fs";

(async () => {
  //   TypeDefs
  const typeDefs = glob
    .sync(path.join(__dirname, "./**/*.graphql"))
    .map(f => fs.readFileSync(f, { encoding: "utf8" }));

  // Resolvers
  const resolvers = await Promise.all(
    glob
      .sync(path.join(__dirname, "./**/*.resolver.ts"))
      .map(async f => (await import(f)).resolver)
  );

  //   Mocks
  const mocks = await Promise.all(
    glob
      .sync(path.join(__dirname, "./**/*.mock.ts"))
      .map(async f => (await import(f)).mock)
  );

  const schema = makeExecutableSchema({
    resolvers: mergeResolvers(resolvers),
    resolverValidationOptions: {
      requireResolversForResolveType: false
    },
    typeDefs: mergeTypes(typeDefs)
  });

  const PORT = 4000;
  const app = express();
  const server = new ApolloServer({
    mocks: Object.assign({}, ...mocks),
    schema
  });
  server.applyMiddleware({ app });

  const httpServer = http.createServer(app);
  server.installSubscriptionHandlers(httpServer);

  httpServer.listen(PORT, () => {
    console.log(
      `🚀 Server ready at http://localhost:${PORT}${server.graphqlPath}`
    );
    console.log(
      `🚀 Subscriptions ready at ws://localhost:${PORT}${
        server.subscriptionsPath
      }`
    );
  });
})(); 
```

Enter fullscreen mode Exit fullscreen mode

你怎么想呢?