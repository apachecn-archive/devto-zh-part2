# 用 React 和 Webpacker 设置 Jest

> 原文：<https://dev.to/aergonaut/setting-up-jest-with-react-and-webpacker-5dmo>

Webpacker 是一个很棒的库，是在 Rails 5.1 中由 T2 引入的。它提供了一种简单的、开箱即用的方式来配置最新的 Javascript 工具，包括 Babel、Webpack 和 React，以便与 Rails 无缝协作。

用 Webpacker 生成一个新的 React 项目就像运行一个`rails`命令一样简单。但是如何为新的 React 组件编写测试就不太清楚了。有无数不同的 Javascript 代码测试库，这个生态系统可能有点混乱。

在本文中，我将展示如何使用 Jest 为我的 React 组件设置测试，以及如何将 Jest 集成到我的 Rails 应用程序的现有 CI 工作流中。

## 安装笑话

Jest 是一个 Javascript 测试库，安装简单，速度快。它是由脸书开发的，对测试 React 组件有很多支持。

因为 Webpacker 使用的是 Babel，所以设置 Jest 需要额外安装一个包:`babel-jest`。这允许 Jest 在测试时使用 Babel 来传输代码。用`bin/yarn` :
安装两者

```
bin/yarn install jest babel-jest 
```

Enter fullscreen mode Exit fullscreen mode

## 创建测试

默认情况下，Jest 会在一个`__tests__`目录中寻找任何文件或者任何以`*test.js`结尾的文件，并将这些文件视为测试套件。“套件”是 Jest 对包含一个或多个测试的文件的术语。

使用 Webpacker，React 组件存储在`app/javascript`目录中。我有一个这样的目录结构:

```
app/javascript
├── packs
└── pulls
    └── components 
```

Enter fullscreen mode Exit fullscreen mode

`components`目录是我的所有组件所在的位置，所以我在其中创建了一个`__tests__`目录。

## 跑笑话

为了运行 Jest，我在我的`package.json` :
中添加了一个新脚本

```
{  "scripts":  {  "test":  "jest"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以用纱线开玩笑了

```
bin/yarn test 
```

Enter fullscreen mode Exit fullscreen mode

然而，当我最初这样做时，我得到了一个错误:

```
$ bin/yarn test
yarn run v1.5.1
$ jest
 FAIL  config/webpack/test.js
  ● Test suite failed to run

    Your test suite must contain at least one test. 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的事情是 Jest 假设 Webpacker 的`test.js`配置文件是一个测试文件，因为它以`test.js`结尾。那显然是不对的！幸运的是，我们可以用 [`testPathIgnorePatterns`](https://facebook.github.io/jest/docs/en/configuration.html#testpathignorepatterns-array-string) 配置选项告诉 Jest 忽略某些文件模式。

Jest 配置在`jest`键下的`package.json`中。我们只需要将`config/webpack/test.js`添加到忽略路径列表中:

```
{  "jest":  {  "testPathIgnorePatterns":  [  "/node_modules/",  "<rootDir>/config/webpack/test.js"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 通天塔模块

我遇到的另一个错误是:

```
SyntaxError: Unexpected token import 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题是，因为 Jest 在 Node 中运行，所以它需要将您的代码编译成 CommonJS 模块。然而，默认情况下，Webpacker 用`"modules": false`配置 Babel，它告诉 Babel 跳过将 ES6 模块转换成另一种格式。

有两种方法可以解决这个问题。第一种方法是在测试环境中从 Babel 中移除`"modules": false`。你可以使用`.babelrc`中的[环境选项](https://babeljs.io/docs/usage/babelrc/#env-option)来实现。然而，当我尝试这样做时，当 Babel 传输我的组件时，我得到了不同的错误。最终，我决定这种方式不适合我。

第二种方法是将`babel-plugin-transform-es2015-modules-commonjs`添加到测试环境中。这个插件将把 ES 模块转换成 CommonJS 模块。我安装了插件，并把它添加到我的`.babelrc` :

```
{  "env":  {  "test":  {  "plugins":  ["transform-es2015-modules-commonjs"]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在运行`bin/yarn test`成功！

## 运行在特拉维斯 CI 上

我使用 [Travis CI](https://travis-ci.org/) 来运行我的测试，所以我也想在 Travis 上运行 Jest。将 test 命令添加到我的`.travis.yml`文件中很简单，但是 Jest 试图运行其他 Javascript 文件时，我开始收到更多的错误，只是因为它们以`test.js`结尾。

这是因为 Travis 将 Webpacker gem 安装到项目中的`vendor`目录，这将它放在 Jest 发现测试的可搜索目录的路径中。我必须将供应商目录添加到我的忽略路径列表中。

## 结论

总而言之，设置 Jest 与 Webpacker 一起工作有一些小问题，但它们都很容易克服。用 Jest 测试 React 组件是非常容易的，我很惊讶它与 Webpacker 的开箱即用配置配合得如此之好！