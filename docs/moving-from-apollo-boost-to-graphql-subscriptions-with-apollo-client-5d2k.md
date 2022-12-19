# 使用 apollo-client 从 apollo-boost 迁移到 GraphQL 订阅

> 原文：<https://dev.to/hasurahq/moving-from-apollo-boost-to-graphql-subscriptions-with-apollo-client-5d2k>

这是一个关于如何在 Apollo 中使用 GraphQL 订阅的简单教程。

[![](../Images/9b97291992dcd54020a405173544a133.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZWbJu-nf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/content/images/downloaded_images/moving-from-apollo-boost-to-graphql-subscriptions-with-apollo-client-cc0373e0adb0/1-t6XspdUqzOTkL-2dEr8y-A.png)

假设您已经有了基本的 apollo 客户端，可以按照[https://www . apollographql . com/docs/react/essentials/get-started . html](https://www.apollographql.com/docs/react/essentials/get-started.html)工作，现在您想要启用订阅。

* * *

这是你可能有的:

```
import { ApolloProvider } from "react-apollo";
import ApolloClient from "apollo-boost";

const client = new ApolloClient({
  uri: "https://w5xlvm3vzz.lp.gql.zone/graphql"
});
ReactDOM.render(
  (<ApolloProvider client={client}>
   <App />
  </ApolloProvider>),
  document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

现在，要转到订阅，您需要将 apollo 客户端设置更改为:

```
import { ApolloProvider } from "react-apollo";
// Remove the apollo-boost import and change to this:
import ApolloClient from "apollo-client";
// Setup the network "links"
import { WebSocketLink } from 'apollo-link-ws';
import { HttpLink } from 'apollo-link-http';
import { split } from 'apollo-link';
import { getMainDefinition } from 'apollo-utilities';
import { InMemoryCache } from 'apollo-cache-inmemory';
const wsurl = wss://hasura-demo.herokuapp.com/v1alpha1/graphql;
const httpurl = https://hasura-demo.herokuapp.com/v1alpha1/graphql;

const wsLink = new WebSocketLink({
  uri: wsurl,
  options: {
    reconnect: true
  }
});
const httpLink = new HttpLink({
  uri: httpurl,
});

const link = split(
  // split based on operation type
  ({ query }) => {
    const { kind, operation } = getMainDefinition(query);
    return kind === 'OperationDefinition' && operation === 'subscription';
  },
  wsLink,
  httpLink,
)

const client = new ApolloClient({
  link,
  cache: new InMemoryCache()
});
// Use the client just as before
ReactDOM.render(
  (<ApolloProvider client={client}>
   <App />
  </ApolloProvider>),
  document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

这读作:

1.  创建一个`wsLink`
2.  创建一个`httpLink`
3.  创建一个新的 apollo-link，将所有 GraphQL 查询委托给`httpLink`，除非它们是`subscription`查询
4.  创建一个使用我们上面创建的链接对象的`ApolloClient`
5.  启用`InMemoryCache`,因为这是我们在`apollo-boost`中拥有的

这些是你需要安装的软件包:

```
npm install --save apollo-client apollo-link-ws apollo-link-http apollo-link apollo-utilities apollo-cache-inmemory 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。您已经准备好开始使用套餐了！