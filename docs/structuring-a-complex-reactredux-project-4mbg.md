# 构建复杂的 reaction/Redux 项目

> 原文：<https://dev.to/nylas/structuring-a-complex-reactredux-project-4mbg>

Nylas 工程团队最近更新了 [Nylas 仪表板](https://dev.to/nylas/announcing-the-nylas-dashboard-20-1pg-temp-slug-6941808)，让我们客户团队中的每个人——从开发人员到产品经理、销售工程师、客户成功代表和财务团队——都能够访问他们的 Nylas API 帐户。

在我们的[关于在 Nylas 仪表板改造期间提高开发速度的博客文章](https://dev.to/nylas/raising-the-limits-on-developer-speed-4dgl)之后，我们有一些后续的请求，要求 a)我们写的脚手架脚本，现在可以在[这里得到](https://gist.github.com/hallamoore/bf4e18e64cca1b9e45bf69901e56ee43)，以及 b)项目的总体结构。

我们的 dashboard 前端代码是一个 React/Redux 应用，大致结构看起来是这样的:

```
src/
  appConstants/
  components/
    DropdownMenu/
      dropDownArrow.png
      index.js
      index.test.js
      stylesheet.js
    ...
  containers/
  higherOrderComponents/
  layouts/
  models/
  modules/
  screens/
  store/
    accounts/
      actions.js
      constants.js
      endpoints.js
      reducers.js
      selectors.js
    applications/
    ...
    actions.js
    api.js
    configureStore.js
    rootReducer.js
    selectors.js
  index.css
  index.js
  registerServiceWorker.js
  Routes.js 
```

Enter fullscreen mode Exit fullscreen mode

🙀这里发生了很多事情，所以我将简单地分解每个目录或文件的用途。

`appConstants/`是我们保存所有应用程序常量的地方，比如第三方服务的 API 密钥。我们最初将它命名为`constants/`，但是后来发现在项目的其他地方有另一个常量节点模块导致了命名冲突，所以我们将其重命名为`appConstants/`。

我们将 React 组件分解到多个目录中，试图以更易于管理的方式进行分组。我们最初只在表示组件和容器之间进行了划分。这两者之间的重要区别是表示组件是无状态的，而容器不是。您可以从本文中了解更多关于表示组件和容器之间的区别。然而，随着我们不断添加越来越多的组件，我们需要更多的分离。我们最终得到的目录是:

*   `components/` -表示组件的原始目录。我们的大部分表象组件仍然生活在这里。
*   `containers/` -容器的原始目录。(我打赌你肯定猜不到😜)
*   `higherOrderComponents/` - [高阶组件(hoc)](https://reactjs.org/docs/higher-order-components.html)是一种特殊类型的容器，实际上是函数。这些函数封装了可重用的逻辑模式，并用于用该逻辑包装其他组件。例如，我们的一个 hoc 是一个 LazyLoaded 组件。在加载必要的数据之前，它会显示一个加载指示器，如果花费的时间太长，它会向我们报告。我们通过 LazyLoaded HOC 传递任何需要这种加载行为的屏幕，而不必在每个屏幕中重新实现该行为！💥
*   这是另外一个保存表示组件的目录。这些表示组件特别关注应用程序中整个页面的布局。
*   `screens/` -屏幕是为特定应用视图拉入所有表示组件和子容器的容器。我们所有的屏幕都从一个布局组件开始，并从那里添加子组件。

每个组件在其中一个父目录中都有自己的子目录。每个子目录中的主文件是`index.js`，这是一般组件定义所在的位置。`index.test.js`是我们通过搭建脚本自动添加的测试文件。我们还将组件的任何样式保存在这个子目录中。这包括它需要的任何图像和一个单独的`stylesheet.js`文件，如果样式太大而无法保存在`index.js`中的话。我们使用[阿芙罗狄蒂](https://github.com/Khan/aphrodite)来用 JavaScript 编写我们的风格，这有助于我们将风格本地化到每个组件，而不是隐藏在大量的 CSS 文件中。

`/models`是我们为每个 API 对象定义类的地方。每个类都定义了一个`toJSON()`和一个`fromJSON()`方法，当我们在应用程序中处理 JSON 响应时，它们允许我们将 JSON 响应转换成实例，当我们必须将数据发送回服务器时，它们又返回到 JSON。该项目还使用[流](https://flow.org/)作为类型检查器，将 JSON 转换成更具体的数据结构允许我们正确地对每个字段进行类型注释。

`/modules`基本上是实用程序或助手代码的目录。我们将密切相关的代码分组到它们自己的文件中，最终得到了像`errorReporter.js`和`apiRequest.js`这样的模块。

`store/`代表我们所有的 Redux 代码。正如我在以前的博客文章中提到的，我们将我们的存储分为每个模型的子目录。每个子目录都有传统的冗余文件`actions.js`、`reducers.js`和`selectors.js`。此外，我们还有一个`constants.js`文件，用于存放与模型存储相关的任何常量，还有一个`endpoints.js`文件，用于存放与后端 API 交互的函数。在`store/`根目录下，我们有从相应的子目录文件中导入所有函数的文件:

*   `actions.js`从所有子`actions.js`文件导入
*   `api.js`从所有子`endpoints.js`文件导入
*   `rootReducer.js`合并所有子`reducers.js`文件
*   `selectors.js`导入所有子`selectors.js`文件

我们还有`configureStore.js`，它进行实际创建存储的初始设置，并可能加载任何以前保存的状态。

拥有我们最重要的 CSS 样式。我们的大多数样式都在组件目录中，但是也有一些`body`和`html`级别的样式在这个文件中。

简单地渲染我们的 root React 组件。

设置服务工作者，这样我们就可以从本地缓存中提供资产，使我们的应用程序运行得更快。

`Routes.js`将我们的每个屏幕组件连接到一个应用程序路径。例如，当用户访问仪表板中的`/register`路线时，这是我们注册要加载的`RegisterScreen`的地方。

总的来说，我们试图构建我们的项目，这样当我们在一个特定的部分工作时，所有相关的代码都在附近，同时仍然保持关注点的分离。将较小的文件按模型或组件分组确实有助于改善开发人员的体验。我们可能会在未来继续迭代我们的结构，但到目前为止，这对我们来说效果很好！如果您有任何问题，或者您的公司有什么不同的做法，请告诉我们。

* * *

这篇文章最初发表在 Nylas 工程博客上