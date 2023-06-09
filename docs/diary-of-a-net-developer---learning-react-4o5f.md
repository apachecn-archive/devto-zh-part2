# 一个网络开发者的日记-学习反应

> 原文：<https://dev.to/jonhilt/diary-of-a-net-developer---learning-react-4o5f>

# 你说什么反应？

当 Angular 1 第一次出现在现场时，它感觉像是一股新鲜空气。

突然之间，使用 MVC(少量使用 AJAX)几乎不可能的场景变得可以实现了；你真正要做的就是在你的网页上引用 Angular 脚本，然后你就可以开始了。

几年后，JavaScript/前端生态系统爆炸式增长。

Webpack，Typescript，React，Angular(现在是什么版本？6?)，巴别塔，茉莉，因果报应...

这个清单还在继续。

这就是问题所在。

如果你是 C#/ASP。NET 开发人员希望涉足这些波涛汹涌的水域，那么你将面临一个不可逾越的选择山和一个看起来像砖墙的学习曲线。

[![](img/5c791ea02369c21c6d57ca6e3ae0335c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lCWvvvQ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-1/2018-05-16-22-35-25.png)

# 建造一些真实的东西

我们刚刚在我的工作中开始了一个新项目，在对不同的 spa 进行了一些实验之后，我们决定为我们接下来的几个项目使用 React(使用 Typescript)。

我在这里的目的是简单地记录我们在前进过程中获得的一些关键东西，希望它可以帮助其他跟随我们脚步的人。

我们最终选择 React 有三个重要原因。

1.  有一个很大的社区可以提供建议/文章和学习资源
2.  无需从第一天开始就学习一些外围细节(如 WebPack ),就可以很容易地开始并构建一些东西
3.  您可以使用 Typescript 构建您的应用程序，我们发现，当我们在这些未知的水域中摸索前进时，来自 VS 代码中的“编译时”警告和错误的安全性是非常宝贵的

在我们继续之前，如果你想看看这个系列的源代码，请点击这里:-)

## 创建 React 应用

我们正在进行的项目已经有了一个全功能的 API，所以我们只是建立一个前端。因此，前端项目不需要成为 ASP.NET 的核心项目。

如果你计划在同一个项目中运行 React 和 ASP.NET 核心 Web API，那么你可能想使用微软的模板来加速这样一个项目。

因此，撇开 aside 核心意味着我们可以从一个直接的 React 项目开始，然后对我们现有的 aside 核心 Web API 进行 API 调用。

有一个方便的项目叫做 Create React App，它会为你处理大部分细节，为你的项目提供一个更简单的起点。当你开始的时候，它隐藏了一些细节，比如 WebPack，但是如果你真的需要/想要的话，你可以在以后的日子里自由地进入那些特定的领域。

我们选择使用[类型脚本版本](https://github.com/wmonk/create-react-app-typescript)。

只要你已经安装了 Node 和 Yarn 的最新版本(你可以使用 NPM 或者 Yarn，它们是可以互换的)你就可以使用这个命令安装 **create-react-app** 。

```
yarn global add create-react-app 
```

这会将 create-react-app 依赖项下载到您机器上的一个全局目录中，因此您可以在任何命令提示符下使用它(不管您当前在哪个文件夹中)。

一旦 yarn 完成了它的工作，你可以用这个命令创建一个新的 React 类型脚本应用程序...

```
create-react-app your-app-name --scripts-version=react-scripts-ts 
```

然后改变目录，使用 yarn 启动应用程序...

```
cd your-app-name
yarn start 
```

如果一切顺利，你会看到这样的东西...

[![](img/d320e8912fe29dc9057760cfd8e6a473.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l8RupZZR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-1/2018-05-16-20-51-39.png)

这应该会出现在您的浏览器中。

[![](img/960e68e0fa0ad5a4720d3c700d534255.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UEA9zjSf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-1/2018-05-16-20-52-07.png)

如果你最近尝试过创建任何前端应用程序(Angular，Aurelia 等。)那么你可能已经习惯了从一开始就看到启动项目看起来非常臃肿。

[![](img/5b498a150feb419e951a8c3237851112.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1dYWuDYE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-1/2018-05-16-21-23-49.png)

按照这些标准，这是相对精简的...

## 第一组件

我们渴望从这一点入手，急切地着手构建我们的第一个组件。

在我们开始实际项目工作之前，我们做了一些研究，实际上发现 reactjs.org 的[入门教程非常有助于在我们真正开始之前获得一些基本概念。](https://reactjs.org/tutorial/tutorial.html)

对于我们的项目，我们需要构建一个简单的列表组件，就像这样。

[![](img/154beba0865c3c60f3bb99c8333fb1aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A5A_ooem--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-1/2018-05-16-21-17-05.png)

我已经改变了我们实际建造的细节，以保护我们的客户，但这符合我们的目标结构。

那么从哪里开始呢？

一个全新的 create-react-app 中的一切都是从 app 的最顶层开始的，在 src/index.tsx 中我们可以找到这段代码。

```
ReactDOM.render(
  <App />,
  document.getElementById('root') as HTMLElement
); 
```

这指示 React 在我们页面上的一个元素中呈现 App 组件(#root)。

为了快速开始，我们决定忽略这里引用的现有应用程序组件(它只是呈现我们前面看到的“欢迎反应”页面)，并创建我们自己的组件。

构建应用程序的“反应”方式是使用组件。考虑到这一点，我们又看了一眼我们的样机，并确定了可能保证其自身组件的关键部分。

我们选定了这些组件...

*   我的用户(父组件)
*   用户详细信息行
*   头像(每行上的图像)
*   任务按钮(每行上的按钮)

因此，我们将呈现“我的用户”组件，这又将呈现一个或多个“用户详细信息行”，其中还将包括头像和任务按钮组件。

从顶部开始，我们创建了这个“我的用户”组件。

**src\MyUsers.tsx**

```
import * as React from "react";

export default class MyUsers extends React.Component<any, any>
{
    public render() {
        return (
            <div>
                <h1>My Users</h1>
                <div>
                    Each row to go here...
                </div>
            </div>
        );
    }
} 
```

这是我们能得到的最简单的开始。

通过扩展 React。组件时，我们需要创建一个呈现方法，然后该方法为我们的组件呈现相关的标记。

javascript 中看起来很有趣的“html”实际上是一种 XML 语法(称为 JSX ),它在幕后反应并转换成 javascript。如果您感兴趣，底层 javascript 如下所示...

```
return React.createElement(
    "div",
    null,
    React.createElement(
        "h1",
        null,
        "My Users"
    ),
    React.createElement(
        "div",
        null,
        "Each row to go here..."
    )
); 
```

但事实上，你通常不需要担心这一点，可以坚持上面的方便的 JSX 代码。

有了这个组件，我们只需要渲染它。

**src\Index.tsx**

```
import MyUsers from './MyUsers';

ReactDOM.render(
  <MyUsers />,
  document.getElementById('root') as HTMLElement
); 
```

App 的引用消失了，取而代之的是我们的 MyUsers 组件。

**便捷提示**

如果您使用 Visual Studio 代码在 React 应用程序上工作...

当你引用类似`MyUsers`(见上)的东西而没有引入引用时，VS 代码会提示你(带有灯泡图标)。

选择其中一个选项以自动添加引用。

[![](img/2e7fd76f34a3ffe861cd39ffdcd84f7a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9xDmQsE---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-1/2018-05-16-22-03-28.png)

因为我们之前运行了`yarn start`，对应用程序的更改会自动反映在浏览器中，这意味着我们现在可以查看我们的工作了。

[![](img/f185387c9cbedbeae4dc423d2ebebea6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hw9ZeOWb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-1/2018-05-16-22-06-28.png)

诚然，我们不会因此赢得任何设计奖项，但这是一个坚实的开端。

**记住，如果你想看看这个系列的源代码，你可以[点击这里:-)](https://jonhilton.net/devto/react-source/)**

[下次](https://dev.to/jonhilt/learning-react---building-up-the-user-interface-using-components-and-dummy-data--106d)我将讲述我们如何构建接下来的几个组件，并开始插入一些(最初是伪造的)数据。

注意，这种对事件的描述并不完全准确；序列缩短和诅咒省略；-)

*图片来源:WanderingtheWorld([www.ChrisFord.com](http://www.ChrisFord.com))[‘Vines’，美国纽约，纽约市](http://www.flickr.com/photos/44028103@N07/14212558927) via [photopin](http://photopin.com) [(许可证)](https://creativecommons.org/licenses/by-nc/2.0/)*