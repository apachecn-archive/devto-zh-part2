# 为什么要用 Redux？有明确例子的理由

> 原文：<https://dev.to/bnevilleoneill/why-use-redux-reasons-with-clear-examples-3k5l>

#### 考虑 Redux 的好处？我们有文章给你。

[![](img/042b2fe9d428619475ee528cf76298b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z65Zeq8o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/713/1%2AdlapmYAhWBkrFuHm020qlg.png)

现在是 2018 年，是软件开发中使用许多新库和工具的时候了。有了这么多的工具和库，(在你读完这篇文章之前可能已经发布了一个 JavaScript 库)，在没有真正理解它的好处或者为什么你应该使用它的情况下就开始使用每一个新的工具和库可能不是最明智的做法。

Redux 并不新鲜，而且相当受欢迎。然而，如果你不知道 Redux 是什么，你想知道为什么你应该使用它，这篇文章是给你的。即使你知道 Redux 是什么，但仍然想知道在你的应用程序中使用它是否是一个明智的选择，这篇文章也适合你。

在本文中，我们将通过讨论 Redux 的好处来考虑您应该使用 Redux 的一些原因。首先，我们将了解 Redux 的基础知识及其工作原理。然后我们将通过使用一个简单但实用的组件来看看使用 Redux 如何在您的应用程序中帮助您。

### Redux 是什么？

> Redux 是 JavaScript 应用程序的可预测状态容器。它帮助您编写行为一致、在不同环境(客户机、服务器和本机)中运行、易于测试的应用程序。——

 *简单来说，Redux 是一个状态管理工具。虽然它主要用于 React，但也可以用于任何其他 JavaScript 框架或库。它是轻量级的，只有 2KB(包括依赖项)，所以您不必担心它会使您的应用程序的资产变大。

使用 Redux，应用程序的状态保存在一个存储中，每个组件都可以从这个存储中访问它需要的任何状态。让我们深入一点，看看为什么您可能需要一个状态管理工具。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 为什么需要状态管理工具

在本文的开始，我们清楚地表明 Redux 是一个状态管理工具，但是现在，让我们考虑一下为什么您可能需要一个状态管理工具。

大多数库像 React，Angular 等。为组件提供了一种在内部管理其状态的方式，而不需要任何外部库或工具。它适用于组件很少的应用程序，但是随着应用程序越来越大，管理跨组件共享的状态就变得很麻烦。

在组件间共享数据的应用程序中，实际知道一个州应该在哪里可能会令人困惑。理想情况下，组件中的数据应该只存在于一个组件中。因此在兄弟组件之间共享数据变得很困难。

例如，在 React 中，为了在兄弟组件之间共享数据，状态必须存在于父组件中。更新该状态的方法由该父组件提供，并作为道具传递给这些兄弟组件。

下面是 React 中登录组件的一个简单示例。登录组件的输入影响它的兄弟组件，即状态组件所显示的内容:

```
class App extends React.Component {
  constructor(props) {
    super(props);
    // First the Parent creates a state for what will be passed
    this.state = { userStatus: "NOT LOGGED IN"}
    this.setStatus = this.setStatus.bind(this);
  }
  // A method is provided for the child component to update the state of the
  // userStatus
  setStatus(username, password) {
    const newUsers = users;
    newUsers.map(user => {
      if (user.username == username && user.password === password) {
        this.setState({
          userStatus: "LOGGED IN"
        })
      }
    });
  }

  render() {
    return (
      <div>
        // the state is passed to the sibling as a props as is updated whenever
        // the child component changes the input
        <Status status={this.state.userStatus} />
        // this method is passed to the child component as a props which it
        // uses to change the state of the userStatus
        <Login handleSubmit={this.setStatus} />
      </div>
    );
  }
}); 
```

> 💡请记住，父组件不需要这些数据，但是因为其子组件需要共享数据，所以它必须提供一个状态。

现在想象一下，当一个状态必须在组件树中相距很远的组件之间共享时会发生什么。状态必须从一个组件传递到另一个组件，直到它到达需要它的地方。

基本上，状态必须提升到最近的父组件，然后提升到下一个组件，直到到达需要该状态的两个组件共有的祖先，然后再传递下去。这使得状态难以维持，并且更不可预测。这也意味着将数据传递给不需要这些数据的组件。

很明显，随着应用程序变得越来越复杂，状态管理也变得越来越混乱。这就是为什么你需要一个像 Redux 这样的状态管理工具，让维护这些状态变得更容易。在考虑 Redux 的好处之前，让我们先了解一下 Redux 的概念。

### 了解 Redux 如何工作

Redux 的工作方式很简单。有一个保存应用程序整个状态的中央存储。每个组件都可以访问存储的状态，而不必将道具从一个组件发送到另一个组件。

有三个构建部分:**动作**、**存储**和**减速器**。下面简单讨论一下他们各自是做什么的。这很重要，因为它们可以帮助您了解 Redux 的优势以及如何使用它。我们将实现一个类似于上面登录组件的例子，但是这次是在 Redux 中。

#### **在 Redux** 中的动作

简单来说，**动作**就是事件。它们是将数据从应用程序发送到 Redux 存储的唯一方式。数据可以来自用户交互、API 调用甚至表单提交。

使用 store.dispatch()方法发送操作。动作是普通的 JavaScript 对象，它们必须有一个 type 属性来指示要执行的动作的类型。它们还必须有一个有效负载，其中包含该操作应该处理的信息。动作是通过动作创建者创建的。

下面是一个在应用程序中登录时可以执行的操作示例:

```
{ 
  type: "LOGIN",
  payload: {
    username: "foo",
    password: "bar"
  }
} 
```

下面是它的动作创建者的例子:

```
const setLoginStatus = (name, password) => {
  return {
    type: "LOGIN",
    payload: {
      username: "foo",
      password: "bar"
    }
  }
} 
```

如前所述，动作必须包含 type 属性，然后是要存储的其他有效负载。

#### **Redux 中的还原剂**

Reducers 是纯粹的函数，它接受应用程序的当前状态，执行一个动作并返回一个新状态。这些状态存储为对象，它们指定应用程序的状态如何改变以响应发送到存储区的操作。

它基于 JavaScript 中的 reduce 函数，在回调函数执行后，从多个值中获取一个值。

这里有一个关于**减速器**如何在 Redux 中工作的例子:

```
const LoginComponent = (state = initialState, action) => {
    switch (action.type) {

      // This reducer handles any action with type "LOGIN"
      case "LOGIN":
          return state.map(user => {
              if (user.username !== action.username) {
                  return user;
              }

              if (user.password == action.password) {
                  return {
                      ...user,
                      login_status: "LOGGED IN"
                  }
              }
          });
default:
          return state;
      } 
}; 
```

> 💡Reducers 获取应用程序以前的状态，并根据传递给它的动作返回一个新状态。

作为纯函数，它们不会改变传递给它的对象中的数据，也不会对应用程序产生任何副作用。给定相同的对象，它应该总是产生相同的结果。

#### **储存在 Redux**

存储保存应用程序状态。任何 Redux 应用程序中只有一个 store。您可以通过 helper 方法访问存储的状态、更新状态以及注册或取消注册侦听器。

让我们为我们的登录应用程序创建一个商店:

```
const store = createStore(LoginComponent); 
```

对状态执行的操作总是返回新的状态。因此，这种状态非常容易预测。

现在我们对 Redux 有了更多的了解，让我们回到之前实现的登录组件示例，看看 Redux 如何改进组件。

```
class App extends React.Component {
    render() {
        return (
            <div>
                <Status user={this.props.user.name}/>
                <Login login={this.props.setLoginStatus}/>
            </div>
        )
    }
} 
```

使用 Redux，存储中有一个通用状态，每个组件都可以访问该状态。这消除了将状态从一个组件持续传递到另一个组件的需要。

当 Redux 和 React 一起使用时，状态将不再需要提升，因此它使您更容易跟踪哪个动作导致了任何变化。如上所述，组件不需要为其子组件提供任何状态或方法来在它们之间共享数据。一切都由 Redux 处理。这大大简化了应用程序，使其更容易维护。

虽然这是您应该使用 Redux 的主要好处和原因，但下面是您应该使用 Redux 的其他原因的总结:

#### 使用 Redux 的其他好处

1.  Redux 使状态可预测。在 Redux 中，状态总是可预测的。如果相同的状态和动作被传递给一个 reducer，那么总会产生相同的结果，因为 reducer 是纯函数。状态也是不可变的，永远不会改变。这使得实现像无限撤销和重做这样的艰巨任务成为可能。还可以实现时间旅行，即在以前的状态之间来回移动并实时查看结果的能力。
2.  **可维护性。Redux 对代码的组织方式有严格的要求，因此了解 Redux 的人更容易理解任何 Redux 应用程序的结构。这通常会使维护变得更容易。**
3.  **可调试数天。** Redux 让调试应用变得简单。通过记录操作和状态，很容易理解编码错误、网络错误和生产过程中可能出现的其他形式的错误。
4.  **易于测试**将 Redux 应用作为用于改变纯函数状态的函数来测试是很容易的。
5.  您可以将应用程序的一些状态保存到本地存储，并在刷新后恢复。这真的很棒。
6.  Redux 也可以用于服务器端渲染。使用它，您可以通过将应用程序的状态及其对服务器请求的响应发送到服务器来处理应用程序的初始呈现。然后，所需的组件以 HTML 格式呈现并发送给客户端。

#### 结论

我们已经讨论了 Redux 的主要功能，以及为什么 Redux 对您的应用程序有益。虽然 Redux 有它的好处，但这并不意味着你应该在你所有的应用中加入 Redux。没有 Redux，您的应用程序可能仍然工作得很好。

Redux 的一个主要好处是增加了将“发生了什么”与“事情如何变化”分离的方向。然而，如果您确定您的项目需要状态管理工具，那么您应该只实现 Redux。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[为什么用 Redux？有明显例子的理由](https://blog.logrocket.com/why-use-redux-reasons-with-clear-examples-d21bffd5835/)首先出现在[的博客](https://blog.logrocket.com)上。*