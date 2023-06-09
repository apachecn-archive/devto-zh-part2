# 用 Express、React 和 GraphQL 构建一个简单的 Web 应用程序

> 原文：<https://dev.to/oktadev/build-a-simple-web-app-with-express-react-and-graphql-5cc2>

GraphQL 和 React 在过去几年里都变得非常流行，可以说它们就像鳄梨和烤面包一样走到了一起。GraphQL 服务器可以用 Node 编写，并允许您使用 JavaScript 类和函数轻松创建灵活的 API。当前端开发人员查询服务器时，只有被请求的信息才会被处理。这意味着你可以通过只请求你正在浏览的页面所需的信息来使后端尽可能健壮，同时保持前端的轻量级。

GraphQL 是一个相对较新的定义类型和查询数据的标准，它有很多不同的实现，包括服务器端和客户端。今天我将向您展示如何使用 Express 创建一个 GraphQL 服务器，以及如何在 React 中创建一个使用 Apollo 的客户端来查询服务器的单页应用程序。

## 创建 React App

开始使用 React 应用的最快方法是使用[创建 React 应用](https://github.com/facebook/create-react-app)。如果你还没有安装 Node、Yarn 和 Create React 应用程序，你可以运行以下命令:

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
npm install --global yarn create-react-app 
```

接下来，创建并启动一个新的应用:

```
create-react-app graphql-express-react
cd graphql-express-react
yarn start 
```

当您运行`create-react-app`时，您将获得一个新文件夹，其中包含您需要开始的所有内容，并且您需要的所有依赖项都将使用`yarn`在本地安装。当你在文件夹中输入`yarn start`时，你就启动了前端开发服务器，当你编辑任何文件时，它都会自动更新。

[![create-react-app bootstrapped app](img/356db2a44542d22c9e31b593f7f804d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--omDWRpIf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/graphql-express-react/create-react-app-f1bc3c3d59d01040f1ce6ae716e14f647788afd5451d5d87c4b63bfa2f43985a.png)

## 创建 GraphQL 服务器

在我们继续编写前端之前，您需要连接一个服务器。运行以下命令来安装启动和运行所需的依赖项:

```
yarn add express@4.16.3 cors@2.8.4 graphql@14.0.2 express-graphql@0.6.12 graphql-tag@2.9.2 
```

在你的项目的`src`文件夹中创建一个新目录，命名为`server` :

```
mkdir src/server 
```

在那里，创建一个名为`index.js`的新文件，代码如下:

```
const express = require('express');
const cors = require('cors');
const graphqlHTTP = require('express-graphql');
const gql = require('graphql-tag');
const { buildASTSchema } = require('graphql');

const POSTS = [
  { author: "John Doe", body: "Hello world" },
  { author: "Jane Doe", body: "Hi, planet!" },
];

const schema = buildASTSchema(gql`
  type Query {
    posts: [Post]
    post(id: ID!): Post
  }

  type Post {
    id: ID
    author: String
    body: String
  }
`);

const mapPost = (post, id) => post && ({ id, ...post });

const root = {
  posts: () => POSTS.map(mapPost),
  post: ({ id }) => mapPost(POSTS[id], id),
};

const app = express();
app.use(cors());
app.use('/graphql', graphqlHTTP({
  schema,
  rootValue: root,
  graphiql: true,
}));

const port = process.env.PORT || 4000
app.listen(port);
console.log(`Running a GraphQL API server at localhost:${port}/graphql`); 
```

让我解释一下这段代码的不同部分。

在文件的顶部，您使用`require`标签来导入您的依赖项。原生节点还不支持`import`标签，但是你可以使用`require`来代替。Node 的未来版本可能会支持`import`。Create React App 在运行之前使用`babel`来转换代码，这允许您在 React 代码中使用`import`语法，所以当我们到达前端代码时，您会看到这一点。

目前，这只是使用一些模拟数据，这就是`const POSTS`包含的内容。每个项目包含一个`author`和一个`body`。

`gql`标签允许您最喜欢的代码编辑器意识到您正在编写 GraphQL 代码，以便它可以适当地将其风格化。它还解析字符串并将其转换为 GraphQL AST [抽象语法树](https://blog.buildo.io/a-tour-of-abstract-syntax-trees-906c0574a067)。然后您需要使用`buildASTSchema`构建一个模式。

GraphQL 模式可能是这段代码中最有趣的部分。这定义了不同的类型，并允许您说出客户端可以查询什么。这也将自动生成一些非常有用的文档，以便您可以专注于编码。

```
type Query {
  posts: [Post]
  post(id: ID!): Post
}

type Post {
  id: ID
  author: String
  body: String
} 
```

这里，您定义了一个`Post`类型，它包含一个`id`、`author`和一个`body`。你需要说出每个元素的类型。在这里，`author`和`body`都使用了原始的`String`类型，`id`是一个`ID`。

`Query`类型是一种特殊的类型，允许您查询数据。这里，你说`posts`会给你一个`Post`的数组，但是如果你想要一个`Post`，你可以通过调用`post`并传入 ID 来查询它。

```
const mapPost = (post, id) => post && ({ id, ...post });

const root = {
  posts: () => POSTS.map(mapPost),
  post: ({ id }) => mapPost(POSTS[id], id),
}; 
```

您需要提供一组解析器来告诉 GraphQL 如何处理查询。当有人查询`posts`时，它将运行这个函数，提供所有`POSTS`的数组，使用它们的索引作为 ID。

当您查询`post`时，它期望一个`id`并将返回给定索引处的文章。

```
const app = express();
app.use(cors());
app.use('/graphql', graphqlHTTP({
  schema,
  rootValue: root,
  graphiql: true,
}));

const port = process.env.PORT || 4000
app.listen(port);
console.log(`Running a GraphQL API server at localhost:${port}/graphql`); 
```

现在，您可以创建服务器了。`graphqlHTTP`函数创建一个运行 GraphQL 的 Express 服务器，它期望解析器为`rootValue`和模式。`graphiql`标志是可选的，它将为您运行一个服务器，允许您更容易地可视化数据并查看自动生成的文档。当您运行`app.listen`时，您正在启动 GraphQL 服务器。

为了确保我们可以轻松地同时运行服务器和客户端，添加以下开发依赖项:

```
yarn add -D nodemon@1.18.4 npm-run-all@4.1.3 
```

接下来，编辑您的`package.json`文件，使`scripts`部分看起来像这样:

```
{
  "start": "npm-run-all --parallel watch:server start:web",
  "start:web": "react-scripts start",
  "start:server": "node src/server",
  "watch:server": "nodemon --watch src/server src/server",
  "build": "react-scripts build",
  "test": "react-scripts test --env=jsdom",
  "eject": "react-scripts eject"
}, 
```

关闭您现有的 web 服务器，然后简单地再次键入`yarn start`来同时运行服务器和客户端。每当您对服务器进行更改时，只有服务器会重新启动。每当您对前端代码进行更改时，页面应该会根据最新的更改自动刷新。

将浏览器指向`http://localhost:4000/graphql`以获取 GraphiQL 服务器。在服务器中修改一些代码后，您可以随时回到这里进行刷新，以查看最新的模式并测试您的查询。

[![GraphiQL](img/a82b36b49e15dc1ebcceeeb572c9808e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1M0Yttwk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/graphql-express-react/graphiql-743319c6b059cfd1ed5bc28394749854af242b7f56d5c8521d43e106d548e9f0.png)

## 连接 React 到 GraphQL

接下来，您需要将前端连接到 GraphQL。我将使用 Bootstrap 以最小的努力获得一些体面的样式。Apollo 是一个很棒的 React 客户端，可以连接到任何 GraphQL 服务器。要安装前端所需的依赖项，请运行以下命令:

```
yarn add bootstrap@4.1.3 reactstrap@6.4.0 apollo-boost@0.1.16 react-apollo@2.1.11 
```

您需要配置 Apollo 客户机，以知道从哪里连接到后端。用下面的代码创建一个新文件【T0:

```
import ApolloClient from 'apollo-boost';

export default new ApolloClient({
  uri: "http://localhost:4000/graphql",
}); 
```

为了让 Apollo 的`Query` React 组件能够使用客户端进行连接，整个应用程序需要包装在一个`ApolloProvider`组件中。你还想包含引导程序的样式，现在你可以去掉 Create React 应用程序附带的`index.css`文件。对您的`src/index.js`文件做如下修改:

```
@@ -1,8 +1,17 @@
 import React from 'react';
 import ReactDOM from 'react-dom';
-import './index.css';
+import { ApolloProvider } from 'react-apollo';
+
+import 'bootstrap/dist/css/bootstrap.min.css';
 import App from './App';
 import registerServiceWorker from './registerServiceWorker';
+import client from './apollo';

-ReactDOM.render(<App />, document.getElementById('root'));
+ReactDOM.render(
+ <ApolloProvider client={client}>
+ <App />
+ </ApolloProvider>,
+ document.getElementById('root')
+);
 serviceWorker.unregister();
+if (module.hot) module.hot.accept(); 
```

`module.hot.accept()`并不是真正必要的，但它使得当你更新组件时，只有应用程序中改变的组件会刷新，而不是刷新整个页面。每隔一段时间，你可能需要刷新来重置应用程序的状态，但一般来说，这会导致更快的周转时间。

创建一个新文件`src/PostViewer.js`，该文件将获取数据并将其呈现在一个表中:

```
import React from 'react';
import gql from 'graphql-tag';
import { Query } from 'react-apollo';
import { Table } from 'reactstrap';

export const GET_POSTS = gql`
  query GetPosts {
    posts {
      id
      author
      body
    }
  }
`;

export default () => (
  <Query query={GET_POSTS}>
    {({ loading, data }) => !loading && (
      <Table>
        <thead>
          <tr>
            <th>Author</th>
            <th>Body</th>
          </tr>
        </thead>
        <tbody>
          {data.posts.map(post => (
            <tr key={post.id}>
              <td>{post.author}</td>
              <td>{post.body}</td>
            </tr>
          ))}
        </tbody>
      </Table>
    )}
  </Query>
); 
```

`Query`组件需要一个 GraphQL 查询。在这种情况下，您只是获得所有带有 ID 和`author`和`body`的帖子。`Query`组件也需要一个渲染函数作为其唯一的子组件。它提供了一个`loading`状态，但是在我们的例子中，当它加载时我们不会显示任何东西，因为在本地获取数据会非常快。一旦加载完成，`data`变量将是一个包含您请求的数据的对象。

上面的代码呈现了一个包含所有帖子的表格(`Table`是一个组件，它包含了让它看起来更漂亮所需的所有引导类)。

现在，您应该修改您的`src/App.js`文件，以包含您刚刚创建的`PostViewer`组件。它应该是这样的:

```
import React, { Component } from 'react';

import PostViewer from './PostViewer';

class App extends Component {
  render() {
    return (
      <main>
        <PostViewer />
      </main>
    );
  }
}

export default App; 
```

现在如果你去`http://localhost:3000`你应该会看到这个:

[![plain table](img/45b7e6a8a25e599eeb1b2a119c34275a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZvM62ZmQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/graphql-express-react/plain-table-596b8d563739fbc4032de03fe94e3501bbfd156cdd7193bc49a053d2d5ea16b5.png)

## 增加了在 GraphQL 中编辑帖子的能力

在 GraphQL 中，查询通常是只读的。如果你想修改数据，你应该使用所谓的*突变*来代替。

在`src/server/index.js`的`const schema`中创建一个新的`Mutation`类型来提交文章。您可以创建一个`input`类型来简化您的输入变量。新的变异应该在成功时返回新的`Post`:

```
type Mutation {
  submitPost(input: PostInput!): Post
}

input PostInput {
  id: ID
  author: String!
  body: String!
} 
```

您还需要更新您的`root`变量来为`submitPost`创建一个新的解析器。添加以下解析器:

```
submitPost: ({ input: { id, author, body } }) => {
  const post = { author, body };
  let index = POSTS.length;

  if (id != null && id >= 0 && id < POSTS.length) {
    if (POSTS[id].authorId !== authorId) return null;

    POSTS.splice(id, 1, post);
    index = id;
  } else {
    POSTS.push(post);
  }

  return mapPost(post, index);
}, 
```

如果你提供了一个`id`，它将试图在该索引处找到文章，并用提供的`author`和`body`替换数据。否则，它会添加一个新帖子。然后它返回您提供的文章以及新的`id`。当您向 GraphQL 发送一个突变请求时，您可以定义想要返回哪些片段:

[![submit post](img/b7b65c4d6e673650342240efcd464727.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hVV2Zee5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/graphql-express-react/submit-post-d2e4223335b436d4e6a5110e2f64b9d8ddfda355f2ea54477d8815bcefbc7aee.png)

对于前端，您需要创建一个新的组件来编辑帖子。React 中的表单可以通过一个名为[最终表单](https://github.com/final-form/react-final-form)的库来简化。用`yarn` :
安装

```
yarn add final-form@4.10.0 react-final-form@3.6.5 
```

现在，创建一个新文件`src/PostEditor.js`，并用以下内容填充它(我将在下面详细解释):

```
import React from 'react';
import gql from 'graphql-tag';
import {
  Button,
  Form,
  FormGroup,
  Label,
  Modal,
  ModalHeader,
  ModalBody,
  ModalFooter,
} from 'reactstrap';
import { Form as FinalForm, Field } from 'react-final-form';

import client from './apollo';
import { GET_POSTS } from './PostViewer';

const SUBMIT_POST = gql`
  mutation SubmitPost($input: PostInput!) {
    submitPost(input: $input) {
      id
    }
  }
`;

const PostEditor = ({ post, onClose }) => (
  <FinalForm
    onSubmit={async ({ id, author, body }) => {
      const input = { id, author, body };

      await client.mutate({
        variables: { input },
        mutation: SUBMIT_POST,
        refetchQueries: () => [{ query: GET_POSTS }],
      });

      onClose();
    }}
    initialValues={post}
    render={({ handleSubmit, pristine, invalid }) => (
      <Modal isOpen toggle={onClose}>
        <Form onSubmit={handleSubmit}>
          <ModalHeader toggle={onClose}>
            {post.id ? 'Edit Post' : 'New Post'}
          </ModalHeader>
          <ModalBody>
            <FormGroup>
              <Label>Author</Label>
              <Field
                required
                name="author"
                className="form-control"
                component="input"
              />
            </FormGroup>
            <FormGroup>
              <Label>Body</Label>
              <Field
                required
                name="body"
                className="form-control"
                component="input"
              />
            </FormGroup>
          </ModalBody>
          <ModalFooter>
            <Button type="submit" disabled={pristine} color="primary">Save</Button>
            <Button color="secondary" onClick={onClose}>Cancel</Button>
          </ModalFooter>
        </Form>
      </Modal>
    )}
  />
);

export default PostEditor; 
```

`submitPost`突变是连接到后端的新突变。它可以使用服务器中定义的`PostInput`类型:

```
const SUBMIT_POST = gql`
  mutation SubmitPost($input: PostInput!) {
    submitPost(input: $input) {
      id
    }
  }
`; 
```

最终表单采用一个`onSubmit`函数，该函数将传递用户输入的数据。提交帖子后，您会想要关闭模态，所以当您完成提交后，`PostEditor`会调用一个`onClose`道具。

Final Form 还接受一个`initialValues`对象来定义表单最初应该有什么值。在这种情况下，`PostEditor`组件将接受一个`post`属性，其中包含您需要的变量，因此它将作为初始值传递。

另一个必需的道具是`render`函数，它将呈现表单。Final Form 为您提供了一些有用的表单道具，这样您就可以知道表单是否有效，或者它是否从`initialValues`中被修改过。

```
const PostEditor = ({ post, onClose }) => (
  <FinalForm
    onSubmit={/* ... */}
    initialValues={post}
    render={/* ... */}
  />
);

export default PostEditor; 
```

在`onSubmit`函数中，您将调用提交帖子所需的突变。Apollo 允许您重新获取查询。因为你知道一旦你提交编辑，你的文章列表就会过时，你可以在这里重新获取`GET_POSTS`查询。

```
onSubmit={async ({ id, author, body }) => {
  const input = { id, author, body };

  await client.mutate({
    variables: { input },
    mutation: SUBMIT_POST,
    refetchQueries: () => [{ query: GET_POSTS }],
  });

  onClose();
}} 
```

`render`功能将显示一个引导模式。这个`PostEditor`组件只有在你希望它打开的时候才会被渲染，所以`isOpen`只是被设置为`true`。这里，当用户在模态之外点击，点击`Esc`，或者点击取消按钮时，你也可以使用`onClose`道具来关闭模态。

表单需要将`handleSubmit`函数作为`onSubmit`属性传递给它。这告诉表单通过最终表单，而不是向页面发送一个`POST`请求。

Final Form 还处理所有需要受控的`input`的样板文件。每当用户输入内容时，您可以使用`Field`组件，而不是将数据存储在状态中。

```
render={({ handleSubmit, pristine, invalid }) => (
  <Modal isOpen toggle={onClose}>
    <Form onSubmit={handleSubmit}>
      <ModalHeader toggle={onClose}>
        {post.id ? 'Edit Post' : 'New Post'}
      </ModalHeader>
      <ModalBody>
        <FormGroup>
          <Label>Author</Label>
          <Field
            required
            name="author"
            className="form-control"
            component="input"
          />
        </FormGroup>
        <FormGroup>
          <Label>Body</Label>
          <Field
            required
            name="body"
            className="form-control"
            component="input"
          />
        </FormGroup>
      </ModalBody>
      <ModalFooter>
        <Button type="submit" disabled={pristine} color="primary">Save</Button>
        <Button color="secondary" onClick={onClose}>Cancel</Button>
      </ModalFooter>
    </Form>
  </Modal>
)} 
```

接下来，你必须对你的`PostViewer`做一些小改动。这将为每一行添加一个钩子，以便您可以确定该行是否应该是可编辑的，如果是，稍微更改一下样式并允许您单击该行。单击该行会调用另一个回调函数，您可以使用它来设置正在编辑的文章。

```
diff --git a/src/PostViewer.js b/src/PostViewer.js
index 5c53b5a..84177e0 100644
--- a/src/PostViewer.js
+++ b/src/PostViewer.js
@@ -13,7 +13,11 @@ export const GET_POSTS = gql`
   }
 `;

-export default () => (
+const rowStyles = (post, canEdit) => canEdit(post)
+ ? { cursor: 'pointer', fontWeight: 'bold' }
+ : {};
+
+const PostViewer = ({ canEdit, onEdit }) => (
   <Query query={GET_POSTS}>
     {({ loading, data }) => !loading && (
       <Table>
@@ -25,7 +29,11 @@ export default () => (
         </thead>
         <tbody>
           {data.posts.map(post => (
- <tr key={post.id}>
+ <tr
+ key={post.id}
+ style={rowStyles(post, canEdit)}
+ onClick={() => canEdit(post) && onEdit(post)}
+ >
               <td>{post.author}</td>
               <td>{post.body}</td>
             </tr>
@@ -35,3 +43,10 @@ export default () => (
     )}
   </Query>
 );
+
+PostViewer.defaultProps = {
+ canEdit: () => false,
+ onEdit: () => null,
+};
+
+export default PostViewer; 
```

现在，在`src/App.js`中把这些绑在一起。您可以创建一个“新帖子”按钮来创建一个新帖子，并使它可以编辑任何其他现有的帖子:

```
import React, { Component } from 'react';
import { Button, Container } from 'reactstrap';

import PostViewer from './PostViewer';
import PostEditor from './PostEditor';

class App extends Component {
  state = {
    editing: null,
  };

  render() {
    const { editing } = this.state;

    return (
      <Container fluid>
        <Button
          className="my-2"
          color="primary"
          onClick={() => this.setState({ editing: {} })}
        >
          New Post
        </Button>
        <PostViewer
          canEdit={() => true}
          onEdit={(post) => this.setState({ editing: post })}
        />
        {editing && (
          <PostEditor
            post={editing}
            onClose={() => this.setState({ editing: null })}
          />
        )}
      </Container>
    );
  }
}

export default App; 
```

## 向 React + GraphQL Web 应用添加用户认证

向项目添加身份验证的一个简单方法是使用 Okta。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。如果你还没有，那么[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)。登录你的开发者控制台，导航到**应用**，然后点击**添加应用**。选择**单页 App** ，然后点击**下一步**。

因为 Create React App 默认运行在端口 3000 上，所以您应该将其添加为基本 URI 和登录重定向 URI。您的设置应该如下所示:

[![create new application settings](img/c79b88714c50f220fb595318380dccc4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bb_2UYjc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/graphql-express-react/create-new-application-settings-31cf8fdf708dbb8c222e9e5dd07b7e89f53cc2bae6b5c69acf427a44faadaeef.png)

点击**完成**保存你的应用，然后复制你的**客户端 ID** 并作为一个变量粘贴到你的项目根目录下的一个名为`.env.local`的文件中。这将允许您在代码中访问该文件，而无需在源代码管理中存储凭据。您还需要添加您的组织 URL(不带`-admin`后缀)。环境变量(除了`NODE_ENV`之外)需要以`REACT_APP_`开头，以便 Create React App 读取它们，因此文件应该看起来像这样:

**.env.local**

```
REACT_APP_OKTA_CLIENT_ID={yourClientId}
REACT_APP_OKTA_ORG_URL=https://{yourOktaDomain} 
```

稍后您还将需要一个用于服务器的 API 令牌，因此当您在那里时，导航到**API**->-**令牌**，然后点击**创建令牌**。您可以有许多令牌，所以只需给这个令牌起一个提醒您它的用途的名称，比如“GraphQL Express”。你会得到一个你现在只能看到的令牌。如果您丢失了令牌，您必须重新创建一个。也将此添加到`.env`中。

```
REACT_APP_OKTA_TOKEN={yourOktaAPIToken} 
```

向 React 应用添加 Okta 认证的最简单方法是使用 [Okta 的 React SDK](https://github.com/okta/okta-oidc-js/tree/master/packages/okta-react) 。您还需要添加路由，这可以使用 [React 路由器](https://reacttraining.com/react-router/)来完成。

```
yarn add @okta/okta-react@1.1.1 react-router-dom@4.3.1 
```

为了知道用户是否通过了身份验证，Okta 要求应用程序被包装在一个具有某种配置的`Security`组件中。它也依赖于 React 路由器，所以你最终会得到一个`BrowserRouter`组件，包装一个`Security`组件，包装一个`ApolloProvider`组件，最终将你的`App`包装在一个`Route`中。您的`src/index.js`文件应该看起来像这样:

```
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter, Route } from 'react-router-dom';
import { Security, ImplicitCallback } from '@okta/okta-react';
import { ApolloProvider } from 'react-apollo';

import 'bootstrap/dist/css/bootstrap.min.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';
import client from './apollo';

ReactDOM.render(
  <BrowserRouter>
    <Security
      issuer={`${process.env.REACT_APP_OKTA_ORG_URL}/oauth2/default`}
      redirect_uri={`${window.location.origin}/implicit/callback`}
      client_id={process.env.REACT_APP_OKTA_CLIENT_ID}
    >
      <ApolloProvider client={client}>
        <Route path="/implicit/callback" component={ImplicitCallback} />
        <Route path="/" component={App} />
      </ApolloProvider>
    </Security>
  </BrowserRouter>,
  document.getElementById('root')
);
registerServiceWorker();
if (module.hot) module.hot.accept(); 
```

Okta SDK 附带了一个`withAuth`高阶组件(HoC ),可以用于各种与身份验证相关的事情，但是对于这个例子，您只需要知道您是否通过了身份验证，以及一些关于用户的信息。为了使这变得简单一点，我编写了一个简单的 HoC 来覆盖 Okta SDK 附带的 HoC。创建一个新文件`src/withAuth.js`，包含以下内容:

```
import React from 'react';
import { withAuth } from '@okta/okta-react';

export default Component => withAuth(class WithAuth extends React.Component {
  state = {
    ...this.props.auth,
    authenticated: null,
    user: null,
    loading: true,
  };

  componentDidMount() {
    this.updateAuth();
  }

  componentDidUpdate() {
    this.updateAuth();
  }

  async updateAuth() {
    const authenticated = await this.props.auth.isAuthenticated();
    if (authenticated !== this.state.authenticated) {
      const user = await this.props.auth.getUser();
      this.setState({ authenticated, user, loading: false });
    }
  }

  render() {
    const { auth, ...props } = this.props;
    return <Component {...props} auth={this.state} />;
  }
}); 
```

通过用这个新功能包装一个组件，每当用户登录或注销时，你的应用程序将自动重新呈现，你将能够访问关于用户的信息。

现在您可以用这个`withAuth` HoC 包装`App`组件。在应用程序第一次加载的一小段时间里，Okta 不太确定用户是否登录。为了简单起见，在加载期间不要在你的`App`组件中渲染任何东西。但是，您可以选择呈现帖子，并在了解用户的更多信息之前禁用编辑。

在`src/App.js`中渲染函数的最顶端，添加以下内容:

```
const { auth } = this.props;
if (auth.loading) return null;

const { user, login, logout } = auth; 
```

现在，您可以用下面的代码替换“New Post”按钮，如果您没有登录，它将呈现一个“Sign In”按钮。如果你已经登录，你将会看到之前的“新文章”按钮和“退出”按钮。这将使您必须登录才能创建新帖子。

```
{user ? (
  <div>
    <Button
      className="my-2"
      color="primary"
      onClick={() => this.setState({ editing: {} })}
    >
      New Post
    </Button>
    <Button
      className="m-2"
      color="secondary"
      onClick={() => logout()}
    >
      Sign Out (signed in as {user.name})
    </Button>
  </div>
) : (
  <Button
    className="my-2"
    color="primary"
    onClick={() => login()}
  >
    Sign In
  </Button>
)} 
```

为了确保只有登录后才能编辑文章，请更改`canEdit`属性以检查您是否有用户。

```
canEdit={() => Boolean(user)} 
```

你还需要导出`withAuth(App)`而不是`App`。您的`src/App.js`文件现在应该看起来像这样:

```
import React, { Component } from 'react';
import { Button, Container } from 'reactstrap';

import PostViewer from './PostViewer';
import PostEditor from './PostEditor';
import withAuth from './withAuth';

class App extends Component {
  state = {
    editing: null,
  };

  render() {
    const { auth } = this.props;
    if (auth.loading) return null;

    const { user, login, logout } = auth;
    const { editing } = this.state;

    return (
      <Container fluid>
        {user ? (
          <div>
            <Button
              className="my-2"
              color="primary"
              onClick={() => this.setState({ editing: {} })}
            >
              New Post
            </Button>
            <Button
              className="m-2"
              color="secondary"
              onClick={() => logout()}
            >
              Sign Out (signed in as {user.name})
            </Button>
          </div>
        ) : (
          <Button
            className="my-2"
            color="primary"
            onClick={() => login()}
          >
            Sign In
          </Button>
        )}
        <PostViewer
          canEdit={() => Boolean(user)}
          onEdit={(post) => this.setState({ editing: post })}
        />
        {editing && (
          <PostEditor
            post={editing}
            onClose={() => this.setState({ editing: null })}
          />
        )}
      </Container>
    );
  }
}

export default withAuth(App); 
```

## 向服务器添加用户认证

web 应用程序现在要求你登录才能创建帖子，但精明的用户仍然可以通过直接向服务器发送请求来修改数据。为了防止这种情况，需要向服务器添加一些身份验证。您需要添加 Okta 的节点 SDK 和 JWT 验证器作为依赖项。您还需要使用`dotenv`来从`.env.local`中读取变量。

```
yarn add @okta/jwt-verifier@0.0.12 @okta/okta-sdk-nodejs@1.2.0 dotenv@6.0.0 
```

在你的`src/server/index.js`文件的顶部，你需要告诉`dotenv`读入环境变量:

```
require('dotenv').config({ path: '.env.local' }); 
```

您将需要前端发送一个 JSON Web 令牌(JWT ),以便用户可以标识自己。当你在服务器上得到一个 JWT，你需要使用 Okta 的 JWT 验证器来验证它。要获得关于用户的更多信息，您还需要使用 Okta 的 Node SDK。您可以在服务器顶部附近设置这些，就在所有其他`require`语句之后。

```
const { Client } = require('@okta/okta-sdk-nodejs');
const OktaJwtVerifier = require('@okta/jwt-verifier');

const oktaJwtVerifier = new OktaJwtVerifier({
  clientId: process.env.REACT_APP_OKTA_CLIENT_ID,
  issuer: `${process.env.REACT_APP_OKTA_ORG_URL}/oauth2/default`,
});

const client = new Client({
  orgUrl: process.env.REACT_APP_OKTA_ORG_URL,
  token: process.env.REACT_APP_OKTA_TOKEN,
}); 
```

既然您将使用真实的用户，那么仅仅发送一个带有用户名的字符串就没有什么意义了，尤其是因为用户名可能会随着时间的推移而改变。如果一个帖子和一个用户有关联就更好了。为此，为您的用户创建一个新的`AUTHORS`变量，并将`POSTS`变量改为一个`authorId`而不是一个`author`字符串:

```
const AUTHORS = {
  1: { id: 1, name: "John Doe" },
  2: { id: 2, name: "Jane Doe" },
};

const POSTS = [
  { authorId: 1, body: "Hello world" },
  { authorId: 2, body: "Hi, planet!" },
]; 
```

在您的模式中，您将不再需要`PostInput`中的`author: String`输入，并且`Post`上的`author`现在应该是类型`Author`而不是`String`。你还需要制作这个新的`Author`类型:

```
type Author {
  id: ID
  name: String
} 
```

当查找您的用户时，您现在想要从`AUTHORS`变量:
中提取作者

```
const mapPost = (post, id) => post && ({
  ...post,
  id,
  author: AUTHORS[post.authorId],
}); 
```

现在，您需要创建一个`getUserId`函数来验证访问令牌并获取一些关于用户的信息。令牌将作为一个`Authorization`报头发送，看起来有点像`Bearer eyJraWQ...7h-zfqg`。如果作者的名字不存在，下面的函数将把它添加到`AUTHORS`对象中。

```
const getUserId = async ({ authorization }) => {
  try {
    const accessToken = authorization.trim().split(' ')[1];
    const { claims: { uid } } = await oktaJwtVerifier.verifyAccessToken(accessToken);

    if (!AUTHORS[uid]) {
      const { profile: { firstName, lastName } } = await client.getUser(uid);

      AUTHORS[uid] = {
        id: uid,
        name: [firstName, lastName].filter(Boolean).join(' '),
      };
    }

    return uid;
  } catch (error) {
    return null;
  }
}; 
```

现在你可以改变`submitPost`函数来获取用户发帖时的 ID。如果用户没有登录，您可以返回`null`。这将阻止帖子被创建。如果用户试图编辑他们没有创建的帖子，你也可以返回`null`。

```
- submitPost: ({ input: { id, author, body } }) => {
- const post = { author, body };
+ submitPost: async ({ input: { id, body } }, { headers }) => {
+ const authorId = await getUserId(headers);
+ if (!authorId) return null;
+
+ const post = { authorId, body };
     let index = POSTS.length;

     if (id != null && id >= 0 && id < POSTS.length) {
+ if (POSTS[id].authorId !== authorId) return null;
+
       POSTS.splice(id, 1, post);
       index = id;
     } else { 
```

您最终的`src/server/index.js`文件现在应该是这样的:

```
require('dotenv').config({ path: '.env.local' });

const express = require('express');
const cors = require('cors');
const graphqlHTTP = require('express-graphql');
const gql = require('graphql-tag');
const { buildASTSchema } = require('graphql');
const { Client } = require('@okta/okta-sdk-nodejs');
const OktaJwtVerifier = require('@okta/jwt-verifier');

const oktaJwtVerifier = new OktaJwtVerifier({
  clientId: process.env.REACT_APP_OKTA_CLIENT_ID,
  issuer: `${process.env.REACT_APP_OKTA_ORG_URL}/oauth2/default`,
});

const client = new Client({
  orgUrl: process.env.REACT_APP_OKTA_ORG_URL,
  token: process.env.REACT_APP_OKTA_TOKEN,
});

const AUTHORS = {
  1: { id: 1, name: "John Doe" },
  2: { id: 2, name: "Jane Doe" },
};

const POSTS = [
  { authorId: 1, body: "Hello world" },
  { authorId: 2, body: "Hi, planet!" },
];

const schema = buildASTSchema(gql`
  type Query {
    posts: [Post]
    post(id: ID): Post
  }

  type Mutation {
    submitPost(input: PostInput!): Post
  }

  input PostInput {
    id: ID
    body: String
  }

  type Post {
    id: ID
    author: Author
    body: String
  }

  type Author {
    id: ID
    name: String
  }
`);

const mapPost = (post, id) => post && ({
  ...post,
  id,
  author: AUTHORS[post.authorId],
});

const getUserId = async ({ authorization }) => {
  try {
    const accessToken = authorization.trim().split(' ')[1];
    const { claims: { uid } } = await oktaJwtVerifier.verifyAccessToken(accessToken);

    if (!AUTHORS[uid]) {
      const { profile: { firstName, lastName } } = await client.getUser(uid);

      AUTHORS[uid] = {
        id: uid,
        name: [firstName, lastName].filter(Boolean).join(' '),
      };
    }

    return uid;
  } catch (error) {
    return null;
  }
};

const root = {
  posts: () => POSTS.map(mapPost),
  post: ({ id }) => mapPost(POSTS[id], id),
  submitPost: async ({ input: { id, body } }, { headers }) => {
    const authorId = await getUserId(headers);
    if (!authorId) return null;

    const post = { authorId, body };
    let index = POSTS.length;

    if (id != null && id >= 0 && id < POSTS.length) {
      if (POSTS[id].authorId !== authorId) return null;

      POSTS.splice(id, 1, post);
      index = id;
    } else {
      POSTS.push(post);
    }

    return mapPost(post, index);
  },
};

const app = express();
app.use(cors());
app.use('/graphql', graphqlHTTP({
  schema,
  rootValue: root,
  graphiql: true,
}));

const port = process.env.PORT || 4000
app.listen(port);
console.log(`Running a GraphQL API server at localhost:${port}/graphql`); 
```

现在，您需要做一些前端更改，以确保您请求的是一个`author`对象，而不是假设它是一个字符串，并且您需要将您的 auth 令牌作为一个头传入。

组件`PostViewer`将需要一个小的更新

```
diff --git a/src/PostViewer.js b/src/PostViewer.js
index 84177e0..6bfddb9 100644
--- a/src/PostViewer.js
+++ b/src/PostViewer.js
@@ -7,7 +7,10 @@ export const GET_POSTS = gql`
   query GetPosts {
     posts {
       id
- author
+ author {
+ id
+ name
+ }
       body
     }
   }
@@ -34,7 +37,7 @@ const PostViewer = ({ canEdit, onEdit }) => (
               style={rowStyles(post, canEdit)}
               onClick={() => canEdit(post) && onEdit(post)}
             >
- <td>{post.author}</td>
+ <td>{post.author.name}</td>
               <td>{post.body}</td>
             </tr>
           ))} 
```

在`PostEditor`中，你只需要完全去掉`author`,因为用户不能编辑它，它将由 auth 令牌决定。

```
diff --git a/src/PostEditor.js b/src/PostEditor.js
index 182d1cc..6cb075c 100644
--- a/src/PostEditor.js
+++ b/src/PostEditor.js
@@ -25,8 +25,8 @@ const SUBMIT_POST = gql`

 const PostEditor = ({ post, onClose }) => (
   <FinalForm
- onSubmit={async ({ id, author, body }) => {
- const input = { id, author, body };
+ onSubmit={async ({ id, body }) => {
+ const input = { id, body };

       await client.mutate({
         variables: { input },
@@ -44,15 +44,6 @@ const PostEditor = ({ post, onClose }) => (
             {post.id ? 'Edit Post' : 'New Post'}
           </ModalHeader>
           <ModalBody>
- <FormGroup>
- <Label>Author</Label>
- <Field
- required
- name="author"
- className="form-control"
- component="input"
- />
- </FormGroup>
             <FormGroup>
               <Label>Body</Label>
               <Field 
```

您的 Apollo 客户端是您将发送授权令牌的地方。为了访问 auth 令牌，您需要某种闭包。对于每个请求，Apollo 都允许您修改头。将`src/apollo.js`更改为:

```
import ApolloClient from 'apollo-boost';

let auth;

export const updateAuth = (newAuth) => {
  auth = newAuth;
};

export default new ApolloClient({
  uri: "http://localhost:4000/graphql",
  request: async (operation) => {
    const token = await auth.getAccessToken();
    operation.setContext({
      headers: {
        authorization: `Bearer ${token}`,
      },
    });
  },
}); 
```

现在，只要`auth`在`src/withAuth.js`中发生变化，您就需要调用`updateAuth`组件，以确保它总是最新的。

```
diff --git a/src/withAuth.js b/src/withAuth.js
index cce1b24..6d29dcc 100644
--- a/src/withAuth.js
+++ b/src/withAuth.js
@@ -1,6 +1,8 @@
 import React from 'react';
 import { withAuth } from '@okta/okta-react';

+import { updateAuth } from './apollo';
+
 export default Component => withAuth(class WithAuth extends React.Component {
   state = {
     ...this.props.auth,
@@ -18,6 +20,8 @@ export default Component => withAuth(class WithAuth extends React.Component {
   }

   async updateAuth() {
+ updateAuth(this.props.auth);
+
     const authenticated = await this.props.auth.isAuthenticated();
     if (authenticated !== this.state.authenticated) {
       const user = await this.props.auth.getUser(); 
```

现在，如果你再次修改`src/App.js`文件中的`canEdit`，你可以让用户只能编辑他们自己的帖子:

```
onChange={(post) => user && user.sub === post.author.id} 
```

## 了解有关 GraphQL、React、Express 和 Web 安全的更多信息

现在，您已经成功地构建了一个 GraphQL 服务器，连接它以作出反应，并使用安全的用户认证锁定它！作为一个练习，看看是否可以将服务器从使用简单的内存 JavaScript 对象切换到使用持久数据存储。关于在 Node 中使用 Sequelize 的例子，请看 Randall 的博客。

如果你想看最终的样本代码，你可以在 github 上找到它[。](https://github.com/oktadeveloper/okta-graphql-react-express-example)

如果您想了解更多关于 GraphQL、Express 或 React 的知识，请查看 Okta 开发人员博客上的其他一些帖子:

*   [通过实例构建并理解 Express 中间件](https://developer.okta.com/blog/2018/09/13/build-and-understand-express-middleware-through-examples)
*   [用 Node 和 React 构建一个基本的 CRUD 应用](https://developer.okta.com/blog/2018/07/10/build-a-basic-crud-app-with-node-and-react)
*   [建立并理解一个简单的 Node.js 网站，并进行用户认证](https://developer.okta.com/blog/2018/08/17/build-and-understand-user-authentication-in-node)
*   [使用 React、GraphQL 和用户认证构建健康跟踪应用](https://developer.okta.com/blog/2018/07/11/build-react-graphql-api-user-authentication)

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，就像我们在脸书关注[，或者订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。