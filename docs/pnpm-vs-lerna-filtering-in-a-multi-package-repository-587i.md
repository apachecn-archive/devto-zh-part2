# pnpm 与 Lerna:在多包存储库中过滤

> 原文：<https://dev.to/zkochan/pnpm-vs-lerna-filtering-in-a-multi-package-repository-587i>

大家都听说过 [Lerna](https://github.com/lerna/lerna/) ，它是“一个用多个包管理 JavaScript 项目的工具。”很少开发人员听说过 [pnpm](https://github.com/pnpm/pnpm) ，它是一个快速、磁盘空间高效的 JavaScript 包管理器。Lerna 和 pnpm 都试图改进多包存储库(MPR)的工具。对 Lerna 来说，这是创作的原因。对于 pnpm，MPR 支持是一个很好的额外特性，它是通过一组名为[递归](https://pnpm.js.org/en/cli/recursive)的命令实现的。当然，Lerna 管理多包回购的方式与 pnpm 有许多不同之处。在本文中，我想比较一个看似简单的方面:过滤。

MPR 中的过滤很重要，因为在开发过程中，更改主要是在一个或两个包中进行的。如果只修改了几个包，那么在整个存储库上运行命令是没有意义的。

[![](img/f1ed77b442212989fe6422a8e9221336.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FYwwtCtb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8zIYqAk.png)

## 在 Lerna 中过滤

Lerna 中的过滤(从`v3.2.1`开始)通过以下标志实现:

*   `scope` -仅包含名称与给定 glob 匹配的包。
*   `include-filtered-dependents` -运行命令时包括所有可传递的从属对象，不管是`--scope`、`--ignore`还是`--since`。
*   `include-filtered-dependencies` -运行命令时包括所有可传递的依赖关系，不管是`--scope`、`--ignore`还是`--since`。
*   `ignore` -排除名称与给定 glob 匹配的包。
*   `private` -包括私人套餐。Pass - no-private 排除私有包。
*   `since` -仅包括自指定的[ref]以来更新的包。如果没有传递 ref，则默认使用最新的标签。

这些标志使得 Lerna 中的过滤非常强大。然而，它们很难打字。假设您下载了一个存储库，并希望只在`login-page`组件上工作。您可能想要运行`login-page`及其任何依赖项:
的安装

```
lerna bootstrap --scope login-page --include-filtered-dependencies 
```

或者，您可能更改了一个名为`site-header`的组件，并且想要在所有依赖包上运行测试:

```
lerna run test --scope site-header --include-filtered-dependents 
```

这些标志不仅很难输入，而且很难记忆，很容易混淆。

[![](img/b1ff15c4666a473940bf6e1c773a0183.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0AeKbXWz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/qlW1eEG.png)

## 在 pnpm 中过滤

与 Lerna 不同，pnpm 使用特殊的包选择器语法来限制它的命令。因此，您不需要记住一组很长的标志名，而只需要记住一个非常容易记住的选择器语法。

从版本`2.15.0`开始，pnpm 支持以下选择器:

*   `<pattern>` -将范围限制为匹配给定模式的包名。例如:`foo`、`@bar/*`
*   `<pattern>...` -包括匹配包的所有直接和间接依赖关系。例如:`foo...`
*   `...<pattern>` -包括匹配包的所有直接和间接依赖项。例如:`...foo`、`...@bar/*`
*   `./<directory>` -包含给定子目录中的所有包。例如:`./components`
*   `.` -包括当前工作目录下的所有包。

这些过滤器可通过`--filter`标志或在命令末尾的`--`后指定。

> 注意:从`v2.15.0`开始，`run`、`exec`、`test`不支持`--`之后的滤镜

因此，如果您想要引导`login-page`及其所有依赖项，这是您使用 pnpm 的方式:

```
pnpm recursive install -- login-page... 
```

如果您想要运行`site-header`及其所有依赖项的测试，使用`...<pattern>`选择器:

```
pnpm recursive test --filter ...site-header 
```

当然，你可以任意组合多个选择器:

```
pnpm recursive update -- ...site-header login-page... ./utils @style/* 
```

上面的命令更新了:

*   `site-header`
*   `site-header`的家属
*   `login-page`
*   `login-page`的依赖关系
*   位于`utils`目录中的所有包
*   来自`@style`范围的所有包

* * *

pnpm 可能还没有 Lerna 提供的所有特性，但是对于许多用户来说，这已经足够了。

如果你还没有听说过 pnpm，我建议你也阅读一下 [Flat node_modules 不是解释 pnpm 创建的独特 node_modules 结构的唯一方式](https://medium.com/pnpm/flat-node-modules-is-not-the-only-way-d2e40f7296a3)。

## Cheatsheet

| 粘土 v3.2 | pnpm v2.15 版 |
| --- | --- |
| -范围我的-组件 | -我的-组件 |
| -范围工具栏-* | -工具栏-* |
| -限定我的组件-包含-过滤-依赖关系的范围 | -我的-组件... |
| -确定我的范围-组件-包含-过滤-从属 | -...我的组件 |

* * *

*最初发布于 [pnpm 博客](https://medium.com/pnpm/pnpm-vs-lerna-filtering-in-a-multi-package-repository-1f68bc644d6a)T3】*