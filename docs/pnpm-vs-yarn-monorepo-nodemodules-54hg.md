# pnpm vs Yarn: monorepo 节点模块

> 原文：<https://dev.to/zkochan/pnpm-vs-yarn-monorepo-nodemodules-54hg>

[pnpm](https://pnpm.js.org/) (从 v2.17 开始)和 [Yarn](https://yarnpkg.com/en/) (从 v1.12 开始)都支持 monorepos 中的快速并发安装。然而，它们在 monorepos 中存储依赖关系的方式有很大的不同。Yarn 试图将所有工作空间包中的所有依赖项提升到 monorepo 的根`node_modules`中，这意味着包可以访问工作空间中其他包的依赖项。这在[纱线文档](https://yarnpkg.com/lang/en/docs/workspaces/#toc-limitations-caveats)中有描述:

> 在工作区中发布包时要小心。如果您正在准备您的下一个版本，并且您决定使用一个新的依赖项，但是忘记在 package.json 文件中声明它，那么如果另一个包已经将该依赖项下载到工作区根目录中，您的测试仍然可能在本地通过。然而，对于从注册表中提取它的消费者来说，它将被破坏，因为依赖项列表现在不完整，所以他们没有办法下载新的依赖项。目前，在这种情况下没有办法抛出警告。

像纱线一样，pnpm 将所有包装提升至根部。然而，pnpm 将所有依赖项存储在一个隐藏的文件夹中，并且只将每个包的直接依赖项链接到它们的`node_modules`中。

让我们通过一个简单的例子来看看 Yarn 和 pnpm 是如何工作的:

*   有一个小的 monorepo 有两个包:`foo`和`bar`
*   `foo`有`is-negative@1.0.0`作为依赖
*   `bar`有`is-positive@1.0.0`作为依赖

## 纱线在行动

要使这个回购与 Yarn 一起工作，在回购的根中应该有一个`package.json`，其内容如下:

```
{  "private":  true,  "workspaces":  ["foo",  "bar"]  } 
```

Enter fullscreen mode Exit fullscreen mode

要安装所有工作空间包的所有依赖项，您应该运行`yarn install`。

> 在此查看结果

在这种情况下，Yarn 将在回购的根中创建一个包含`is-negative`和`is-positive`的单个`node_modules`。这意味着`foo`和`bar`都可以访问彼此的依赖关系。

## pnpm 在行动

要让 pnpm 在这个 repo 中安装依赖项，您需要在 repo 的根目录下创建一个`pnpm-workspace.yaml`(可以为空)和一个`.npmrc`文件，内容如下:

```
shared-workspace-shrinkwrap = true
link-workspace-packages = true 
```

Enter fullscreen mode Exit fullscreen mode

要用 pnpm 安装所有工作空间包的所有依赖项，应该运行`pnpm multi install`(或者只运行`pnpm m i`)。

> 在此查看结果

像 Yarn 一样，pnpm 在回购的根中创建一个`node_modules`。然而，如果你打开那个文件夹，你会看到所有的目录和文件都隐藏在那里:

```
node_modules
+ .registry.npmjs.org
+ .modules.yaml
+ .shrinkwrap.yaml 
```

Enter fullscreen mode Exit fullscreen mode

这个`node_modules`存储了所有的依赖项，但是它们被隐藏在`.registry.npmjs.org`中。节点的解析算法不会找到它们。

现在，如果您检查 bar 的[目录，您也会在那里看到一个`node_modules`，带有一个名为`is-negative`的符号链接。这个符号链接指向`../../node_modules/.registry.npmjs.org/is-negative/1.0.0/node_modules/is-negative`。同样，在`foo`的`node_modules`目录中有一个指向`is-positive`的符号链接。所以`foo`只能解析`is-positive`，而`bar`只能解析`is-negative`🎉😊](https://github.com/zkochan/comparing-monorepo-node-modules/tree/master/pnpm-example/foo)

* * *

如您所见，pnpm 不仅在与单个`package.json` 一起使用时是严格的[，在多包存储库中也是如此。](https://medium.com/pnpm/pnpms-strictness-helps-to-avoid-silly-bugs-9a15fb306308)