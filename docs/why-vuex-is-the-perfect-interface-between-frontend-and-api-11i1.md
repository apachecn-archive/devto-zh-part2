# 为什么 VueX 是前端和 API 之间的完美接口

> 原文：<https://dev.to/kball/why-vuex-is-the-perfect-interface-between-frontend-and-api-11i1>

前端 web 开发的复杂性的增加已经推动了前端和后端的专门化和分离的增加。

这种专业化和复杂性的增加带来了许多好处——用户在网上的体验质量呈指数级增长，同时通过比以往任何时候都多的设备接触到更多的人。

然而，它也带来了许多挑战。

##### 挑战:前端和 API 之间的接口

前端和 API 之间的接口已经成为摩擦、挑战和复杂性的共同点。

在理想的情况下，后端和前端将和谐地一起发展，密切沟通，后端提供的数据将完全符合前端的需求。

实际上，通常一个应用程序的两个部分是由完全不同的团队甚至不同的公司开发的。当你自己的开发团队在后端工作时，一个机构的专业团队在构建设计和前端，这并不罕见

##### 典型解决方案

结果是一个典型的如下所示的流:

1.  使用伪造的“存根”数据构建 UI，要么直接内嵌在模板和代码中，要么通过一组 fixtures 加载。
2.  当 API 准备好时，争先恐后地用真正的 API 调用和数据替换每个集成点。

这种方法有两个问题:

1.  数据集成通常分散在整个应用程序中，需要跟踪和修改大量代码。
2.  即使数据是相对隔离的，在前端期望的和 API 最终交付的之间也经常存在不匹配。

##### 更好的解决方案存在:VueX

如果您正在使用 Vue.js 开发您的前端，这个问题的一个更好的解决方案就在您的指尖。

深入集成到 Vue 中的 [VueX](https://vuex.vuejs.org/en/) 库提供了完美的解决方案，为您的数据创建一个*干净*、*隔离*的接口，使存根数据和真正的 API 之间的转换变得轻而易举。

### vuex 是什么

VueX 是一个受 Flux、Redux 和 Elm 架构启发的状态管理库，但经过专门设计和调整，可以与 Vue.js 很好地集成，并利用 Vue 的反应能力。

所有这些库都旨在解决一个简单的问题:当许多组件共享一个状态时，特别是兄弟组件或视图非常不同的组件，管理状态的分布和更新是一个挑战。

像 VueX 这样的库，通过创建一个全局状态树，可以由每个组件以结构化的方式访问和更新，使得以结构化和可维护的方式管理跨组件的共享状态成为可能。

### VueX 是如何工作的

VueX 将状态管理分为 3 个关键部分:**状态**、**突变**和**动作**。当您实例化一个 VueX 商店时，您定义了这三个对象:

```
const store = new Vuex.Store({
  state: {
    ...
  },
  mutations: {
    ...
  },
  actions: {
    ...
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

##### 状态

状态代表实际的数据本身。这只是一个包含数据树的 JavaScript 对象。在 VueX 中，您可以有一个单一的全局状态树或按模块组织(如用户状态树、产品状态树等)

例如，我们可以使用这个状态树来跟踪当前用户，如果用户没有登录，就从 null 开始:

```
state: {
  currentUser: null
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 突变

突变是我们改变状态树的机制。状态*的所有变化必须*通过突变，这允许 VueX 以可预测的方式管理状态。

突变的一个例子可能如下:

```
mutations: {
  setCurrentUser(currentState, user) {
    currentState.currentUser = user;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

变异是*同步*，而*直接*修改状态对象(与例如 Redux 相比，Redux 的等价概念被称为缩减器，并返回一个*新的*对象。)

状态对象的这种同步、直接的变化与 Vue 的反应性概念完全吻合。VueX 状态对象是反应性的，所以变化会波及到所有的依赖项。

你通过`commit`函数调用一个突变:

```
store.commit('setCurrentUser', user); 
```

Enter fullscreen mode Exit fullscreen mode

##### 动作

动作是 VueX 的最后一部分，是*意图*和*修改*之间的中介。

动作是*异步*，而*通过`committing`突变间接*修改存储。然而，因为它们是异步的，所以它们能做的远不止这些。

异步性允许操作处理 API 调用、用户交互和整个操作流程。

举个简单的例子，一个动作可以调用一个 API 并记录结果:

```
actions: {
  login(context, credentials) {
    return myLoginApi.post(credentials).then((user) => {
      context.commit('setCurrentUser', user)
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

动作可以返回承诺，允许调度动作的视图或其他代码等待动作完成，并根据结果做出反应。不使用`commit`，而是使用`dispatch`一个动作。例如，我们的调用代码可能看起来像:

```
store.dispatch('login', credentials).then(() => {
  // redirect to logged in area
}).catch((error) => {
  // Display error messages about bad password
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么 VueX 动作是 API 的完美接口

如果您正在从事一个后端和前端同时发展的项目，或者您在一个 UI/前端团队中，该团队甚至可能在后端存在之前就构建了一个用户界面，那么您可能很熟悉在开发前端时需要剔除部分后端或数据的问题。

这种表现的一种常见方式是纯静态模板或内容，占位符值和文本就在您的前端模板中。

在此基础上更上一层楼的是某种形式的 fixtures，即由前端静态加载并放置到位的数据。

这两者经常遇到相同的挑战:当后端最终可用时，需要进行大量的重构工作来将数据放置到位。

即使(奇迹般地)来自后端的数据结构与您的设备相匹配，您仍然不得不到处寻找每个集成点。如果结构是不同的(让我们面对它，它通常是)，你不仅必须这样做，而且你必须弄清楚如何改变前端或创建一个抽象层来转换数据。

##### 输入 VueX 股份

VueX 的美妙之处在于，动作提供了一种*完美的*方式来隔离和抽象前端和后端，而且这样做的方式使得从存根数据到真实后端的更新无缝而简单。

让我稍微扩展一下。让我们以登录为例。如果我们的登录 API 还不存在，但是我们仍然准备构建前端，我们可以这样实现我们的操作:

```
actions: {
  login(context, credentials) {
    const user = MY_STUBBED_USER;
    if(credentials.login === 'works@test.com') {
      context.commit('setCurrentUser', user)
      return Promise.resolve();
    } else {
      return Promise.reject(new Error('invalid login'));
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的前端可以实现一个登录，它的行为*与未来的行为*完全一样，带有测试数据，允许成功和失败。行为将立即发生*而不是通过 API 异步发生*，但是通过返回承诺，现在任何调用者都可以像对待真正的 API 调用一样对待它。

当我们的 API 可用时，我们可以简单地修改这个动作来使用它，而我们代码库中的其他一切都保持不变。

##### 处理数据不匹配

将我们的 API 调用隔离到 VueX 还为我们提供了一种漂亮而干净的方式来处理后端和前端之间的数据格式不匹配。

继续我们的登录示例，也许我们假设 API 将在登录时返回我们需要的所有用户信息，但是一旦我们通过身份验证，我们需要从一个单独的端点获取首选项，即使这样格式也与我们预期的不同

我们可以将这种差异完全隔离在我们的 VueX 操作中，防止我们需要在前端的任何其他地方进行更改。因为承诺可以被链接和嵌套，所以我们可以遍历一系列 API 调用，在我们的操作被认为完成之前，所有这些调用都需要完成。

```
actions: {
  login(context, credentials) {
    return myLoginApi.post(credentials).then((userData) => {
      const user = { ...userData };
      return myPreferencesApi.get(userData.id).then((preferencesData) => {
        user.preferences = transformPreferencesData(preferencesData);
        context.commit('setCurrentUser', user)
      });
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

从我们的状态修改和分派我们的`login`动作的代码的角度来看，最终结果是*完全相同的*。

有了 VueX，将新的或不断变化的后端 API 集成到我们的前端的挑战已经大大简化了。

* * *

又及——如果你对这类话题感兴趣，我会发送一份名为“[星期五前端](https://zendev.com/friday-frontend.html)”的每周时事通讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T2】