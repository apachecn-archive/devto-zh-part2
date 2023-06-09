# 带有 React 和 Apollo 的 AWS AppSync 应用程序

> 原文：<https://dev.to/gyandeeps/aws-appsync-app-with-react-and-apollo-4c7j>

# 简介

介绍如何使用 AWS AppSync 构建应用程序，以及前端需要什么来让整个系统正常工作。我们不会涉及任何基础知识，因为有太多的博客文章在那里。

## 先决条件

基本了解:

*   反应
*   反应路由器
*   AWS 控制台内的 AWS AppSync 设置
*   不同的阿波罗图书馆
*   GraphQL 术语-查询、变异和订阅

#### 资源

*   [用 AWS AppSync 构建无服务器 React GraphQL 应用](https://tylermcginnis.com/building-serverless-react-graphql-apps-with-aws-appsync)作者[纳德·达比特](https://twitter.com/dabit3)

如果你对这些话题都没有基本的了解，那么我强烈建议你在继续之前先了解一下。

## 我们要学什么

*   用所有配置连接 AWS AppSync
*   使用 AWS Cognito 集成进行身份验证
*   管理变异和解决方案
*   使用 apollo 管理客户端状态和服务器端状态

## 动机

*   找不到任何解释这些部分如何一起工作的文档。
*   没有太多的信息为高级水平的东西与基础入门。
*   花了这么多时间试图弄清楚所有这些部分以及它们是如何协同工作的。

# 设置

## 库

*   [`aws-amplify`、`aws-amplify-react`](https://github.com/aws/aws-amplify) -用于 Auth 和其他 AWS 调用
*   [`aws-appsync`、`aws-appsync-react`](https://github.com/awslabs/aws-mobile-appsync-sdk-js)——管理与`graphql` api 端点通信的 apollo 客户端的包装器
*   [`react-apollo`](https://github.com/apollographql/react-apollo)——Apollo 客户端 React 框架库
*   其他阿波罗库，如`apollo-link-http`、`apollo-link-state`和`apollo-cache-inmemory`

注意:大多数博客并没有涵盖应用的所有方面，这也是他们只使用这些库的子集的原因。

# 积木

## 基础 app 加载

我假设您已经使用`react`加载了一些东西(使用`create-react-app`创建)。我们将在此基础上再接再厉。假设你现在有一个带有 hello world 的 div 渲染。

```
import * as React from "react";
import * as ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom";

class App extends React.Component {
    render() {
        return <div>Hello World</div>;
    }
}

ReactDOM.render(
    <BrowserRouter>
        <App />
    </BrowserRouter>,
    document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

## 添加 AWS AppSync 层连接到后端

我们将使用 AWS AppSync 团队提供的`aws-amplify`库。这将负责与 AWS 资源直接对话，如用于 Auth、analytics api、pubsub、api 调用等的`cognito`。更多详细信息，请使用[自述](https://github.com/aws/aws-amplify/blob/master/README.md)。

```
import * as React from "react";
import * as ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom";
import Amplify, { Auth } from "aws-amplify";

Amplify.configure({
    Auth: {
        // REQUIRED - Amazon Cognito Identity Pool ID
        identityPoolId: "XX-XXXX-X:XXXXXXXX-XXXX-1234-abcd-1234567890ab",
        // REQUIRED - Amazon Cognito Region
        region: "XX-XXXX-X",
        // OPTIONAL - Amazon Cognito User Pool ID
        userPoolId: "XX-XXXX-X_abcd1234",
        // OPTIONAL - Amazon Cognito Web Client ID
        userPoolWebClientId: "XX-XXXX-X_abcd1234"
    }
});

class App extends React.Component {
    render() {
        return <div>Hello World</div>;
    }
}

ReactDOM.render(
    <BrowserRouter>
        <App />
    </BrowserRouter>,
    document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

*   一旦您配置了`Amplify`,那么在任何其他模块中，您都可以导入一个特定的工件并使用它。
    *   https://aws.github.io/aws-amplify/media/developer\_guide
*   在这种情况下，我们只使用 Auth，所以我们只配置了这一部分。但是对于 API 或任何其他模块，您可以添加一个块及其配置。
*   按照上面的设置，你应该可以在应用程序的任何地方使用`Auth`模块。

## 添加缓存和状态管理

我们将使用`aws-appsync`和`aws-appsync-react`库来创建本地缓存，来自 graphql 的数据和您的本地状态将保存在这里。这个概念更像`Redux`，但在幕后`aws-appsync`使用阿波罗缓存和它的库来完成繁重的工作。

```
import * as React from "react";
import * as ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom";
import Amplify, { Auth } from "aws-amplify";
import { ApolloProvider } from "react-apollo";
import { ApolloLink } from "apollo-link";
import { InMemoryCache } from "apollo-cache-inmemory";
import AWSAppSyncClient, { createAppSyncLink } from "aws-appsync";
import { withClientState } from "apollo-link-state";

Amplify.configure({
    Auth: {
        // REQUIRED - Amazon Cognito Identity Pool ID
        identityPoolId: "XX-XXXX-X:XXXXXXXX-XXXX-1234-abcd-1234567890ab",
        // REQUIRED - Amazon Cognito Region
        region: "XX-XXXX-X",
        // OPTIONAL - Amazon Cognito User Pool ID
        userPoolId: "XX-XXXX-X_abcd1234",
        // OPTIONAL - Amazon Cognito Web Client ID
        userPoolWebClientId: "XX-XXXX-X_abcd1234"
    }
});

const cache = new InMemoryCache();
let nextTodoId = 1;
const stateLink = withClientState({
    cache,
    defaults: {
        todos: []
    },
    resolvers: {
        Mutation: {
            addTodo: (_, { text }, { cache }) => {
                const query = gql`
                    query GetTodos {
                        todos @client {
                            id
                            text
                            completed
                        }
                    }
                `;
                const previous = cache.readQuery({ query });
                const newTodo = {
                    id: nextTodoId++,
                    text,
                    completed: false,
                    __typename: "TodoItem"
                };
                const data = {
                    todos: previous.todos.concat([newTodo])
                };
                cache.writeData({ data });
                return newTodo;
            }
        }
    }
});

const authConfig = {
    type: appSyncAtrributes.authenticationType,
    jwtToken: async () =>
        (await Auth.currentSession()).getAccessToken().getJwtToken()
};

const client = new AWSAppSyncClient(
    {
        disableOffline: true,
        url: appSyncAtrributes.graphqlEndpoint,
        region: appSyncAtrributes.region,
        auth: authConfig,
        complexObjectsCredentials: () => Auth.currentCredentials()
    },
    {
        cache,
        link: ApolloLink.from([
            stateLink,
            createAppSyncLink({
                url: appSyncAtrributes.graphqlEndpoint,
                region: appSyncAtrributes.region,
                auth: authConfig,
                complexObjectsCredentials: () => Auth.currentCredentials()
            })
        ])
    }
);

class App extends React.Component {
    render() {
        return <div>Hello World</div>;
    }
}

ReactDOM.render(
    <BrowserRouter>
        <ApolloProvider client={client}>
            <App />
        </ApolloProvider>
    </BrowserRouter>,
    document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码中发生了很多事情。让我们在这里讨论重要的部分。

#### 缓存/状态

基于他们的所有文章，当你通过提供第一个参数直接使用`AWSAppSyncClient`时，你自动开始维护你的远程调用的本地缓存。也就是说，当你从你的 graphql api 中获取数据时，它会存储在缓存中。但是我们也想维护一些客户端的本地状态，这并不是来自 grapghql api。我们使用`apollo-link-state`来实现。

这里需要理解的最重要的部分是:

*   `createAppSyncLink` -默认状态设置由`aws-appsync`库完成。
*   `withClientState` -我们在前端创建由应用程序维护的本地状态。在这种情况下，它是 todo 状态。
*   使用这个，我们将上面两个命令的输出组合起来，得到一个进入状态的入口点。可以认为这是将远程州和本地州合并成一个州。
*   `ApolloProvider` -它就像`react-redux`提供者，将客户端向下游公开到其他组件中。

## 在组件中使用状态

这里我只关注`App`组件，因为围绕它的所有其他代码都保持不变。理想情况下，您应该将`App`组件创建为一个单独的文件并导入它。

```
import * as React from "react";
import { Mutation, Query } from "react-apollo";
import gql from "graphql-tag";

const GET_TODOS = gql`
    {
        todos @client {
            id
            completed
            text
        }
    }
`;

const GET_ORDERS = gql`
    {
        listOrders {
            items {
                id
                name
            }
        }
    }
`;

const ADD_TODO = gql`
    mutation addTodo($text: String!) {
        addTodo(text: $text) @client {
            id
        }
    }
`;

class App extends React.Component {
    render() {
        return (
            <Query query={GET_ORDERS}>
                {({ data }) => (
                    <Mutation mutation={ADD_TODO}>
                        {(addTodo) => (
                            <>
                                <div>{JSON.stringify(data)}</div>
                                <button
                                    onClick={() =>
                                        addTodo({
                                            variables: { text: "gyandeep" }
                                        })
                                    }
                                >
                                    add
                                </button>
                            </>
                        )}
                    </Mutation>
                )}
            </Query>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里需要理解的最重要的部分是:

*   `Query`和`Mutation`-graph QL 的组件。
*   `onClick` `addTodo` call - Add todo 使用变异定义中的指令`@client`在客户端调用变异。这告诉底层的 apollo 基础设施，这个 graphql 命令仅用于本地更改。
    *   在使用`withClientState`的主文件`stateLink`中，我们为 add todo 定义了变异解析器，它基本上写入本地缓存，然后组件刷新以读取值。
    *   把这个想象成 **`redux`动作和减速器**。
*   `GET_ORDERS` -这个 graphql 查询不使用`@client`，所以它通过网络访问 graphql 接口，然后当数据返回时，它自动更新缓存。

## 认证

在所有的设置完成后，如果你想在继续之前验证你的路径，那么你可以使用助手来实现。

```
import { withAuthenticator } from "aws-amplify-react";

// App class definition here as seen above

export default withAuthenticator(App); 
```

Enter fullscreen mode Exit fullscreen mode

因此，在这个组件得到渲染之前，它将路由到登录页面。关于这方面的更多细节，我推荐使用这里的[指南](https://aws.github.io/aws-amplify/media/authentication_guide)。它很好地解释了一切。

# 结论

*   你不应该使用`Redux`，因为它不能很好地与 apollo 缓存一起工作。我知道这很难，但一旦你搬到阿波罗，你会没事的。
    *   我花了一些时间才明白。
*   把你的解析器想成 redux reducers，我建议把它们分开写。
*   将所有的`gql`查询都看作是动作，我建议也分别编写它们。

## 备注

*   写这个是因为我与所有这些片段(不同的库)斗争了很多，并向人们和 twitter 抱怨了很多。
*   我在 AppSync 世界刚刚 2-3 周大，所以这个帖子就像一个大脑垃圾场。