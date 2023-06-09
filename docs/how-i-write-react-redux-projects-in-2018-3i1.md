# 2018 年我如何写 React/Redux 项目

> 原文：<https://dev.to/heiskr/how-i-write-react-redux-projects-in-2018-3i1>

我从事 JavaScript 开发大约十三年了。经历过 [Prototype.js](http://prototypejs.org/) 、 [YUI](https://yuilibrary.com/) 、 [MooTools](https://mootools.net/) 、 [jQuery](https://jquery.com/) 、[骨干](http://backbonejs.org/)、 [Angular](https://angular.io/) ，现在 [React](https://reactjs.org/) (以及其他无数的库和工具)。在过去的四年里，我在各种项目中主要从事 React/ [Redux](https://redux.js.org/) 浏览器项目。

本文记录了我在 React/Redux 中构建浏览器项目时使用的**策略**。这些实践是大量试验和错误的结果。在各个团队中达成共识。并观察哪里容易发生问题。我在这里假设你至少读过官方的 [**React 文档**](https://reactjs.org/docs/hello-world.html) 和 [**Redux 文档**](https://redux.js.org/introduction/motivation) 。如果你还没有，先这样做。

这里没有什么是规则；这些是**高级向导**。拿走与你相关的东西，*忽略其他的。每个项目都有例外。有些项目，React/Redux 会[矫枉过正](http://youmightnotneedjs.com/)。(我喜欢简单的 HTML 网站。)或者项目需求太具体，React/Redux 会碍事。我不喜欢这个系统的一切。但是对于大多数 web 项目来说，React/Redux 是一个不错的缺省值。*

**“2018 年”**部分相关。每隔几个月就有一种闪亮的做事方式。我相信这篇文章的 2019 年版本会有很大的不同。

请不要使用本指南来试图推翻团队共识。我从未见过一个项目因为技术选择而失败；但是我确实见过团队不能合作的项目失败。

## 为什么做出反应& Redux

**为什么使用 React？网络浏览器项目有很多选择。React 的一些主要优势:**

*   React 是目前最受欢迎的选项。使用 React 的新项目数量最多。React 将至少在未来 5 年左右继续获得支持。受欢迎也意味着很容易获得帮助，并且有一个支持 React 的大型工具系统。
*   Vue 越来越受欢迎。Vue 使用类似的模型做出反应。也许几年后 Vue 会成为 React 的接班人，但我猜 React 的接班人还不存在。
*   单向数据流是对以前系统的巨大改进。越接近这个模型，代码就越容易使用。
*   虚拟 DOM 是一种性能优势。这是一个更大的维护优势。

还有许多其他的虚拟 DOMs 有些在文件大小上更小，有些更快。但是 React 是成熟的，与 React 的成熟相比，其他的收获都是微不足道的。

**为什么要用 Redux？**状态管理也有很多选择。

*   Redux 是**小**好理解。
*   Redux 是目前最流行的状态管理系统。
*   Redux 有助于保持您对状态**的更改纯粹**，使更改更容易跟踪和理解。
*   Flux，React 最初的状态管理系统，很快变得复杂。

状态管理还有许多其他选择，MobX 可能是最流行的。Redux 的**样板**有问题。大多数其他选项都有处理这种样板文件的特定方式。其他选项可能对您的项目有意义。我敢打赌，对于减少 Redux 样板文件的最佳方式，永远不会有真正的共识。Redux 是一个通用的解决方案。并且尽可能具体，同时确保它在各种情况下都能工作。

小型项目最好只在树中最高的组件上使用 React 的组件状态。您可以稍后切换到 Redux 或另一个系统。

我推荐使用其他一些库。如果你使用 React，你需要为 web 项目准备`react-dom`。 [`react-redux`](https://github.com/reduxjs/react-redux) 是一个很小的库，提供了更多关于如何将 React 和 Redux 集成在一起的指导。对于有多条路线的项目， [`react-router`](https://reacttraining.com/react-router/) 加`react-router-dom`是最受欢迎的选项。它们并不完美，而且在短时间内发生了很大的变化。也就是说，他们已经形成了一个很好的路由模式。

[![The React/Redux flow, from youtu.be/hiaqhI62zZs](img/044067ba774d7c59b35843f542ef3aec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t4tUl_HP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://heiskr.cimg/react-2.jpeg)

## 支配一切的原则

这些年来，我在这些项目中总结出了一些原则。这些原则有助于避免问题。也就是说，从来没有“永远正确的答案”

**先做无聊的方式。**JavaScript 世界里有如此多的工具和库。我的总体建议是，看看你能否用一个基本功能满足要求。这样做，直到你证明这是行不通的。如果你可以使用浏览器中已经内置的东西，那就先试试。如果具体到 React 或 Redux，尝试作者建议的方法，直到你知道这对你的项目不起作用。(如果你可以在没有 JS 的情况下构建你的项目…不要认为你需要 JS！)

**功能>类**。我不是函数式编程纯粹主义者。有时可变数据完美地模拟了问题。当您需要某种类型数据的多个副本，并且每个实例都有自己的属性时，类非常有用。但是，除非您知道在这种情况下需要可变数据或类，否则纯函数是更好的默认设置。函数的编写更简单，测试更容易，阅读更快。

****>**[**干**](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) **。**重复代码通常是不好的。但是重复代码并不像过度抽象的代码或者错误抽象的代码那样糟糕。我采取的方法是让一些重复滑动，直到模式变得明显。大多数糟糕的抽象来自于在模式变得明显之前试图保持干燥。一点点耐心是有价值的。有时，如果每次都有一点点不同，你最好不要碰它。**

 ******>干单源。** Redux 为您提供了一个单一的工作状态。让你的还原状态成为真理的唯一来源比保持干燥更重要。有些时候，你必须在拥有一个单一的真理来源和服从干巴巴的事实之间做出决定。采取单一来源的真理方法。只要你有单一的真理来源，没有完全干燥的痛苦是可以忍受的。**

 ****基数→其他一切→指数。** Index 通常用作目录的“根”文件名。我见过目录中的其他文件依赖于 index 的情况。而在其他情况下，index 消耗其他文件。后者问题较少。对于共享的值和函数，我将把它们存储在一个“基本”文件中。我的其他文件消耗基本文件。那么索引文件会消耗所有其他文件。

[![](img/bb9079816ad84dfc5cba5d770b8e60c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wDK10YdI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://heiskr.cimg/react-3.png)

**名实相符。** JavaScript 默认为骆驼大小写名称。除非你有一个很好的理由，尽可能坚持使用骆驼箱。你希望事物的名称尽可能一致。组件，它的样式，它的文件名，它的 CSS 类名，它的相关选择器…都应该使用相同的名字。他们不排队的时候很混乱。

## 配置本地开发

几乎不用说，从第一天起就使用 **Git** 进行版本控制。

我会从 [`**create-react-app**`](https://github.com/facebook/create-react-app) 开始。除非您没有公司样板文件或其他需要开始的配置。CRA 的作者们也在研究 React。CRA 是一个简单的开始。如果您的项目以后需要，还有一个简单的退出机制。这些年来我见过很多样板系统，CRA 是我见过的最好的。 [`next.js`](https://github.com/zeit/next.js/) 可能对你的项目也有意义。如果你没有使用样板文件，你需要自己安装和配置 [Babel](https://babeljs.io/) 和 [Webpack](https://webpack.js.org/) 。

除了快速演示项目，我建议使用 [`**eslint**`](https://eslint.org/) 。`eslint`附带的静态分析工具可以与大多数静态类型语言相媲美。我通常选择 AirBnB 规则集——React 项目使用 full，否则使用 base。而且我还在 [`prettier`](https://prettier.io/) 中添加了例外。还有其他配置可能更适合您的项目。

```
{
  "extends": ["airbnb", "prettier", "prettier/react"],
  "env": {
    "node": true,
    "browser": true,
    "jest": true
  },
  "parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  }
} 
```

说到`**prettier**`* *……* *比和队友争论代码风格好多了。并且它减少了您必须处理的 lint 警告的数量。我一直用分号自由体。实际上，分号样式几乎没有任何区别。

```
semi: false
singleQuote: true
trailingComma: es5 
```

即使你没有使用 React/Redux，我也会推荐你使用 [`**lodash.get**`](https://www.npmjs.com/package/lodash.get) **。**沉重地。JavaScript 中的分支覆盖可能会成为一个真正的问题。不得不编写和测试每一个可能的分支将会使你的测试更加脆弱。JavaScript 通常运行在一个几乎任何事情都可能发生的环境中。很少有任何保证。`lodash.get`是帮助应对这一现实的工具之一。

```
// before lodash.get
const userEmail = state &&
                  state.users &&
                  state.users[userId] &&
                  state.users[userId].email ||
                  ''

// after lodash.get
const userEmail = get(state, ['users', userId, 'email'], '') 
```

今天有许多**造型**工具可供选择。这几年和我合作最多的是[`sass`](https://sass-lang.com/)[`stylus`](http://stylus-lang.com/)， [`glamor`](https://github.com/threepointone/glamor) 。每个项目在这里都有不同的要求，所以我不能提出一个通用的建议。如果你正在开始一个新项目，我会放入 [`normalize.css`](https://necolas.github.io/normalize.css/) ，然后有一个单独的 CSS 文件。当文件变得太大时，您可以更改为其他内容。如果你的项目没有太具体的设计要求，你也可以看看完整的 CSS 系统，如[超光速粒子](https://tachyons.io/)或[自举](https://getbootstrap.com/)。

**本地化**并不是一个已经解决的问题。内置的 [`window.Intl`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) 浏览器本身就能让你走得很远。

## 目录结构

我的**目录结构**看起来会像这样:

*   `README.md`
*   `package.json`
*   `client.js`
*   `server.js`
*   `state/`
*   `data/`
*   `services/`
*   `middleware/`
*   `selectors/`
*   `views/components/`
*   `views/containers/`
*   `views/routes/`
*   `constants/`
*   `helpers/`
*   `images/`

## 根文件

每个项目都应该有一个 [**自述**](https://en.wikipedia.org/wiki/README) 。您的自述文件有两个目的:a)为开发人员提供文档；b)营销。我建议您的自述文件至少包含…

*   项目的名称。
*   简短的描述。(谁在使用它，他们用它做什么，他们为什么要使用它。)
*   截图，如果相关的话。
*   主分支的生成状态。
*   链接到您的项目使用的工具和 API 的文档。
*   项目中的目录列表，以及它们的预期用途。
*   顶级`npm` / `yarn`命令及其作用的列表。

您的样板文件可能包含也可能不包含供您使用的 [`**package.json**`](https://docs.npmjs.com/files/package.json) 。JS 社区过去常常发出咕噜声，然后咽下去。但是现在似乎有一个共识，使用`package.json.`的`scripts`部分，你将拥有开发、构建和测试命令。`package.json`现在也是你的工装配置之家。我可以就这一个文件写一整篇文章，所以对于这篇文章，这就是我要说的关于`package.json`的全部内容。

```
{
  "name": "messages",
  "scripts": {
    "build": "# ...",
    "start": "# ...",
    "test": "# ...",
  },
  "jest": {
    ...
  },
  "dependencies": {
    ...
  },
  "devDependencies": {
    ...
  }
} 
```

如果你的项目只是客户端——没有 [SEO](https://en.wikipedia.org/wiki/Search_engine_optimization) 要求——那么你的应用程序的单个`client.js`条目就可以了。否则，你需要一个`**client.js**`和一个`**server.js**`。

`**client.js**`将各自负责:

*   创建您的 Redux 商店，
*   集成您的 Redux 中间件，以及
*   将您的商店与来自`react-redux`的 [`Provider`](https://github.com/reduxjs/react-redux/blob/master/docs/api.md#provider-store) 绑定，并
*   将 ReactDOM 绑定到实际的 DOM。

如果你用的是 React 路由器，那么 [BrowserRouter](https://reacttraining.com/react-router/web/api/BrowserRouter) 就会在这个文件里。

```
const store = createStore(reducer, applyMiddleware(logger))

document.addEventListener('DOMContentLoaded', () => {
  const container = document.querySelector('#react-root')
  ReactDOM.render(
    <Provider store={store}>
      <Router>
        <Messages />
      </Router>
    </Provider>,
    container
  )
}) 
```

*(本文所有代码示例都是为了演示。他们没有生产体验所需的一切。)*

您的`**server.js**`文件需要

*   在每个请求上创建一个新的 Redux 存储*，这也意味着将中间件绑定到存储。*
*   您需要在这里进行任何必要的服务调用来生成 Redux 状态。
*   如果你使用的是 React 路由器，那么 [StaticRouter](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/api/StaticRouter.md) 就会在这个文件中。
*   您还需要基本的 HTML 标记。
*   然后你将把完成的状态和初始的 React HTML——用 [`react-dom/server`](https://reactjs.org/docs/react-dom-server.html) 渲染——放到你的 HTML 模板中。

对于实际处理请求，我会推荐使用 [Express](https://expressjs.com/) 。 **Express** 易于使用，支持良好，非常适合这种情况。

```
const app = express()

const html = `
<!doctype html>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
Messages
<link rel="stylesheet" href="/index.css">
<body>
<div id="react-root">{innerHtml}</div>
<script>window.preload={state}</script>
<script src="https://cdn.polyfill.io/v2/polyfill.min.js"></script>
<script src="/index2.js"></script>
</body>
`

app.get(/.*/, (request, response) => {
  const store = createStore(reducer, applyMiddleware(logger))
  // once the store is filled of data, then...
  const myContext = {}
  const innerHtml = ReactDOMServer.renderToString(
    <Provider store={store}>
      <Router location={request.url} context={myContext}>
        <Messages />
      </Router>
    </Provider>
  )
  if (myContext.url) {
    response.redirect(myContext.url)
  } else {
    response.status(200).send(
      html.replace('{innerHtml}', innerHtml)
          .replace('{state}', JSON.stringify(store.getState()))
  }
})

app.listen(3000, () => {
  console.log('serving app realness')
}) 
```

**服务器端渲染** React/Redux 很难，超过了我在本文中所能描述的范围。服务器端渲染可能会出很多问题。如果你不需要做服务器端渲染，我就不会。另外，有几篇关于如何使用 React 进行服务器端渲染的文章。规则是你需要为每个请求创建一切*…除非你正在缓存。*

 *如果你有任何需要在服务器和客户端都有的**配置**，比如 API 端点 URL 或者图像基路径，我会在`server.js`中有。使用内联脚本标记通过 HTML 文档向浏览器公开它。这样，您的所有配置都在一个地方。

如果您的需求开始在服务器端扩展，我会推荐使用 [Express 中间件](https://expressjs.com/en/guide/using-middleware.html)模式。这有助于在服务器端保持代码的小型和模块化。

跨浏览器支持在过去几年中变得更加容易，但是要知道每个浏览器将支持什么功能仍然有困难。 [**polyfill.io**](https://polyfill.io/v2/docs/) 是解决这个问题的一个简单方法。你在你自己的脚本之前添加一个脚本标签到你的 HTML，你就完成了。强烈推荐。

## 状态

**状态**是 React/Redux 项目的核心。如果您掌握了 Redux 状态，应用程序的其余部分将从那里开始。如果你做错了，你将花费大量的时间在下游试图纠正问题。

将 Redux 状态想象成 SQL 数据库。有三个主要指南:

1.  **没有重复数据。**
2.  **无衍生数据。**
3.  **尽可能平坦。**

我所知道的遵守这三个指南的最简单的方法是写出一个状态模式。您可以在您的`state/`目录中有一个描述整个状态树的键和默认值的文件。您还可以添加注释来描述这些字段是什么。如果你想更进一步，你也可以在你的状态模式中添加验证规则，尽管我认为这通常是不必要的。

如果您在两个不同的集合中有相同的字段，那么您应该将共享字段分离到它们自己的集合中。这避免了**重复**数据。

**衍生**数据是你可以从你已经拥有的一个或多个其他数据中推断出来的数据。例如，如果你有一个`firstName`字段和一个`lastName`字段，你不需要一个`fullName`字段。

当你有嵌套数据时，以后就很难处理了。只要您没有创建重复的或派生的数据，您就希望尽可能地将您的模式扁平化。

我构建状态的方式是这样的:

```
module.exports = {
  global: {
    userId: '',
  },
  users: {
    '[id]': {
      name: '',
      email: '',
    },
  },
  messages: {
    '[id]': {
      toUser: '',
      fromUser: '',
      content: '',
    },
  },
  requests: {
    '[id]': {
      endpoint: '',
      success: false,
      failed: false,
    }
  }
} 
```

我的状态树有两种类型:**字段**和**集合**。

我有一个`global`部分，其中包含我所在州的平坦**区域**。它们只存在于单个层面。这些字段不属于集合。通常它们是标识符。

然后我会有我的**系列**。每个集合都是由标识符存储的对象的对象。这些集合应该主要是平面数据。如果一个集合拥有另一个集合，那么这两个集合应该是两个独立的集合。并由标识符连接。想象一下如何在 SQL 数据库中建模关系，这就是你想要遵循的模式。如果一个集合没有自然标识符，你需要用类似于[uuid](https://en.wikipedia.org/wiki/Universally_unique_identifier)的东西来生成它们。

[Tal Kol](https://medium.com/@talkol) 有[两篇](https://hackernoon.com/avoiding-accidental-complexity-when-structuring-your-app-state-6e6d22ad5e2a) [伟大的](https://hackernoon.com/redux-step-by-step-a-simple-and-robust-workflow-for-real-life-apps-1fdf7df46092)文章深入到状态树设计中。我推荐阅读这些。只要你遵循以上三条规则，你可以用任何对你有意义的方式来建模你的状态。

您的状态模式也可以作为您的*默认状态*。您只需要一个将集合清空为普通对象的函数。然后你可以用它来启动你在`client.js`和`server.js`的 Redux 商店。您将状态模式与代码集成得越多，它就越有可能保持准确。

```
function createDefaultState(schema) {
  return Object.keys(schema).reduce(
    (sum, key) => {
      sum[key] = isCollection(sum[key]) ? {} : schema[key]
      return sum
    },
    {}
  )
} 
```

为了编写你的 [**减速器**](https://redux.js.org/basics/reducers) ，围绕[鸭子模式](https://github.com/erikras/ducks-modular-redux)和[通量标准动作](https://github.com/redux-utilities/flux-standard-action)已经有了越来越多的共识。我会推荐使用那些。而你的顶级减速器可以是 Redux 的 [`combineReducers`](https://redux.js.org/api/combinereducers) 。

我参与过的项目中，每个领域和集合都有自己独特的一套动作。我还参与过一些项目，在这些项目中，每个字段和集合都有相同的操作集，并且都以相同的方式工作。**一致的**方法更容易使用。对于我的每个“全局”平面字段，我将有两个动作:*设置*和*重置。*该设置可以使用任何值，而*复位*会将该值恢复为默认值。对于我的收藏，我有七种动作类型:*添加一个*，*添加多个*，*更新一个*，*更新多个*，*移除一个*，*移除多个*，*重置*。在不同的集合中，这些都以完全相同的方式工作。我喜欢正好有那七个，所以我总是知道每个集合能做什么而不用查我的减速器。

```
// state/global.js
function globalReducer(state, action) {
  if (action.type === SET_USER_ID) {
    return { ...state, userId: action.payload }
  }
  if (action.type === RESET_USER_ID) {
    return { ...state, userId: schema.global.userId }
  }
  return state
}

// state/users.js
function usersReducer(state, action) {
  if (action.type === ADD_USER) {
    return { ...state, [action.payload.id]: action.payload }
  }
  // ... ADD_USERS, UPDATE_USER, UPDATE_USERS, ...
  // ... REMOVE_USER, REMOVE_USERS ...
  if (action.type === RESET_USERS) {
    return {}
  }
  return state
}

// state/index.js
const reducer = combineReducers({
  global,
  users,
  messages,
}) 
```

我让我所有的动作创建器都一样，使用这种函数:

```
const createAction = type => (payload, meta) =>
  ({ type, payload, meta })

const setUserId = createAction(SET_USER_ID)

dispatch(setUserId('abcd1234')) 
```

如果您选择一致的操作，您将看到 Redux 生成的大量**样板文件**。我会等一会儿，直到模式完全清晰，然后再尝试擦干它，甚至一次一点点。

旁注:我和 [`immutable.js`](https://facebook.github.io/immutable-js/) 没有任何正面的经历。对于单个贡献者来说，记住普通数据和不可变数据是很困难的。对一个团队来说是不可能的。如果你在考虑那种方法， [`immer`](https://github.com/mweststrate/immer) 似乎更合理一点。

*更新/编辑 2018 年 10 月 10 日:*查看 [Redux 入门套件](https://github.com/reduxjs/redux-starter-kit)和我的 [Redux Schemad](https://github.com/heiskr/redux-schemad) 。

## 测试数据

拥有好的**测试数据**是加快几乎任何项目开发时间的最有效方式。怎么推荐都不为过。您可以使用测试数据进行快速的本地开发。编写单元测试也将更加容易。

在我的项目中，我有一个包含两种类型的`data/`目录:(a)完全写出的测试冗余**状态**和(b)来自我将使用的所有端点的示例**响应**。

我将有至少四个 Redux 测试状态。

1.  一个‘T0’everythingstate，表示尽可能多的事物组合。
2.  一个 **null** 或零状态，这是用户到达时的第一个状态。
3.  一个**加载**状态，显示服务加载时页面的样子，以及
4.  一个**错误**状态，显示每个服务出现错误时会发生什么。

随着您的项目变得越来越复杂，您可能会需要更多的这些状态以及其他类型的测试状态。

我喜欢有一种简单的方法来加载每个测试状态，并在测试状态上呈现*反应树。一个好的方法是使用查询字符串参数。这将使开发项目的反应方面非常快。并对您的视图进行单元测试。*

我喜欢在项目中使用的每个端点至少有一个样本。这些示例将使格式化服务响应和相应的单元测试变得非常容易。您还可以使用您的示例服务响应来验证真实的服务是否与您期望的数据格式相匹配。我也见过一些基于样本服务响应的模拟服务。大多数项目都不需要模拟服务，但是模拟服务是一个选项。

## 服务

有很多方法可以发出**网络请求**。首先，直截了当的 [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 可能对你有用。除此之外，我看到越来越多的人认同浏览器内置的、基于承诺的 [`window.fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 。如果你需要服务器端渲染，你可以找一个包让`fetch`也在服务器上运行。(在[`async`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)[/](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)[`await`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)上):在有更多的浏览器支持之前，它的传输量很大……我还不会在浏览器中使用它。)

在我的服务目录中，每个端点家族都有一个文件(`GET` / `POST` / `PUT` / `DELETE`)。或者，如果有一个以上的服务有多个端点，我会为每个端点创建一个文件。

对于每个端点，我有三个函数。最高层是实际的服务调用。我将函数命名为`getMessage`、`listMessages`、`postMessage`、`putMessage`或`deleteMessage.`，格式为`{method}{noun}`。这里的模式是:

1.  **将**状态数据格式化为**请求的参数**(可选)。
2.  **更新**状态以记录我正在向该服务发出**请求**。
3.  **提出**请求，然后…
4.  如果请求成功，**格式化**的**响应。**然后分派一个动作，让**用来自响应的格式化数据更新**状态。
5.  无论哪种方式，**更新**中的**请求**的状态，以记录请求完成。

```
function getMessages(dispatch, getState) {
  const params = formatGetMessagesRequest(getState())
  const id = uuid4()
  dispatch(addRequest({ id, endpoint: GET_MESSAGES }))
  return fetch('/messages', params)
    .then(response => response.json())
    .then((response) => {
      const payload = formatGetMessagesResponse(response)
      dispatch(addMessages(payload))
      dispatch(updateRequest({ id, success: true }))
    })
    .catch(() => {
      dispatch(updateRequest({ id, failed: true }))
    })
} 
```

格式化请求和格式化响应可能会变得冗长。所以我会把它们分成不同的功能。例如:

*   我有一个最高的函数，`getMessage`。
*   我有一个函数叫`formatGetMessageRequest`。那个*带*冗余**状态**和其他参数。并且*返回*一个格式化的**参数**的对象来发出请求。我用的格式是`format{method}{noun}Request`。
*   我有一个函数叫做`formatGetMessageResponse`。那个*拿*一个 API **响应**。并且*返回*格式化的响应以匹配我的**状态。这样我就可以分派动作来更新我的 Redux 状态。我用的格式是`format{method}{noun}Response`。**

```
function formatGetMessagesRequest(state) {
  return {
    headers,
    body: {
      userId: get(state, 'global.userId'),
    },
  }
}

function formatGetMessagesResponse(response) {
  return get(response, 'messages', []).map(message => ({
    toUser: get(message, 'toUser', ''),
    fromUser: get(message, 'fromUser', ''),
    content: get(message, 'content', ''),
  }))
} 
```

我见过有人使用 [Normalizr](https://github.com/paularmstrong/normalizr) 来更系统地处理格式化。我还没有遇到过需要工具来处理这种情况的项目，但这也是一种选择。

提出服务请求可能是你项目中最混乱的部分。只要你期望服务电话有点乱，你就会开心得多。

## Redux 中间件

所以现在你有一个状态树，你有服务功能。但是你怎么知道**什么时候**调用你的服务呢？或者发送分析事件？

一些开发人员使用生命周期方法对本地组件状态做出反应。如果你已经决定你的项目足够复杂，可以使用 Redux，*我建议不要这样做*。React 的生命周期方法和本地组件状态是，嗯…状态。并且你已经在 Redux 中有了你的状态。因此，如果您使用生命周期方法或本地组件状态，您就违反了“单一事实来源”原则。因此，很难确定你的项目在做什么，或者为什么你会看到你所看到的结果。特别是当你在一个团队中时，如果所有地方都有状态，那么很难让所有的东西都协同工作。

更好的解决方案是使用 Redux 来**存储**服务和视图的所有必要状态。并使用 Redux **中间件**基于其他状态变化进行状态变化。

当您想要使用 React 的本地组件状态和生命周期方法时，有一些例外:

*   您正在构建一个**可重用组件**供其他项目使用。其他项目可能会也可能不会使用 Redux。即使一个项目正在使用 Redux，他们也不会以同样的方式使用 Redux。
*   您需要优化特定组件的**性能**。
*   将特定组件的所有状态放入 Redux 和 Redux 中间件将需要大量**非显而易见的工作**。例如，我在工具提示和滚动行为中看到过这种情况。

如果您不属于这三类，我会坚持使用 Redux 和 Redux 中间件来满足您所有的状态管理需求。

那么我们如何编写 Redux 中间件呢？有时候直接的方法是最好的。Redux 的作者选择中间件使用 [currying](https://en.wikipedia.org/wiki/Currying) 模式，所以我推荐熟悉 currying。

考虑级联状态变化有两种主要方式。一种方法是在我们分派动作时监听它们，并调用匹配的函数。热门图书馆 [`redux-saga`](https://redux-saga.js.org/) 就属于这一类。

```
const serviceMiddleware = store => next => action => {
  const result = next(action)
  if (action.type === GET_MESSAGES) {
    getMessages(store.dispatch, store.getState)
  }
  return result
} 
```

另一种方法是您分派一个为您分派其他操作的函数。`redux-thunk`就属于这一类。Redux 的大多数其他“异步”库也是如此。

```
const thunkMiddleware = store => next => action => {
  if (typeof action === 'function') {
    return action(store.dispatch, store.getState)
  }
  return next(action)
}

dispatch((dispatch, getState) => {
  getMessages().then(() => {
    dispatch(setMessagesFetched(true))
  })
}) 
```

很多项目都在使用 [`redux-thunk`](https://github.com/reduxjs/redux-thunk) 。当您分派一个函数而不是一个普通对象时，中间件调用这个函数。该函数可以访问存储方法`getState`和`dispatch`。`redux-thunk`提供了访问权限。否则，它没有提供如何进行这些状态更改的指导。对于单个贡献者来说，这很好。对于一个团队来说，这可能是有问题的。

我见过两种方法——“先听后做”和“多做”——都奏效。我更喜欢“先听后做”的方法。我的理由是:

*   您可以将几乎所有的状态管理排除在您的视图之外。视图只知道如何分派简单的动作，其他什么都不知道。
*   你最终会得到许多小的、容易测试的功能，而不是难测试的大功能。
*   将您的状态更改保存在一个地方更容易。因此，更容易看到变化的**流程**。

我自己的做法是使用我的 [`redux-listen`](https://github.com/heiskr/redux-listen) 库。也就是说，它一点也不流行。我发现`redux-saga`对大多数项目来说很沉重，而`redux-thunk`提供的指导太少。您也可以轻松地编写自己的 Redux 中间件，这对您的项目很有意义。在这里我不能做一个笼统的推荐，除了要看项目。

## 选择器

[![](img/e55e09cd6311c0e123a85fd2567abb7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_CxdqR17--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://heiskr.cimg/react-4.png)

您现在有了一个没有重复或派生数据的漂亮的状态。在某些时候，您需要获取状态中的数据并呈现出视图。也就是说，您的状态的格式可能不是您在视图中想要的格式。

开发人员通常做的第一件事是视图中的所有格式。

```
function Messages(state) {
  return (
    <div id="Messages">
      { Object.keys(state.messages).map(messageId => (
        <ul>
          <li>From: {state.users[state.messages[messageId].fromUser].name}</li>
          <li>{state.messages[messageId].content}</li>
        </ul>
      )) }
    </div>
  )
} 
```

但是很快，这个视图有太多的格式和逻辑，很难阅读或推理。下一步是使用来自`react-redux`的 [`connect`](https://redux.js.org/basics/usagewithreact) 并拥有一个`mapStateToProps`功能。`mapStateToProps`获取完整状态并返回一个将作为视图道具的对象。你可以将你的数据重新格式化成`mapStateToProps`。现在您有了一个易于阅读的视图和一个单独的格式化功能。

```
function Messages({ messages }) {
  return (
    <div id="Messages">
      { messages.map(message => (
        <ul>
          <li>From: {fromUserName]}</li>
          <li>{content}</li>
        </ul>
      )) }
    </div>
  )
}

function mapStateToProps(state) {
  return {
    messages: Object.keys(state.messages).map(messageId => ({
      fromUserName: state.users[state.messages[messageId].fromUser].name,
      content: state.messages[messageId].content,
    }))
  }
} 
```

然而，新的问题是您有许多格式和逻辑重复的`mapStateToProps`函数。解决这个问题的方法是使用**选择器**。选择器是获取状态并返回导出值的函数，例如`state => value`。您也可以组合选择器。大多数开发人员会以单词`get`开始他们所有的选择器。您可以在您的`mapStateToProps`中调用这些选择器，现在您有了一个几乎没有格式或逻辑的视图。

```
function getMessagesNaive(state) {
  return get(state, 'messages', {})
}

function getMessagesArray(state) {
  const naive = getMessagesNaive(state)
  return Object.keys(naive).map(id => { ...naive[id], id })
}

function getMessages(state) {
  const users = getUsers(state)
  const messages = getMessagesArray(state)
  return messages.map(message => ({
    ...message,
    fromUserName: get(users, [message.fromUser, 'name'])
  }))
}

function mapStateToProps(state) {
  return {
    messages: getMessages(state),
  }
} 
```

如果你愿意，你可以使用 [`reselect`](https://github.com/reduxjs/reselect) 库来帮助组合选择器。您不一定要使用这个库，但是我建议至少阅读它的 README 来掌握模式。一旦你有了很多选择器，这个库将会对你更有用。

以下是您希望从视图中移除并放入`mapStateToProps`或选择器中的内容列表:

*   `.length`
*   `+`、`-`、`*`、`\`、`%`、`**`、`in`、`<`、`>`、`≤`、`≥`、`==`、`!=`、`===`、`!==`、`&&`、`||`、`?:`
*   任何决定逻辑的函数，比如`RegExp.test`
*   任何重新格式化数据的函数，如`map`或`filter`
*   …有时`!`

对于只进行数字格式化或本地化的功能，我将把它们留在视图中。

“有时候！”注释就是你要把任何布尔值命名为正数。所以不要在布尔名字中使用`not`、`un-`、`hide`或者任何[否定](https://softwareengineering.stackexchange.com/questions/196830/boolean-method-naming-affirmative-vs-negative)的东西。比如`!hideSection`就比较混乱。

(名称选择器有些问题，因为 Redux/Redux 选择器不同于 HTML/CSS 选择器。这是浏览器开发的一个过载术语。)

我建议按路线将你的选择器分组。因此，您将拥有一个跨路由选择器的“基础”文件。每条路线一个选择器文件。以及可以组合来自不同路由的选择器的“索引”选择器文件。

## 视图:页面、容器、&组件

您现在终于进入了使用 React 的应用程序部分！如果到目前为止您已经类似地设置了一切，那么这一部分是轻而易举的。

重复一遍:如果你在使用 Redux，我建议你几乎总是写 [**无状态功能组件**](https://reactjs.org/blog/2015/09/10/react-v0.14-rc1.html) 。(参见“中间件”一节中的三个例外。)如果你不属于这三种例外之一，那么我会建议你编写简单的、只支持道具的函数。这样，你所有的状态信息都在 Redux 和 Redux 中间件中。你只有一个真实的来源。您的项目变得非常可预测，并且很容易看到为什么您会随时看到您正在看到的视图。

您可以从一个平面目录中的所有视图开始。随着项目的增长，我喜欢将它们分成三种类型的视图:

*   **组件**:简单、功能性、道具专用组件。我们还没有直接连接到州或路线。
*   容器(Containers):我们已经附加到状态的组件，但是没有直接附加到路由。(注意:如果您有几个路由，您可能没有任何容器！)
*   **Routes** :我们已经附加到一个路由上的组件，可能还直接附加到状态上。(如果您没有使用路由器，那么您可能只有一条路由。)

路线可以使用任何视图。容器可以使用任何容器和组件。并且组件只能消耗其他组件。

```
function Component(props) { return <div /> }

function Container(props) { return <div /> }
const mapStateToProps = state => ({})
export default connect(mapStateToProps)(Container)

function Route(props) { return <div /> }
const mapStateToProps = state => ({})
export default connect(mapStateToProps)(Route)
// ... a higher-level file
<Route path="/messages" component={Route} /> 
```

`react-redux`将在集装箱和航线上发挥作用。你想使用`connect`和`mapStateToProps`来连接你的状态和你的视图。这使得视图函数本身看不到这些内容。 [`mapDispatchToProps`](https://github.com/reduxjs/react-redux/blob/master/docs/api.md) 会帮你绑定你的动作创建者到你的店铺。在某些地方，我还必须创建一个`mapPropsToProps`来保存我的选择器、逻辑和格式化视图。

**是 React 最有争议的部分。我推荐使用 JSX。我不喜欢 HTML 和 JS 的混合。我一直觉得很奇怪，即使几年后。但大多数其他项目将使用 JSX。JSX 周围的工具是壮观的。通过使用 JSX，您可以获得相当多的低投入。我希望 React 的作者选择一种直接的 JavaScript 方式来处理模板。但是这个决定已经成为过去。**

 **我参与过一些项目，这些项目试图使用表单库，或者只使用 React，或者同时使用 React 和 Redux。除非您的表单是基本的，否则我建议不要使用专门的表单库。表单库通常会增加很多开销，但收效甚微。我将坚持 React 的作者建议如何做 [**受控组件形式**](https://reactjs.org/docs/forms.html) **。等到你有了一个清晰的复制模式，再去做任何事情。**

## 辅助物:常量、辅助物、&图像

我的项目中有辅助目录:常量、助手和图像。有时也有更多的辅助目录。

**常数**帮你避开[魔弦](https://en.wikipedia.org/wiki/Magic_string)。常数对项目将具有的潜在值进行编码。如果你使用常量而不是神奇的字符串，`eslint`可以在重构的时候帮你不少忙。强烈推荐。

**助手**是不适合模式其他部分的一次性函数。它们总是特定于您的项目。我建议在创建任何助手之前，检查一下`npm`地是否有你可以使用的包。尤其是`lodash`。(lodash 的一个提示…你可以安装每个功能，而不是全部。在浏览器中保存文件大小。)我会尽量少找帮手，但不能少。

对于需要它的项目，我会有一个包含**图像**的目录。除了*优化你的图片*之外，关于图片就不多说了。通过优化图像，您将获得比其他任何方式都更多的性能提升。尝试使用 SVG，减少颜色数量，使用 [ImageOptim](https://imageoptim.com/mac) 。如果只有少量矢量 y 的图像，base64 编码它们。这将节省网络请求的数量。有很多文章都是关于用图片节省尺寸的。

## 测试

我用过茉莉，摩卡/柴/西农，Jest。相似的比不相似的多。如果你已经有了，我不会换的。也就是说，对于新项目，我推荐 **Jest** 。它是最容易设置和开始使用的，并且内置了单元测试所需的一切。

Jest 的杀手锏特性是 [**快照测试**](https://jestjs.io/docs/en/snapshot-testing) 。不必断言对象的每个值或 JSX 节点，你可以写:

```
expect(result).toMatchSnapshot() 
```

第一次运行该测试时，它会对结果进行快照。如果您稍后运行测试，并且它不是相同的，您将看到两个快照的不同之处。如果您想要更新快照，可以在命令行上轻松完成。

我不能推荐使用足够的快照测试。特别是，这里有一些你肯定想使用快照测试的地方:

*   您的减速器在`state/`中的结果
*   `services/`中的请求和结果格式化程序(如果您有好的测试数据，这些可以是一行测试！)
*   您可以在您的服务呼叫中拍摄被模仿的`dispatch`的快照。它会记录我们每次调用模拟函数时的参数和返回值！
*   在你的`middleware/`中使用`getState`的任何时候。嘲笑和抓拍`dispatch`的把戏在这里也管用！
*   如果你同时使用测试数据和快照测试，你的大多数测试可以是一行。
*   如果你一起使用你的测试数据和快照测试，你的大多数`view/`测试也可以是单行的。您可以对`mapStateToProps.`进行一个测试，对将所有测试结合在一起的视图进行另一个测试。

```
// Reducer test assertion
expect(
  messagesReducer(allState.messages, addMessage(payload))
).toMatchSnapshot()

// Request formatter assertion
expect(formatGetMessagesRequest(allState)).toMatchSnapshot()

// Response formatter assertion
expect(formatGetMessageResponse(getMessagesSample)).toMatchSnapshot()

// Service assertion
const dispatch = jest.fn()
const getState = () => allState
await getMessages(dispatch, getState)
expect(dispatch).toMatchSnapshot()

// Selector assertion
expect(getUsers(allState)).toMatchSnapshot()

// View assertions
expect(mapStateToProps(allState)).toMatchSnapshot()
expect(
  Messages(mapStateToProps(allState))
).toMatchSnapshot() 
```

通过简单的函数、测试数据和快照测试，您的单元测试几乎可以自己编写。

对于其他测试，您将需要使用 Jest 的 [**模拟**](https://jestjs.io/docs/en/mock-functions) 功能。如果你不熟悉 mock 的内部原理，我有一个[视频](https://www.youtube.com/watch?v=wUxmVL998FU)展示它们是如何工作的。Jest 的系统可以处理大约 90%的典型嘲讽需求。您可以模仿单个函数和整个模块。有时候，老办法也很管用:

```
test('updates correctly', () => {
  const prevLocation = window.location
  location = { href: 'https://example.com' }
  expect(something).toBe(another)
  window.location = prevLocation
}) 
```

最常被嘲笑的事情之一是[时间](https://github.com/facebook/jest/issues/2234)。尤其是对于快照，您希望您的测试以确定性的方式运行。

我使用的另一个策略是使用`call`和`apply`来覆盖`this`上下文。这种策略避开了许多棘手的测试用例。

```
test('componentDidMount', () => {
  const myThis = {}
  const result = Component.prototype.componentDidMount.call(myThis)
  expect(result).toMatchSnapshot()
}) 
```

给定快照测试，我发现达到 100%单元测试**覆盖率**非常容易。如果更难，我不会推荐大部分项目 100%覆盖。但是在这种组合中，这个目标在大多数时候是可行的。如果有你不能覆盖的情况，你总是可以把它单独放在一个文件中，并配置 Jest 忽略那个文件。

您可以执行以下操作之一:

1.  在根级别创建一个大的测试目录。
2.  在您拥有的每个主目录中创建一个测试目录。
3.  在每个文件旁边创建一个测试文件。

我更喜欢 3。每个文件夹中有两倍多的文件，但是这样开发会更容易处理这些文件。

## 总结起来

我无法在一篇文章中涵盖构建 React/Redux 项目所需的所有内容。我已经涵盖了这些年来我看到的一些模式。这些是我避免我经历过的最常见问题的方法。在这篇文章中，我还没有涉及到写作风格。或者设置支持生产体验所需的工具。

*个人提示*:我希望有一天浏览器能内置高效的 DOM 元素更新。类似于…

```
rootElement.updateNodes(newDetachedDomTree) 
```

这将减少对我在本文中提到的许多工具的需求。当一个库变得足够流行时，浏览器随后就有办法在本地做同样的事情。我希望这也发生在这里。

*重申*:拿走与你相关的东西，忽略其余的。每个项目都有例外。你可能不需要 JS，或者 React/Redux！本文为 2018 年。如果你在晚些时候读到这篇文章，这个社区可能已经改变了一些内容。

**感谢阅读。** *欢迎反馈！*

可以访问我这边项目 [**萨格菲**](https://sagefy.org/) **。*********