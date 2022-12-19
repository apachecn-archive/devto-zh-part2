# React:在组件之间传递数据…以口袋妖怪为例！

> 原文：<https://dev.to/runtime-revolution/react-passing-data-between-components-with-pokemon-as-an-example-37n0>

### React——组件间传递数据……以口袋妖怪为例！

[![](img/15da3ae3bd3c04761297752c3b51d654.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yHYBvLeU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AvWDGffKyCg5E2Lhz) 

<figcaption>照片由[斯蒂芬·亨宁](https://unsplash.com/@unlesbar?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

在 react 组件之间传递数据可能很简单，但在某些情况下，这可能非常棘手。虽然从父节点到子节点，以及从子节点到父节点，可能是一个简单的过程，但是你需要使用 react 和 javascript 提供的工具，以便在兄弟节点之间传递数据(这就是 Redux 被发明的原因)。

让我们首先关注将数据从一个组件传递到另一个组件的最简单的方法。而且，为了让它更有趣，我们将使用口袋妖怪！

怎么会？为了解释这个概念，我们将创建/模拟一个口袋妖怪战斗，就像 GameBoy 的第一代游戏一样！

> 注意:你可以在这个[库](https://github.com/jpac-run/pokemon-battle-blog-post)中找到源代码，如果你错过了什么或者卡住了，你可以找到如何运行和安装这个应用的说明。

### 设置

首先，让我们创建一个新的 react 应用程序。点击[这里](https://github.com/facebook/create-react-app)，它会把你送到 create-react-app 存储库，这个工具可以让你立刻创建一个新的 react 应用程序。所有的说明都可以在那里找到。然后，创建一个 react 应用程序(你可以称之为口袋妖怪-战斗)。之后，在 src 文件夹中，创建一个组件文件夹，并在其中创建一个口袋妖怪和一个游戏管理器文件夹。每一个都必须有一个 index.jsx 文件，我们的组件将被写入其中。

启动你的应用，打开你的浏览器，进入 localhost:3000 查看你的应用运行！(端口 3000 是默认端口，但在您的情况下可能会有所不同)

在检查了一切是否都在运行之后，用下面的文件替换您的 App.js 文件: