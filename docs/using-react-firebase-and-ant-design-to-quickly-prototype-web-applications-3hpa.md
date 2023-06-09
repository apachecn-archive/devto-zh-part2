# 使用 React、Firebase 和 Ant Design 快速构建 Web 应用程序原型

> 原文：<https://dev.to/nbrempel/using-react-firebase-and-ant-design-to-quickly-prototype-web-applications-3hpa>

在本指南中，我将向您展示如何使用 [Firebase](https://firebase.google.com) 、 [React](https://reactjs.org) 和 [Ant Design](https://ant.design) 作为构建模块来构建功能强大、高保真的 web 应用程序。为了说明这一点，我们将通过一个例子来构建一个待办事项列表应用程序。

如今，有如此多的工具可用于 web 开发，以至于让人感到麻痹。你应该使用哪个服务器？你准备选择什么前端框架？通常，推荐的方法是使用您最了解的技术。一般来说，这意味着选择一个久经考验的数据库，如 [PostgreSQL](https://www.postgresql.org) 或 [MySQL](https://www.mysql.com) ，为你的网络服务器选择一个 MVC 框架(我最喜欢的是 [Adonis](http://adonisjs.com) )，或者使用该框架的渲染引擎，或者使用客户端 javascript 库，如 [ReactJS](https://reactjs.org) 或 [AngularJS](https://angularjs.org) 。

使用上面的方法是有成效的——特别是如果你有好的样板文件来帮助你开始的话——但是如果你想用几乎为零的设置时间快速构建一些东西呢？有时模型不能向客户传达足够的信息；有时你想为一个新产品快速建立一个 MVP。

这个例子的源代码可以在[这里](https://github.com/nrempel/quick-todo)找到。如果你正在寻找一个好的 IDE 来使用本指南，[我强烈推荐 Visual Studio 代码](https://dev.to/posts/sublime-to-vscode/)。

[![React + Firebase + Ant Design = Love](img/e8f58be15f95d7ff93773819ead84774.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ywgPeAF2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/react%2Bfirebase%2Bant.png)

## 使用 Create React App 的 React 开发环境

[React](https://reactjs.org) 是一个用于构建用户界面的 javascript 库。该库是“基于组件”的，这意味着你可以创建构建块，并用这些可重用的组件组成你的界面。[创建 React App](https://github.com/facebook/create-react-app) ，另一方面是零配置 React 环境。它开箱即用，只需一个 shell 命令，就能让您的环境保持最新。

首先，按照这里的说明[为您的系统安装 Node.js。](https://nodejs.org/en/download/)

然后启动您的新 Create React App 项目:

```
npx create-react-app quick-todo && cd quick-todo 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用:
运行开发 web 服务器

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

在您的浏览器中访问 [http://localhost:3000/](http://localhost:3000/) ,您应该会看到:

[![Create React App starter page](img/21fab61de06a08a50bd51cf6304f5fd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K9tNYV5f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/react_start.png)

太好了！您现在拥有了一个功能性的 React 开发环境。

## 将 Firebase 与您的应用程序集成

现在您已经有了 React 开发环境，下一步就是将 Firebase 集成到您的应用程序中。Firebase 的核心产品是实时数据库服务。这意味着您的用户不需要刷新页面来查看应用程序状态的更新，您也不需要额外的努力来实现这一点。

如果你还没有的话，去 https://firebase.google.com 注册一个账户。然后创建一个名为`quick-todo`的新 Firebase 项目。

一旦有了 Firebase 项目，就可以提供一个“云 Firestore”数据库:

[![Provision Cloud Firestore Database Step 1](img/87ccdbdcd272b8ac0320effbff320abf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8CXahFc5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/firebase_step_1.png)

[![Provision Cloud Firestore Database Step 2](img/00616e088b52c11fbf2ab86e7cfe9684.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VLKItPUy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/firebase_step_2.png)

[![Provision Cloud Firestore Database Step 3](img/5aaff09cfa7824f11b6574e5be3e99fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yo_mZDcu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/firebase_step_3.png)

这里我们没有对数据库使用身份验证，因为我们正在构建一个原型。当你构建一个真正的应用程序时，你会想要创建适当的[安全规则](https://firebase.google.com/docs/firestore/security/get-started)，但是现在我们先不要担心这个。

好了，现在您的 Firebase 数据库已经准备好了，让我们将它集成到 React 应用程序中。在您的项目目录中，运行以下命令来安装必要的依赖项:

```
npm i --save firebase @firebase/app @firebase/firestore 
```

Enter fullscreen mode Exit fullscreen mode

然后，在您的项目中，在`src`目录中添加一个名为`firestore.js`的新文件，其内容如下:

### [T1】firestore . js](#firestorejs)

```
import firebase from "@firebase/app";
import "@firebase/firestore";

const config = {
  apiKey: "<apiKey>",
  authDomain: "<authDomain>",
  databaseURL: "<databaseURL>",
  projectId: "<projectId>",
  storageBucket: "",
  messagingSenderId: "<messageingSenderId>"
};

const app = firebase.initializeApp(config);
const firestore = firebase.firestore(app);

export default firestore; 
```

Enter fullscreen mode Exit fullscreen mode

确保您插入了自己项目中的`apiKey`和其他参数。您可以在项目的设置中找到这些:

[![Firebase Settings](img/09d7776dd37af8e51375c5e2c8a8c445.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dh3iiequ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/firebase_step_4.png)

好吧！现在，通过导入我们的`firestore.js`实用程序:
，我们可以在应用程序的任何地方访问实时 Firebase 数据库

```
import firestore from "./firestore"; 
```

Enter fullscreen mode Exit fullscreen mode

## 安装蚂蚁设计

[Ant Design](https://ant.design) 是一个全面的设计系统，包括一整套 React 组件。因为 React 是基于组件的，所以使用 Ant Design 的 React 组件作为构建块来快速组装原型是相当简单的。

要开始使用 Ant Design 的 React 组件系统，请安装`antd` :

```
npm i --save antd 
```

Enter fullscreen mode Exit fullscreen mode

## 齐心协力

我们现在拥有了构建原型所需的所有工具。让我们使用我们的环境来构建一个 todo 应用程序的高保真原型。

首先，让我们洗心革面。修改`App.js`和`App.css`，使它们看起来像这样:

### App.js

```
import React, { Component } from "react";

import "./App.css";

class App extends Component {
  render() {
    return <div className="App" />;
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

### App.cs

```
@import "~antd/dist/antd.css";

.App {
  text-align: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何为`antd`导入 css 的。

现在，让我们为 todo 应用程序设置一些基本结构。为此我们可以使用`antd` [布局](https://ant.design/components/layout/)组件:

### App.js

```
import React, { Component } from "react";
import { Layout } from "antd";

import "./App.css";

const { Header, Footer, Content } = Layout;

class App extends Component {
  render() {
    return (
      <Layout className="App">
        <Header className="App-header">
          <h1>Quick Todo</h1>
        </Header>
        <Content className="App-content">Content</Content>
        <Footer className="App-footer">&copy; My Company</Footer>
      </Layout>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

### App.css

```
@import "~antd/dist/antd.css";

.App {
  text-align: center;
}

.App-header h1 {
  color: whitesmoke;
}

.App-content {
  padding-top: 100px;
  padding-bottom: 100px;
} 
```

Enter fullscreen mode Exit fullscreen mode

做出这些更改后，我们就可以运行我们的开发服务器了。您应该像这样播种:

[![Quick Todo Step 1](img/985977bdce780ae211561738bd629239.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--41WasHBW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/quick_todo_step_1.png)

现在，我们可以利用我们之前创建的`firestore.js`模块开始将 todos 添加到我们的实时 firebase 数据库中。你可以在这里阅读更多关于如何使用 Firebase Cloud Firestore [的信息。](https://firebase.google.com/docs/firestore/)

让我们看一下对源代码的以下更改:

### App.js

```
import React, { Component } from "react";
import { Layout, Input, Button } from "antd";

// We import our firestore module
import firestore from "./firestore";

import "./App.css";

const { Header, Footer, Content } = Layout;

class App extends Component {
  constructor(props) {
    super(props);
    // Set the default state of our application
    this.state = { addingTodo: false, pendingTodo: "" };
    // We want event handlers to share this context
    this.addTodo = this.addTodo.bind(this);
  }

  async addTodo(evt) {
    // Set a flag to indicate loading
    this.setState({ addingTodo: true });
    // Add a new todo from the value of the input
    await firestore.collection("todos").add({
      content: this.state.pendingTodo,
      completed: false
    });
    // Remove the loading flag and clear the input
    this.setState({ addingTodo: false, pendingTodo: "" });
  }

  render() {
    return (
      <Layout className="App">
        <Header className="App-header">
          <h1>Quick Todo</h1>
        </Header>
        <Content className="App-content">
          <Input
            ref="add-todo-input"
            className="App-add-todo-input"
            size="large"
            placeholder="What needs to be done?"
            disabled={this.state.addingTodo}
            onChange={evt => this.setState({ pendingTodo: evt.target.value })}
            value={this.state.pendingTodo}
            onPressEnter={this.addTodo}
          />
          <Button
            className="App-add-todo-button"
            size="large"
            type="primary"
            onClick={this.addTodo}
            loading={this.state.addingTodo}
          >
            Add Todo
          </Button>
        </Content>
        <Footer className="App-footer">&copy; My Company</Footer>
      </Layout>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

### App.css

```
@import "~antd/dist/antd.css";

.App {
  text-align: center;
}

.App-header h1 {
  color: whitesmoke;
}

.App-content {
  padding-top: 100px;
  padding-bottom: 100px;
}

.App-add-todo-input {
  max-width: 300px;
  margin-right: 5px;
}

.App-add-todo-button {
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这些更改，您可以看到我们的应用程序现在有了一个输入来添加新的 todos。

[![Quick Todo Step 2](img/53d370be8251e7eaaad886cd17e419a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zNuHDtcd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/quick_todo_step_2.png)

添加 todo 还没有出现在 UI 中，但是您可以浏览 Firebase 数据库来查看您添加的任何 todo！

[![Quick Todo Step 3](img/e27192d4f90a1230d907b031a8709f01.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iCXS7Dlf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/quick_todo_step_3.png)

拥有一个全功能的待办事项应用程序的最后一步是显示待办事项列表，并允许用户完成它们。为此，我们可以使用 Ant Design 的 [List](https://ant.design/components/list/) 组件来显示不完整的待办事项。以下面的源代码为例:

### App.js

```
import React, { Component } from "react";
import { Layout, Input, Button, List, Icon } from "antd";

// We import our firestore module
import firestore from "./firestore";

import "./App.css";

const { Header, Footer, Content } = Layout;

class App extends Component {
  constructor(props) {
    super(props);
    // Set the default state of our application
    this.state = { addingTodo: false, pendingTodo: "", todos: [] };
    // We want event handlers to share this context
    this.addTodo = this.addTodo.bind(this);
    this.completeTodo = this.completeTodo.bind(this);
    // We listen for live changes to our todos collection in Firebase
    firestore.collection("todos").onSnapshot(snapshot => {
      let todos = [];
      snapshot.forEach(doc => {
        const todo = doc.data();
        todo.id = doc.id;
        if (!todo.completed) todos.push(todo);
      });
      // Sort our todos based on time added
      todos.sort(function(a, b) {
        return (
          new Date(a.createdAt).getTime() - new Date(b.createdAt).getTime()
        );
      });
      // Anytime the state of our database changes, we update state
      this.setState({ todos });
    });
  }

  async completeTodo(id) {
    // Mark the todo as completed
    await firestore
      .collection("todos")
      .doc(id)
      .set({
        completed: true
      });
  }

  async addTodo() {
    if (!this.state.pendingTodo) return;
    // Set a flag to indicate loading
    this.setState({ addingTodo: true });
    // Add a new todo from the value of the input
    await firestore.collection("todos").add({
      content: this.state.pendingTodo,
      completed: false,
      createdAt: new Date().toISOString()
    });
    // Remove the loading flag and clear the input
    this.setState({ addingTodo: false, pendingTodo: "" });
  }

  render() {
    return (
      <Layout className="App">
        <Header className="App-header">
          <h1>Quick Todo</h1>
        </Header>
        <Content className="App-content">
          <Input
            ref="add-todo-input"
            className="App-add-todo-input"
            size="large"
            placeholder="What needs to be done?"
            disabled={this.state.addingTodo}
            onChange={evt => this.setState({ pendingTodo: evt.target.value })}
            value={this.state.pendingTodo}
            onPressEnter={this.addTodo}
            required
          />
          <Button
            className="App-add-todo-button"
            size="large"
            type="primary"
            onClick={this.addTodo}
            loading={this.state.addingTodo}
          >
            Add Todo
          </Button>
          <List
            className="App-todos"
            size="large"
            bordered
            dataSource={this.state.todos}
            renderItem={todo => (
              <List.Item>
                {todo.content}
                <Icon
                  onClick={evt => this.completeTodo(todo.id)}
                  className="App-todo-complete"
                  type="check"
                />
              </List.Item>
            )}
          />
        </Content>
        <Footer className="App-footer">&copy; My Company</Footer>
      </Layout>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

### App.css

```
@import "~antd/dist/antd.css";

.App {
  text-align: center;
}

.App-header h1 {
  color: whitesmoke;
}

.App-content {
  padding-top: 100px;
  padding-bottom: 100px;
}

.App-add-todo-input {
  max-width: 300px;
  margin-right: 5px;
}

.App-add-todo-button {
}

.App-todos {
  background-color: white;
  max-width: 400px;
  margin: 0 auto;
  margin-top: 20px;
  margin-bottom: 20px;
}

.App-todo {
  /* position: relative; */
}

.App-todo-complete {
  font-size: 22px;
  font-weight: bold;
  cursor: pointer;
  position: absolute;
  right: 24px;
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这些最终的更改，我们可以看到添加到我们的应用程序中的待办事项列表:

[![Quick Todo Step 4](img/701c277b23b8db02a2b0e5053bfba52d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s----H2P8Sd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nrempel.com/quick_todo_step_4.png)

我们做到了！使用 React、Firebase 和 Ant Design，我们能够快速创建高保真的 web 应用程序。使用这些工具可以帮助你立刻创造出实用且美观的东西。

当你需要向某人演示一个应用程序的功能而不需要花太多时间构建它时，这是非常有价值的。

* * *

本指南着重于使用工具快速构建原型，但我认为这种方法也可以用于创建生产就绪的 web 应用程序。Ant Design [可以主题化](https://ant.design/docs/react/customize-theme)，Firebase 的可扩展性极强。

在传统网络服务器上使用 Firebase 的唯一问题是成本。对于有很多用户的应用程序，Firebase 可能会很快变得昂贵；然而，使用 web 服务器和数据库的传统方法也可能是昂贵的主机。此外，您还需要考虑构建、配置和管理您的 web 服务器和数据库的时间和成本！

最初发表于[nrempel.com](https://nrempel.com/guides/react-firebase-ant-design/)