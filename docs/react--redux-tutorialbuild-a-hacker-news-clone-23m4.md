# React & Redux 教程—构建一个黑客新闻克隆

> 原文：<https://dev.to/treyhuffine/react--redux-tutorialbuild-a-hacker-news-clone-23m4>

#### 使用 Redux 和样式化组件构建一个生产 React 项目。使用 GitHub 页面部署应用程序。

这是 [gitconnected Hacktoberfest 项目](https://gitconnected.com/hacktoberfest)的分步解决方案。

在本教程中，我们将构建一个生产质量的黑客新闻克隆。我们将介绍初始化应用程序、添加 [Redux](https://redux.js.org/) 来管理状态、在 React 中构建 UI 以及将解决方案部署到 GitHub 页面的步骤。我们将使用[样式化组件](https://www.styled-components.com/)对应用程序进行样式化，并使用 [axios](https://github.com/axios/axios) 库调用公共[黑客新闻 API](https://github.com/HackerNews/API) 。

[**GitHub —查看来源**](https://github.com/gitconnected/hacker-news-reader)

[**下载黑客新闻克隆作为 Chrome 扩展(太牛逼了！)**](https://chrome.google.com/webstore/detail/hacker-news/hknoigmfpgfdkccnkbfbjfnocoegoefe?pli=1&authuser=1)

[![](img/23163fc43457a4e59699c354f85a85f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F0N2Qthb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AUfu70cs08PyXKaM2Wb6D0w.png)

如果你喜欢视频，你也可以在我们的 YouTube 上跟随这个教程。

[https://www.youtube.com/embed/oGB_VPrld0U](https://www.youtube.com/embed/oGB_VPrld0U)

### 初始化项目

我们将使用 [create-react-app](https://github.com/facebook/create-react-app) 来启动项目。这允许我们构建 React 应用程序，而不用担心配置。首先，确保你已经安装了 create-react-app。

```
npm i -g create-react-app 
```

通过运行下面的命令初始化您的项目。create-react-app 安装了构建 react 应用程序所需的所有基本包，并提供了管理开发和生产构建的默认脚本。

```
create-react-app hn-clone
# Wait for everything to finish... 
cd hn-clone 
```

现在让我们安装我们将需要的核心包。我用的是纱线，但是如果你有 npm，只要把纱线换成 npm 就可以了。

```
yarn add redux styled-components react-redux redux-logger redux-thunk axios 
```

create-react-app 使用 NODE_PATH 环境变量来创建绝对路径。我们可以在。env 文件和 create-react-app [将识别它](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-development-environment-variables-in-env)并使用 [dotenv 库](https://www.npmjs.com/package/dotenv)应用它。

```
# Create a .env file using the touch command 

touch .env 

# Inside the .env file add: 
# NODE\_PATH=src 
```

如果您不熟悉这种模式，当我们开始构建应用程序时，它会更有意义。它允许你做的是直接导入文件，而不需要跟踪你的文件夹路径。类似这样的东西../../components/List 变成了 components/List，方便多了。

### 文件夹结构

在 src 中，让我们更新我们的文件夹结构，使其更具可伸缩性，更适用于生产应用程序。

[![](img/449931e79dbae85c3ecaf43c9a04ca2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dXrQfu7w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AeCWgjBOXcPZ_9MIQYeSzFQ.png)

*   组件:这个文件夹将包含我们所有的 React 组件(包括容器组件和表示组件)。
*   服务:服务允许您连接到 API(例如使用 axios 调用 HN API)或为应用程序提供扩展功能(例如添加降价支持)。
*   store:store 保存了我们用于 Redux 和管理状态的所有逻辑。
*   样式:在 styles 文件夹中，我们声明变量、模板和可重用的样式模式，它们可以在组件中共享。
*   utils:可以在整个应用程序中重用的助手函数。

> *该文件夹结构有两个方面值得注意:*
> 
> 1.  我们的应用程序只有一个路由，它是根/如果我们有多个路由，我也会使用 react-router 并为页面级组件创建一个 pages 文件夹。
>     
>     
> 2.  我不使用单独的容器文件夹来连接 Redux。我发现这增加了不必要的复杂性和混乱，因为开发人员会从错误的位置导入(当他们需要组件时是容器，反之亦然)。以我的经验来看，在实际环境中，只有一个真实的进口来源会更好。

因为我们使用的是样式化组件，所以我们可以删除 index.css 文件和 App.css 文件。现在我们可以添加一些样板基础样式。在 src/styles 文件夹中，创建名为 globals.js 和 palette.js 的文件。

调色板将包含我们将在应用程序中使用的颜色分组，并为我们的 UI 创建主题。将以下代码添加到 src/styles/palette.js 中。

[![](img/549e99d9a69db44989282ed2e329643c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TKG6oMAu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AZr48tSzc6mcC981FWblX3g.png)

globals.js 用于生成我们在整个应用程序中共享的默认基础样式。styled-components 中的 injectGlobal 方法应该谨慎使用，但它对于应用程序级别的样式非常有用。

> *注意:在样式化组件 v4 中，* *injectGlobal API 被* *createGlobalStyle 所取代。*

[![](img/770fee3a81f230810e934994d051501a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o2WB0rE0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AWaP3vgmtvQxpQQ0hC9j3og.png)

在组件文件夹中创建一个应用程序文件夹。将默认的 CRA 文件移动到这个位置，并将 App.js 重命名为 index.js。这允许我们导入组件/App。

[![](img/bbc4d9ac7a64b3a222d4a5ea3b7e2517.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9bjbLLAr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AjCIpQi-8uiCyDejD9yRTYg.png)

现在，打开 src/index.js(项目的根文件)并更新内容以使用我们的新文件夹结构。

[![](img/a3b9606d5ba205bee3fd7421c428b3ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NLxoDMwQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AJ4iZthOacnUvdXpGcKXWHw.png)

请注意，由于我们之前设置了 NODE_PATH，因此我们可以使用 components/App 导入 App，并使用 styles/globals 设置 GlobalStyles。执行 setGlobalStyles()函数将它们应用到应用程序。

现在我们已经准备好开始我们的开发环境，我们的核心结构已经就位。运行以下命令启动应用程序，您应该会在 [http://localhost:3000](http://localhost:3000) 上看到它。还没什么可看的，但我们会到达那里的:)

```
yarn start 
```

[![](img/1a9ccbf9517907fce95ea2634b7a4b98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WsYEdfjA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AkerMzK7dPowXiHRJx90Zfg.png)

### 向 React 应用添加 Redux

在 src/store 文件夹中，创建一个 index.js 文件、一个 reducer.js 文件和一个 middleware.js 文件。让我们也初始化一个应用程序特性来管理应用程序的状态。

[![](img/6e3a7fbafb3b87dbf75e3fc1d5aacf40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kZ0EanW9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AWiTf2X7rt16a_YhDrKqMcw.png)

> *根据我的经验，如果你按功能而不是按特性分组，生产中的冗余更容易管理，类似于* [*鸭子接近*](https://medium.freecodecamp.org/scaling-your-redux-app-with-ducks-6115955638be) *。在“按功能分组”的方法中，所有操作、缩减器等都位于一个单独的文件夹中，当生产中文件数量增加时，在文件之间导航会变得越来越困难。按功能分组时，您总是可以在一个紧凑的位置找到所需的文件。*

在 index.js 中，我们将创建一个 configureStore 函数，这是我们在应用程序中初始化 Redux 的方式。

[![](img/5bc5a91f76c421b5469c557aaaf7ac0b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fz1ACCkX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A81tI96h93hovxfBlCc0lgA.png)

我们使用 Redux 中的 createStore 来构建初始存储。我们从根 reducer 文件导入 reducer，从中间件配置文件导入中间件。initialState 将在运行时提供并传递给我们的函数。在生产中，我们可以管理复杂的功能，如 SSR 或在初始加载时从服务器传递数据，这使我们能够优雅地处理这些功能，并将其从商店创建中抽象出来。

在 reducer.js 文件中，使用 combineReducers 创建根 reducer。这个函数组合了所有的 reducer 函数来构建一个状态树。

[![](img/ac7b1394038faf0afacce46ca650cb57.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wej596nE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AB7Vfn2aQ8xbFRG5ihnVpnw.png)

接下来，我们可以在 middleware.js 文件中创建中间件。中间件是一个被分派的动作每次都必须通过的功能。它用于扩展 Redux 的功能。将以下代码添加到文件中。

[![](img/1363a6203c9c06431b378b54349e7afe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---mMUsrbx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AgHwwDTE5o8UomnMrvlUuKw.png)

我们还将制造我们的第一台减速器。在 src/store/app 中，创建 reducer.js 和 actions.js 文件。我们将添加在白天模式和夜晚模式之间切换的功能，所以让我们创建一个操作来管理这个功能。在 src/stre/app/actions.js 中，添加以下代码。

[![](img/c215366e400817c65051934e9895c8e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HhA-1lno--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AEaIas2IFck_z3EwKdeFe2A.png)

我们创建一个 actionTypes 对象来保存我们的动作类型常量。这些将在减速器中使用，以使类型与状态变化相匹配。我们还创建了一个 actions 对象，它保存了我们将从应用程序中调度的函数，以创建状态更改。每个动作都有一个类型和一个有效载荷。

最后，我们可以创建我们的减速器。

[![](img/77ce3e188d0b9031a7feb7578a68d53b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xDppjc8u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AS4kGs9EADhwpWLr_0cv08Q.png)

当我们调度 SET_THEME 动作时，它会将状态的主题值更新为有效负载中的值。有效负载将是一个具有{ theme: 'value' }形式的对象。当我们展开…有效载荷对象时，状态的键将被替换为...陈述匹配—在本例中为主题。

为了本文的简洁，如果你需要进一步了解 Redux 的基础知识，[看看 Redux 的创造者](https://egghead.io/courses/getting-started-with-redux)[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1)的免费视频。

返回到 src/index.js，现在我们可以更新它以将我们的应用程序连接到 Redux。为 Provider 添加一个导入，并更新您的呈现方法，如下所示。

[![](img/672d3a2f7602187272f45ef6239cfa58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VmtgrO7i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A3n6O0LIUSTCVwFt8uduNMw.png)

这应该就是你将 Redux 与应用程序集成所需要的一切！返回 [http://localhost:3000](http://localhost:3000) ，打开 Chrome 控制台应该会看到以下内容。

[![](img/616a4e1d099285c7a8383befdf699e80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mZPM_Znh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2APg3Jfden_UothgVAoavi2Q.png)

### 用 React 和 Styled 组件构建 UI

既然 Redux 已经初始化，我们可以开始处理我们的 UI 了。首先，让我们声明一些将在组件中使用的样式常量。在这种情况下，我们将创建一个 mediaQueries 文件来保存常数，以便于为我们的应用程序添加移动响应。创建一个 src/styles/mediaQueries.js 文件，并在每个文件中添加以下代码。

[![](img/d839346309d3a0dd39e6ab5e4196e7d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lOPGwnNU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AP2P94sWY44UGWgEmnp93fg.png)

返回到我们的 src/components/App 文件夹。在 index.js 中，我们将内容更新如下。

[![](img/00e50b5247957619def7cc9446a10d81.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m-JQyOQa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2ACKXnrGJRjg0KF_h8k8nXCw.png)

我们使用 styled-components 中的 ThemeProvider 组件。这提供的功能使我们能够将“主题”作为道具传递给我们构建的所有样式化组件。我们在这里将其初始化为 colorsDark 对象。

App 包含我们尚未构建的组件，所以我们现在就构建吧。首先，让我们构建我们的样式组件。在 App 文件夹中创建一个 styles.js 文件，并添加以下代码。

[![](img/6ec6c6e7a5ea7c0c0067bc02ed06348f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ywj_CqFq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AgVQdtVhtJ2Z6IJYYa5sgXA.png)

这为页面创建了 div，我们称之为 Wrapper，并为页面创建了 h1 作为组件标题。styled-components 语法使用您在 styled 对象之后指定的 HTML 元素创建一个组件。您使用一个字符串来定义该组件的 CSS 属性。

注意在第 20 行，我们使用了主题道具。一个包含 props 作为参数的函数被 styled-components 注入到样式字符串中，允许我们提取属性或添加逻辑来构造样式，从使用它们的组件中抽象出来。

接下来，我们将创建包含黑客新闻的列表组件。创建 src/components/List 文件夹，并添加 index.js 和 styles.js 文件。在 index.js 中添加以下内容。

[![](img/c01f576bcc2a69ccff0e9723207626e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r-9-Vqgv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A4g_MVApQD8hbj_DRoLVF1g.png)

在 styles.js 中，我们创建了 ListWrapper。我们使用从 ThemeProvider 组件获得的主题道具来设置背景色。

[![](img/70c0e6f5216fa7e2773d38a47524199e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RMK_amxu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A3936mELdgni4HKpqpLcR0w.png)

最后，我们创建 ListItem 组件来显示各个故事。创建 src/components/ListItem 文件夹以及 index.js 和 style.js 文件。

我们希望我们的用户界面模仿黑客新闻。现在，我们将在 ListItem 中使用假数据来模拟这一点。将以下代码添加到 index.js 文件中。

[![](img/6e47f1475c102d3760b5ee7f00613ec3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0k9iqbp6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AUySO57sHLlWpX-6LIY79Vw.png)

每个故事都有标题、作者、分数、发布时间、源 URL 和评论数。我们初始化这些来测试值，这样我们就可以看到它在我们的 UI 中的样子。出于安全原因，添加了 rel = " no follow no referrer no opener"。

在 styles.js 文件中，添加以下代码。

[![](img/0de5fb814e7cd18219538d71b688bab5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qu9G1L_W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2ADfFmD06YGcaZ8O0kMfTwtg.png)

这应该是我们需要的基本 UI 组件！回到你的浏览器，你应该有一个带有虚假数据的单品提要。

[![](img/0d31e5c6c27a63abae3a863399e921cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B27Da60F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AU0d5JVmGjTZfttpvnhcz4w.png)

### 用 Redux 和 Axios 进行 API 调用

是时候给我们的应用添加真实数据了。我们将使用 axios 请求库调用黑客新闻 API。调用一个 API 会给我们的应用程序引入一个“[副作用](https://en.wikipedia.org/wiki/Side_effect_%28computer_science%29)，这意味着它会从我们本地环境之外的源修改状态。

API 调用被认为是副作用，因为它们会将过时的数据引入我们的状态。其他副作用的例子包括在浏览器中与 localStorage 交互、跟踪用户分析、连接到 web 套接字等等。有多个库来管理 Redux 应用程序中的副作用，从简单的 [redux-thunk](https://github.com/reduxjs/redux-thunk) 到更复杂的 [redux-saga](https://github.com/redux-saga/redux-saga) 。然而，它们都服务于同一个目的——允许 Redux 与外界进行交互。redux-thunk 是使用起来最简单的库之一，因为除了 action 对象之外，它还允许您调度 JavaScript 函数。这正是我们使用 axios 所需要的功能，通过利用一个函数来管理从 API 调用返回的承诺。

在我们的 src/services 文件夹中，创建一个 Api.js 文件和 hackerNewsApi.js。Api.js 将包含简化 axios 请求的配置。我们不会在这里复制整个文件，但是您可以从[源代码](https://github.com/gitconnected/hacker-news-reader/blob/master/src/services/Api.js)中获得内容，该源代码为我们在这个项目中需要的基本 API 请求使用了合理的默认值。

在 src/services/hackerNewsApi.js 文件中，我们将定义向 hackerNewsApi 发出请求的函数。[文档](https://github.com/HackerNews/API)显示将使用/v0/topstories 端点获取 id 列表，并使用/v0/items/ endpoint 获取每个故事的数据。

[![](img/d971904b7dd3916f9b4c61e9151ceec1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vM-RsqRy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AfJz7VjaaonQbPsCYgbHrTQ.png)

/v0/topstories 端点返回列表中大约 400–500 个条目的所有 top story IDs。因为我们为每个故事单独获取数据，所以立即获取所有 500 个单独的项目会降低性能。为了解决这个问题，我们一次只能获取 20 个故事。我们。slice()基于当前页面的 story ID 数组，并返回 story 项目的该部分。因为我们为每个故事 ID 调用了/v0/item/然后()并按照故事 id 的顺序保留排序。

为了管理应用程序中故事的状态，我们将创建一个故事缩减器。创建一个 src/store/story 文件夹，在其中添加一个 reducer.js 文件和一个 actions.js 文件。在 actions.js 文件中，添加以下代码。

[![](img/8f26efc8123a85092a012a646e8350c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ki-lk036--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A25TV9sa-gTIT3Wodb8ptQQ.png)

我们为 story ID 和 story items API 调用的请求、成功和失败状态创建 actionTypes。

我们的 actions 对象将包含管理请求的 thunk 函数。通过分派函数而不是动作对象，我们能够在请求生命周期的不同点分派动作。

函数 getTopStoryIds 将调用 API 来获取完整的故事列表。在 getTopStoryIds 的成功回调中，我们调度 fetchStories 操作来检索故事项的第一页结果。

当我们的 API 调用成功返回时，我们调度成功动作，允许我们用新数据更新 Redux 存储。

> thunk 包的一个基本实现只使用了几行代码。完全理解它需要 Redux 中间件的知识，但是从代码中我们可以看到，如果我们的 *动作是一个* *函数而不是一个* *对象，我们执行那个函数并传递* *dispatch 作为参数。*

[![](img/718d8dd96a8b84b293ad872a923f5e7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0AOhmktX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AzzZcmCcNFymcha5RMfcftg.png)

现在我们需要创建 reducer 来存储 Redux 状态中的数据。在 src/store/story/reducer.js 文件中，添加以下内容。

[![](img/f3876b8bc82189d5818cf8de4f8c2418.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tNSrOhp6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A1lx_NoFojzqqH-dMNbr_xw.png)

对于 FETCH_STORY_IDS_SUCCESS 动作类型，我们扩展当前状态和有效负载。有效负载中唯一的键/值是 storyIds，它会将状态更新为新值。

对于 FETCH_STORIES_SUCCESS 操作类型，我们将新的故事添加到先前创建的故事列表中，这将使它们在我们获取更多页面时保持有序。此外，我们增加页面并将 isFetching 状态设置为 false。

既然我们在 Redux 中管理我们的故事的状态，我们可以使用我们的组件显示这些数据。

### 将 React App 连接到 Redux 商店

通过使用 react-redux 绑定，我们能够将组件连接到存储并接收 redux 状态作为道具。然后，任何时候商店有更新，道具也会改变，导致我们的组件重新渲染，这将更新用户界面。

我们还将函数作为道具传递给分派动作的组件。当我们在组件内部调用这些函数时，它会触发 Redux 存储中的状态变化。

让我们看看如何在我们的应用程序中管理这一点。返回到 src/components/App 文件夹，创建一个 App.js 文件，将 src/components/App/index.js 中的内容复制并粘贴到新的 App.js 文件中。在 index.js 中，我们将把 App 组件连接到 Redux。将以下代码添加到 index.js 文件中。

[![](img/733de290c3c469e0cc7f5dad8e733ac4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YzoTFUMa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Ac_61z20YchuagvsYmImxdQ.png)

mapStateToProps 是一个函数，它将 Redux 状态作为参数，并返回一个对象，该对象作为 Props 传递给连接的组件。对于 App，我们需要 stories 数组、当前页面、storyIds 数组和 is fraction 指示器。

mapDispatchToProps 是一个函数，它将 dispatch 函数作为一个参数，并返回作为道具传递给组件的函数对象。我们创建了一个函数 fetchStoriesFirstPage，它将显示获取故事 id 的动作(然后获取故事条目的第一页)。

我们在 App.js 文件中使用这些属性。首先，我们添加一个 componentDidMount，这样一旦组件在 DOM 中，就可以获取故事。这将故事本身传递给我们的列表组件

[![](img/529851998bb3eeb3074f1fe884d95256.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FeB0jNJ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AWdwfnfIqzt3gvOiLYrqlUg.png)

在 src/components/List/index.js 中，我们映射 stories 数组并创建一个 ListItem 组件数组。我们将关键字设置为 story ID，并传播 story 对象……story——这将对象的所有值作为单独的道具传递给组件。作为数组挂载的组件需要 key prop，以便在渲染过程中更新组件时 React 可以更快。

[![](img/ca4fbf5e95c9ac87c30400e174dd3f25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ue8X9dnf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A7eEOEtZMMaefxKwhIjvkkw.png)

如果我们现在看屏幕，我们应该有 20 个列表项，但是仍然使用硬编码的数据。

[![](img/b561490276e299427ce0fa2b66fc020a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PvzhK_cu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AyUTZNu2i3K8_4kdnFUsXhA.png)

我们需要更新我们的 ListItem 来使用故事中的值。同样在《黑客新闻》中，它显示了自故事发表以来的时间以及消息来源的域。我们将安装 [timeago.js](https://www.npmjs.com/package/timeago.js) 和 [url](https://www.npmjs.com/package/url) 包来帮助计算这些值，因为它们不是直接从 API 传递的。使用以下命令安装它们。

```
yarn add timeago.js url 
```

我们还将编写助手函数来构建这些值。将文件从 src/utils 文件夹中复制到[源代码](https://github.com/gitconnected/hacker-news-reader/tree/master/src/utils)中。

现在，我们可以将 src/components/ListItem/index . js 文件更新为以下内容。

[![](img/ef4cdaa9a8c28d5a1e9a663ab66783be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ybr-vWGD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AA-hGJAgOxanZPWjcOM6SRw.png)

通过这一步，我们现在在我们的应用程序中显示前 20 个顶级黑客新闻——非常酷！

[![](img/5ced7fca8f175aab46d6e1d01eeedde8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jw4YFesA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2ACWm1RlXAwNatofoYIUTIag.png)

### 无限滚动分页请求

现在我们想在用户向下滚动屏幕时获取一个额外的页面。回想一下，每次我们成功获取故事时，我们都会增加存储中的页码，因此在收到第一页后，我们的 Redux 存储现在应该读取 page: 1。我们需要一种方法来调度 scroll 上的 fetchStories 操作。

为了实现无限滚动，我们将使用 react-infinite-scroll-component。我们还希望有一种方法来确定我们是否有更多的页面要加载，我们可以在选择器中使用 [reselect](https://www.npmjs.com/package/reselect) 来做到这一点。

```
yarn add react-infinite-scroll-component reselect 
```

首先，我们将构建我们的选择器来计算是否存在更多的故事。创建 src/store/story/selectors.js 文件。为了确定是否存在更多的故事，我们查看 Redux 存储中 storyIds 的数组长度是否与 stories 数组的长度相同。如果 stories 数组更短，我们知道会有更多的页面。

[![](img/29bb679db52c7084e5e7ba5eca330b50.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lFHWfx5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A9BZsfoH8vkhyo8kModYwxQ.png)

在 src/components/App/index.js 容器中，我们导入了 hasMoreStoriesSelector 并向 mapStateToProps 添加了一个 hasMoreStories 键。此外，将 fetchStories 操作添加到我们的 mapDispatchToProps 中，以便我们可以在滚动时加载它们。

[![](img/9bdded7e357ad0185ed227d1603d8576.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T0uuT08f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AUH_L2Rddr9lbR40j-N-Agg.png)

我们将希望在等待 API 请求时显示一个加载动画。创建 src/components/Loader 文件夹以及 index.js 和 styles.js 文件。我们希望我们的动画是 3 个褪色的点。

[![](img/ef87dd02c973653d7ca4a0dd91493b87.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ND4YTBXT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2An3ITZcnQidyQ3dBVlSVyHw.gif)

在 styles.js 文件中添加以下代码。

[![](img/b9e70830a6c92a3d00c96a63c04a484e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dEKz578x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AI_2jL9IWId4N2ghr82KSoA.png)

[@关键帧](https://developer.mozilla.org/en-US/docs/Web/CSS/@keyframes) API 是一种定义动画的 CSS 技术。上面的代码展示了样式化组件中对它的抽象。我们将在屏幕上有 3 个点，它们的不透明度从 0.2 开始，增加到 1，然后回到 0.2。我们给第二个和第三个点添加了动画延迟，这样就有了偏移反弹的效果。

我们的加载器组件将只是我们的动画风格的组件，有 3 个跨度包含周期。

[![](img/777bf01dc69b8c650250bfc697e40192.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rtYbkC5b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AOEvcjnFwj7x6OMsue2rTKQ.png)

现在，我们准备将功能添加到我们的列表中。在 App 组件中导入无限滚动模块和我们的加载器。我们还将创建一个 fetchStories 回调，该回调将调用 fetchStories 属性来调度下一页的请求。只有当 isFetching 为假时，我们才调用 fetchStories dispatch prop。否则，我们可能会多次获取同一个页面。您的 src/components/App/App.js 现在看起来应该如下所示。

[![](img/85b03de7bd3d05f1abe8a33286cfe663.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ngi-sSc_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AFPavhHcReHylbh6qVythUA.png)

当我们向下滚动页面时，只要 hasMoreStories 为真，InfiniteScroll 组件就会调用 this.fetchStories。当 fetchStories API 请求返回时，新的故事被追加到 Stories 数组中，将它们添加到页面中。

有了这个功能，您现在可以滚动浏览整个头条新闻列表！*击掌*

### 你的最终挑战

在本教程的开始，我们在 App reducer 中初始化了一个主题属性。现在，我将让您自己实现切换功能。您可能希望在某个组件中添加一个 click 事件，该事件将调度 setTheme 操作并在明暗之间切换。如果 state . app . theme = = ' dark '，您将希望在 ThemeProvider 组件上有一个传递 colorsDark 的三元条件，否则使用 colorsLight。

如果你被卡住了，参考[源代码](https://github.com/gitconnected/hacker-news-reader)来看看我们的实现，[加入我们的懈怠，至于帮助](https://community.gitconnected.com/)，[试试我们的工作解决方案](https://chrome.google.com/webstore/detail/hacker-news/hknoigmfpgfdkccnkbfbjfnocoegoefe?authuser=1)。

### 部署到 GitHub 页面

任何生产应用程序的最后一步实际上都是将其推向生产。由于我们所有的功能都在客户端，我们可以使用 [GitHub Pages](https://pages.github.com/) 免费将它部署为静态站点。

提交你所有的代码，推送到 GitHub。我把我的报告命名为克隆人。[如果您在创建回购和上传代码方面需要帮助，请遵循本指南](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)。

现在使用以下步骤将其托管在 GitHub 页面上:

1.  将“home page”:“http://. github . io/”添加到 package.json 中。用您使用的值替换和—我的值将是 treyhuffine 和 hn-clone。

[![](img/d9154172322d52fc2068d7f80ce6a12a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1Bqx7iu1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2ACj1S_3wEglnhtgfyBC6s7Q.png)

1.  将 gh-pages 作为开发依赖项安装

```
yarn add -D gh-pages 
```

1.  向 package.json 中添加 2 个脚本

```
"predeploy": "npm run build","deploy": "gh-pages -d build" 
```

[![](img/64e4f18fc7932074428676a8030847b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PYNQs4al--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AEvO5yeQ-V3cFxf9-bOyANA.png)

1.  最后，运行 yarn deploy 并访问您在主页中指定的 URL。

[![](img/db362d3380dabd4b1dd90488aadd7084.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tkbOc1iI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AjPe2jesKJwjTH13RIH0HWA.png)

现在你有一个黑客新闻的克隆产品！*双高五*

### 结论

这涵盖了构建黑客新闻克隆所需的基本功能。[源代码](https://github.com/gitconnected/hacker-news-reader)有一些额外的功能，并且正在继续更新，因此请查看那里以获得一些灵感来继续构建应用程序并了解更多 React。

别忘了[下载 Chrome 扩展](https://chrome.google.com/webstore/detail/hacker-news/hknoigmfpgfdkccnkbfbjfnocoegoefe?authuser=1)并访问 gitconnected.com[加入开发者社区](https://gitconnected.com/)。

**原载于 gitconnected.com**[**-开发者社区**](https://gitconnected.com/courses/learn-react-redux-tutorial-build-a-hacker-news-clone) **。**