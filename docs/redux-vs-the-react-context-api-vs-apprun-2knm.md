# Redux 与 React 上下文 API 与 AppRun

> 原文：<https://dev.to/yysun/redux-vs-the-react-context-api-vs-apprun-2knm>

最近，我读了一篇名为 [Redux 与 React 上下文 API](https://daveceddia.com/context-api-vs-redux) 的文章。这是我喜欢的帖子类型。它用一个简单的例子来解释背后的概念。这个例子看起来很简单，但它让我们毫不分心地理解这个概念。经过几分钟的阅读，我已经学会了 Redux 和 Context API，并开始思考如果我们在 [AppRun](https://github.com/yysun/apprun) 应用程序中做同样的事情会怎么样。事不宜迟，我在 glitch.com 创造了同样的例子。

[![AppRun Example](../Images/86ab9a0e083e3f5fb9755905e20eb5a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4p-N58ik--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A_bvkERxKewur67C5zowOBQ.png)

可以在 glitch.com 上看到现场演示:[https://apprun-state-2-components . glitch . me](https://apprun-state-2-components.glitch.me)

我将描述 AppRun 的思想过程，帮助你理解并与 Redux 和 Context API 进行比较。

# HTML

HTML 具有站点结构。

```
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
  AppRun
</head>
<body>
  <div id="root">
    <div class="app">
      <div class="nav" id="user-avatar">
      </div>
      <div class="body">
        <div class="sidebar">
          <div class="user-stats" id="user-stats">
          </div>
        </div>
        <div class="content">main content here</div>
      </div>
    </div>
  </div>
  <script src="main.tsx"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到我们主要使用 HTML，不需要组件结构。

```
// No need
<App>
  <Nav>
    <UserAvatar />
  </Nav>
  <Body>
    <Sidebar>
      <UserStats />
    </Sidebar>
  </Body> </App> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们不是被迫进入组件结构，所以我们利用 HTML 并将组件安装到 HTML 元素上。

# 主程序

在 AppRun 应用程序中，我们将组件安装到 HTML 元素中。

```
import './style.css';

import app from 'apprun';

import UserAvatar from './UserAvatar';
import UserStats from './UserStats';

new UserAvatar().mount('user-avatar');
new UserStats().mount('user-stats');

app.run('/set-user', {
  avatar: "https://github.com/yysun/apprun/blob/master/logo.png?raw=true",
  name: "AppRun",
  followers: 1234,
  following: 123
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们只需要两个组件<useravatar>和<userstats>。我们将它们分别挂载到 id 为“user-avatar”和“user-stats”的</userstats></useravatar>

元素。

然后我们发布一个 AppRun 事件'/set-user '，将用户数据作为事件参数。

# 组件

组件订阅并处理 AppRun 事件。它们从事件参数中获取用户数据，并创建一个新的组件状态。AppRun 然后用新的状态调用视图函数。视图函数使用 JSX 创建虚拟 DOM。然后 AppRun 渲染 DOM。

## 用户头像组件

UserAvatar 组件显示化身图像。状态是头像 URL。

```
import app, {Component} from 'apprun';

export default class extends Component {
  state = '';

  view = (state) => <img className="user-avatar small" alt="user avatar" src={state} />; 
  update = {
    '/set-user': (_, user) => user.avatar,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## UserStats 组件

UserStats 组件显示用户的头像、姓名和其他信息。状态是用户对象。

```
import app, {Component} from 'apprun';

export default class extends Component {
  state = {} ;

  view = (user) => <>
    <div>
      <img className="user-avatar " alt="user avatar" src={user.avatar}/>
      {user.name}
    </div>
    <div className="stats">
      <div>{user.followers} Followers</div>
      <div>Following {user.following}</div>
    </div>
  </>; 
  update = {
    '/set-user': (_, user) => user,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

AppRun 支持 [JSX 片段](https://reactjs.org/docs/fragments.html)。我们可以创建一个没有根元素的元素列表来对它们进行分组。元素列表被插入到组件安装到的元素中。它帮助我们进行微调，得到一个完美的 HTML 结构。

仅此而已。

# 结论

两件事使得 [AppRun](https://github.com/yysun/apprun) 应用程序变得更加简单明了。一是我们可以将组件安装到元件上。第二，我们使用事件来传递数据和触发一系列的过程，包括更新组件状态和渲染 DOM。

你可以重新组合 glitch.com 的例子。[https://glitch.com/edit/#!/remix/apprun-state-2-components](https://glitch.com/edit/#!/remix/apprun-state-2-components)

有关 AppRun 的更多信息，请访问:

## [和](https://github.com/yysun) / [学习](https://github.com/yysun/apprun)

### AppRun 是一个 JavaScript 库，用于使用 elm 启发的架构、事件和组件开发高性能和可靠的 web 应用程序。

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![Build](../Images/f259070d058f2cdb110bc24faf221358.png)](https://travis-ci.org/yysun/apprun)[![NPM version](../Images/b7beb8c0660d521dd86968e5a8cd6896.png)](https://npmjs.org/package/apprun)[![Downloads](../Images/1350f842f8cb91e44168b2bf8746a3c1.png)](https://npmjs.org/package/apprun)[![License](../Images/7194bf8ac6a620cdacf881fa0c5ffaac.png)](https://raw.githubusercontent.com/yysun/apprun/master/LICENSE.md)[![twitter](../Images/ab05dc0fe6acc6a87259485c555b534f.png)](https://twitter.com/intent/tweet?text=Check%20out%20AppRun%20by%20%40yysun%20https%3A%2F%2Fgithub.com%2Fyysun%2Fapprun%20%F0%9F%91%8D%20%40apprunjs)[![Discord Chat](../Images/4be9076338a059b31ea56a7723e4b7f4.png)](https://discord.gg/CETyUdx)

AppRun 是一个 JavaScript 库，用于使用受 Elm 启发的架构、事件和组件构建可靠、高性能的 web 应用程序。

> AppRun 是一个 MIT 许可的开源项目。请考虑[支持 Patreon](https://www.patreon.com/apprun) 上的项目。<g-emoji class="g-emoji" alias="+1" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f44d.png">👍</g-emoji> <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/2764.png">❤️</g-emoji> <g-emoji class="g-emoji" alias="pray" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f64f.png">🙏</g-emoji>

## 大约好处

*   少写代码
*   无需学习专有语法
*   编译器/传输器是可选的
*   包括状态管理和路由
*   与 jQuery、chartjs、D3、lit-html 并行运行...

AppRun 分布在 npm 上。

```
npm install apprun
```

Enter fullscreen mode Exit fullscreen mode

您也可以直接从 unpkg.com CDN 加载 AppRun:

```
<script src="https://unpkg.com/apprun/dist/apprun-html.js"></script>
```

Enter fullscreen mode Exit fullscreen mode

或者作为 ES 模块使用来自 unpkg.com:

```
<script type="module">
  import { app, Component } from 'https://unpkg.com/apprun/esm/apprun-html?module'
</script>
```

Enter fullscreen mode Exit fullscreen mode

## 建筑概念

[![apprun-demo](../Images/e15bd051d11a9d3c5926cfd74e5ad2bc.png)T2】](https://raw.githubusercontent.com/yysun/apprun/master/docs/imgs/apprun-demo.gif)

*   AppRun architecure 有*状态*、*视图*、*更新*。
*   AppRun 是事件驱动的。
*   AppRun 是基于组件的[。](https://raw.githubusercontent.com/yysun/apprun/master/docs/#/05-component)

[试试 AppRun 游乐场](https://apprun.js.org/#play)。

## 出版社出版的书

[![Order from Amazon](../Images/ca13816934b0eaf5d68dbccba7115c8d.png)T2】](https://www.amazon.com/Practical-Application-Development-AppRun-High-Performance/dp/1484240685/)

*   [来自亚马逊的订单](https://www.amazon.com/Practical-Application-Development-AppRun-High-Performance/dp/1484240685/)

## 创建批准

…</article>

[View on GitHub](https://github.com/yysun/apprun)

享受编码的乐趣，发送 PRs。