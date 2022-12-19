# 如何在 Sanity.io 项目中构建代码库

> 原文：<https://dev.to/sanity-io/how-to-structure-your-code-repository-in-a-sanityio-project-3aph>

您已经启动了一个健全项目，配置了您的模式，并向 API 发布了一些内容。你现在已经准备好制作一个前端，一个应用，或者一个利用它的服务。尽管您可以使用 Sanity.io 作为各种事情的通用 API，但在某些情况下，让 Sanity Studio 靠近那些使用它的服务是有意义的。此外，如果你想到它，工作室只是你的理智 API 的另一个前端。

可能有很多方法可以实现这一点，但是我们最常看到的模式是以一种 [monorepo](https://github.com/babel/babel/blob/master/doc/design/monorepo.md) 的方式来组织你的项目。事实上，我们自己用 Studio 和许多可以独立使用的包来完成。当然，你应该考虑[多边或单边转帖](https://medium.com/@patrickleet/mono-repo-or-multi-repo-why-choose-one-when-you-can-have-both-e9c77bd0c668)的利弊，但是对于一个典型的工作室项目，一个前端网络或应用项目，也许是一个简单的服务，我们认为这是有意义的。

这意味着您可以对模式进行版本控制。这是件好事。这也意味着您可以将 content studio 包含在一个连续的集成流程中，将其与其他应用程序集成在一起。

```
project-folder/
├── studio/
│   ├── config/
│   ├── plugins/
│   ├── schemas/
│   ├── static/
│   ├── .gitignore
│   ├── README.md
│   ├── package.json
│   └── sanity.json
├── frontend/
│   ├── components/
│   ├── lib/
│   ├── pages/
│   ├── static/
│   ├── .gitignore
│   ├── README.md
│   └── package.json
├── services/
│   ├── rss-feed/
│   │   ├── .gitignore
│   │   ├── index.js
│   │   ├── package.js
│   │   └── README.md
│   └── README.md
├── .gitignore
└── README.md 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，依赖项安装在每个单独的文件夹中。因此，您必须启动应用程序，并在构建系统中定位文件夹。如果项目增长，你的一些包将被发布到 [npm](https://www.npmjs.com/) 或类似的地方，你可以使用 monorepo 管理工具，如 [Lerna](https://github.com/lerna/lerna) ，使跟踪版本和引导更容易。

由于我们已经在 React 中编写了 Sanity Studio，这种结构还允许跨项目共享 UI 组件、CSS 等等。

[**还不确定如何在你的项目中构造代码？加入我们的 Slack，问我们！**T3】](https://slack.sanity.io/)