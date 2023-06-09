# 说吧，我要迁徙了！

> 原文：<https://dev.to/vblaha/yarn-it-im-migrating--341m>

[![img](img/e2ac8433caef930c6aa06b7d3ac5644d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ibh68YF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://yarnpkg.com/assets/feature-secure.png) 
我最近在一个项目上协助从 npm 到 Yarn 的迁移，它就像在终端中键入 *yarn* 一样简单。Yarn 是一个包管理工具，它通过终端命令创建模块(来自其他开发人员的代码块，您可以将其加载到您的程序中)，这与您使用 npm (Node.js 包管理器)的方式非常相似。它通过使用锁定文件来确保所有库在用户之间匹配(npm 客户端用户的一个常见抱怨是，用户之间可能存在多个版本的依赖关系，责任性下降，导致“在我的机器上工作”的态度)。所以有了 Yarn，你总是知道你在每台开发机器上都得到同样的东西。

您可以从 npm 迁移到 yarn，方法是在终端中导航到您的项目文件夹，并使用下面的比较图表来自定义您正在构建的包。

```
npm (v5)                            Yarn

npm install                         yarn install 
```

Enter fullscreen mode Exit fullscreen mode

```
(N/A)                                   yarn install --flat 
```

Enter fullscreen mode Exit fullscreen mode

Flat mode 是一个独特的特性，它可以解决依赖项版本不匹配的问题，创建一个单一版本——这有助于代码清理和删除重复项。

```
(N/A)                                   yarn install --har
npm install --no-package-lock           yarn install --no-lockfile
(N/A)                                   yarn install --pure-lockfile
npm install [package] --save            yarn add [package]
npm install [package] --save-dev        yarn add [package] --dev
(N/A)                                   yarn add [package] --peer
npm install [package] --save-optional   yarn add [package] --optional
npm install [package] --save-exact      yarn add [package] --exact
(N/A)                                   yarn add [package] --tilde
npm install [package] --global          yarn global add [package]
npm update --global                     yarn global upgrade 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令集将根据您选择的定制安装/添加依赖项并创建一个. lock 文件。

```
npm rebuild                         yarn add --force
npm uninstall [package]                 yarn remove [package] 
```

Enter fullscreen mode Exit fullscreen mode

remove package 命令非常有用。如果您在一个运行多个包管理工具的环境中工作，您将会很痛苦。尽量坚持一个。

```
npm run test                            yarn test 
```

Enter fullscreen mode Exit fullscreen mode

Yarn 的 lockfile 系统创建了一个本地缓存副本，便于离线安装软件包。这很有帮助，因为您可以在没有互联网连接的情况下安装 npm 软件包。在 Yarn 中，即使 npm 下降，您的测试也会通过！

这些只是纱线系统的一些基本概念。如需完整文档，请访问 [Yarn CLI 文档](https://yarnpkg.com/lang/en/docs/cli/)。虽然你可能会尝试 Yarn 并发现它不是最合适的，但它是一个有助于推动创新的伟大工具。一如既往，我欢迎提问、反馈和改进空间。感谢阅读！