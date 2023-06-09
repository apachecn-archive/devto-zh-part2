# 什么、为什么以及如何反应(路由器)

> 原文：<https://dev.to/mangel0111/the-what-why-and-how-of-react-routers-41b>

嗨，这是关于 React 基础知识的第二篇文章，今天我们将讨论任何 web 应用程序最重要的部分之一，**路由**。每个网站都有一个特定的方式来处理他的导航。我将尝试解释一种不同的方法，并尝试给你一个更清晰的概念，什么时候你必须使用一个库，什么时候最好自己使用。

**什么是路由？**
路径基本上就是你如何在你的网站中移动。您可以从索引、主页或仪表板页面开始，然后站点的每个部分都将有一个基于您想要显示的内容的域，您可以有如下内容:

*   **域名资源的名称:**例如，对于一个有书籍列表的页面，你会看到这样的内容:`books`。
*   **资源的详细信息:**要查看某个特定资源的详细信息，在本例中是一本书，您将有一个类似于`books/{id}`的 url。
*   **创建、管理、编辑等。一个资源:**你可以在域旁边添加你想做什么的描述，比如，要创建一本新书，你可以使用:`books/create`而要编辑你可以使用:`books/edit`。

这是最常用的模式，效果很好。这些网站可以呈指数增长，这是极好的，因为这会给你一个`Domain`和`Ownership`的概念。通常，一个域会变成一个大模块，有几个逻辑和特定导航方式。

在 React 的几乎所有教程和文档中，他们都推荐你创建一个 SPA(单页应用)，那是什么？Well 只是一个可以有内部域名、资源甚至其他 spa 的页面。

**为什么路由在 React？**
SPA 的基本思想是你可以拥有一个完整的可操作的应用程序，只需要一个页面，只需要一次对服务器的调用，不需要改变 url，或者至少不需要消耗一个新的页面。在大多数 React 应用中，你都有一个`Index.js`，他启动一切(redux、sagas 等。)，那么你就有了一个神奇的组件，它决定了你的代码中可以拥有的无限多个组件中的哪一个是应该被渲染的。

这个`Magical Component who decided which component needs to be rendered`就是我们在 React 中可以命名的`Router`。

[![React Router](img/fbe6ea5c5184bcba530ad66bd7d9d0e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--06jNwgUd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/fmratU/Routing_React.png)

拥有一个水疗中心的想法是，你可以在开始时获得你需要的一切，资源，图像，组件等。如果您在页面`books`中，您不应该为`books/id`呈现一个新的整页，只需更改组件，并且如果我们需要信息来呈现页面，可能会在新页面中使用一些您可能需要的信息，而不需要重新加载整个页面。路由器应该提供一些有用的解决方案来帮助我们实施，这些是我们对路由器的最低要求:

*   只需更改组件，而不是重新加载整个页面。
*   状态应该是一样的，改变路线不代表重新开始。
*   能够在我们所有的页面之间移动。
*   路由器应该能够知道何时返回特定页面、默认页面、未找到页面等。
*   应该能够消费查询参数或 url 中提供的任何信息，在最好的情况下作为一个漂亮的对象。

如果我们能够构建或使用能够提供这些功能的东西，我们将能够构建一个良好的应用程序，它能够成长，并如您所愿的那样完整。另外，有很多关于文件结构和导航的讨论，常见的是`domain`在你的代码中给你一个目录级别，你需要小心避免过多的嵌套。这就是为什么一个好的路由定义会帮助你得到一个更好的应用。

**如何在 React 中使用路由器？**
这里有一个棘手的部分，正如我们之前所说的`routing`是我们开发中最重要的部分之一，基本上导航流是赋予我们网站身份的东西，这个流应该是自然的、直观的，并且符合你的商业模式。

那么，我该用什么呢？图书馆？自己造路由器？如果我选择使用一个图书馆，哪一个？如果我找到了库，是什么版本？遗憾的是，如果你是 React 的早期版本，你会有自己的选择，如果你是最新版本，你将不得不使用另一个版本，即使是基于 React 版本的同一个库，你也应该使用最老的或最新的版本。一个站点中的导航可以如此定制，以至于完美的路由器取决于你想要什么。

在 React 的官方[文档中，他们推荐了一个库列表，并附带了一个小小的注释。这在 React 文档中很常见。他们不会在这类主题中给你意见，他们只是给你一些信息，然后他们会告诉你这是一件真正的好事，因为他们不会与任何图书馆联姻，给你找到适合你的信息的自由。](https://reactjs.org/community/routing.html)

但是在几乎所有你可以在网上找到的 React 教程中，他们使用`React Router`作为默认的路由器库，这取决于教程的年份，你会发现他们使用的是一个特定的版本，你需要小心，我不会重复这些教程，我会让你在这里列出我认为当今网络上最好的。

*   Routing React Apps:The Complete Guide，这是最好的和完整的，他谈论了很多关于依赖关系、结构文件、理论概念，并且很好地理解了实现的细节。
*   React 培训 React 路由器，这也是一个很好的例子，因为他们给你带来了 React 路由器提供给我们的几乎所有 Api 的实现和例子。
*   一个简单的 React 路由器 v4 ，这个很棒，因为它更实用，就像上面两个的混合。也是适用于 React 16 的最新版本。
*   [React 路由器教程官方](https://github.com/reactjs/react-router-tutorial)，是官方文档，你可以确定这一个应该是所有中最准确和更新的。

我将带给你们的是一个简单的例子，关于如何自己编码，这个想法是理解所有这些库正在做的事情，不要认为这是一件神奇的事情，没有人理解发生了什么。

首先，您需要了解的是，路由只是一个状态，它将拥有当前路径。在这个例子中，我将使用一个动作来改变当前路径到另一个路径，无论我们传递给这个动作什么，它都将是当前路径。采用的第一条路线是`window.location`中的路线，然后我们只能使用`push`动作改变路线。

```
// Action
import { createAction } from 'redux-actions';

export const push = createAction('PUSH');

// Reducer
import { handleActions } from 'redux-actions';

const { pathname } = window.location

export const routerState = {
    route: 'pathname'
};

export default handleActions({
    PUSH: (state, { payload }) =>
        Object.assign({}, state, {
            route: payload
        })
}, routerState); 
```

Enter fullscreen mode Exit fullscreen mode

减速器只是处理动作，接收新的路径，现在这是当前路径，你可以在这个状态中包含更多的信息，比如历史，当然还有更多的动作，比如`goBack`、`goToTheMainPage`、`goTo404`等等。所有这些都取决于你的导航，对于简单的重定向来说，这就很好了。

之后，我们需要 2 个组件，一个`Router`将处理哪个`Route`应该被渲染的决定，另一个`Route`应该是一个简单的组件，`Router`可以阅读和理解。

```
import { Component, Fragment, createElement } from 'react';
import { connect } from 'react-redux';

// Route
export const Route = ({ component }) => createElement(component, this.props);

// Router
export const Router = ({ children, route }) => <Fragment>
    { children.find(childrenRoute => route === childrenRoute.props.path) }
</Fragment> 
export const mapStateToProps = (state) => ({
    route: state.router.route
});

export default connect(mapStateToProps)(Router); 
```

Enter fullscreen mode Exit fullscreen mode

正如你在这里看到的，`Route`，只返回作为道具传递的组件，`Router`将所有的`Routes`作为子组件，然后只返回与当前路径匹配的组件。当我们连接状态和道具时，`Router`接收当前路径，当有人调度动作`push`时，我们的`Router`将基于此刷新组件，这样我们可以在页面之间导航而不会丢失状态。

**注意:**`Fragment`只是一个我喜欢使用的花哨的 React 组件，给你一个不插入任何元素的包装。
**注 2:** 过滤器很简单，但是你可以根据你想要如何处理你的站点上的导航来提取一个更复杂的函数。
**注 3:** 你也可以在路由器中定义一个默认路由(比如 home，或者 404)当过滤器什么都不会发现。
**注 4:** 此外，本例中的`Route`看起来并没有做任何事情，但是您可以在其中包含更多的逻辑，这些逻辑是您不想包含在真实组件中的，比如对路径的特殊验证，您可能需要的额外道具。这就是为什么我创造了这个`Route`

然后是实现，这里我们有一个`BooksPage`、一个`BooksDetails`和一个`BooksCreate`，它们只是组件，有自己的逻辑，我不想在这个组件中实现任何类型的路由逻辑，在这个示例中，我们可以继续并包括我们想要的任意多的路由，这与 React 路由器实现非常相似，但没有它们提供的所有其他功能和组件，但如果我们的导航不是那么复杂，我们现在就不需要它。

```
export class Index extends Component {
    render() {
        return (
            <Router>
               <Route path=“books” component={BooksPage}/>
               <Route path=“books/{id}” component={BooksDetails}/>
            <Route path=“books/create” component={BooksCreate}/>
            </Router>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要的最后一件事是，如何改变路线？从你的应用程序的任何部分，你都可以用新的路径来分派动作`push`，就像这样:

```
dispatch(push(‘books/create’)); 
```

Enter fullscreen mode Exit fullscreen mode

reducer 处理这个调度，当状态改变时，路由器将更新页面，保持我们的状态，而不刷新整个页面。这个路由器是原始的，也缺少很多功能和逻辑(你需要意识到现有的库已经解决了这些问题)，比如 url 的改变，处理历史，返回到上一页，但是这个实现做了最简单的事情:**在 SPA 中导航**。

希望这有助于您了解什么是路由器，并帮助您决定使用哪个库，以及如何处理他的实现，您也可以尝试自己构建它，并处理您的导航流的所有特定要求。

你需要意识到的是，为你的站点开发一个新的路由器不应该成为重新发明轮子的尝试，如果你决定在他的实现中包含一个现有的库，也不会成为我们的项目中一个依赖于这么多东西的怪物，你完成安装依赖项的依赖项只是为了让一个东西如你所愿地工作。

真的希望你喜欢阅读。下一篇文章再见。

# 密令信托

**上一篇:** [什么，为什么以及如何反应(高阶组件)](https://dev.to/mangel0111/the-what-why-and-how-of-react-high-order-components-3ko1)