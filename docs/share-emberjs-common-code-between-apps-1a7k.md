# 在应用程序之间共享 EmberJS 公共代码

> 原文：<https://dev.to/michalbryxi/share-emberjs-common-code-between-apps-1a7k>

## 弃用警告

我认为 *yarn workspaces* 是目前这个问题的一个更好的解决方案。我[写了一篇关于 yarn workspaces](https://dev.to/michalbryxi/share-common-code-with-yarn-workspaces-5g29) 和我遇到的陷阱的文章。请在阅读这篇文章之前检查一下。

## 小背景故事

最近，我一直在开发一个面向用户的 EmberJS 应用程序。范围已经扩大，我们需要实现一个管理员界面。出于各种原因，我决定*分离* EmberJS 应用将是一个很好的解决方案。

启动新的`ember-cli`项目，开始编码，从一开始，我注意到我基本上只是从原始应用程序复制粘贴授权代码。以后模特也是一样。最后，甚至一些组件似乎是可重用的。似乎还有改进的空间。所以我试着做到以下几点:

1.  **在应用程序之间尽可能多地重用**代码。
2.  确保**实时重新加载**仍然工作。我希望能够编辑*前端*、*管理*或*公共*代码，保存并立即看到结果。
3.  不用**在任何地方发布**通用代码。

## 实现

通读博客帖子和[论坛](https://discuss.emberjs.com/t/sharing-models-via-ember-cli-addons/6311/2?u=michal_bryxi)，我发现要走的路是一个[插件](https://ember-cli.com/extending/#create-addon)。

开始时，文件布局如下。我的两个项目的两个文件夹:

```
.
├── admin
└── frontend 
```

Enter fullscreen mode Exit fullscreen mode

接下来，生成具有公共代码的插件:

```
ember addon bp-ember-components 
```

Enter fullscreen mode Exit fullscreen mode

所以文件夹结构会是这样的:

```
.
├── admin
├── bp-ember-components
└── frontend 
```

Enter fullscreen mode Exit fullscreen mode

现在要在我们的应用程序中安装插件，只需将其添加到`package.json`文件中。使用相对文件路径，这样`yarn`就可以直接从你的磁盘中获取它。由于我在这个项目中使用了 [monorepo](https://en.wikipedia.org/wiki/Monorepo) ，甚至 *CI* 也利用了它只是一个“邻居”文件夹的事实。

```
//  admin/package.json  and  fronend/package.json  {  "devDependencies":  {  "bp-ember-components":  "../bp-ember-components/"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

为了启用[实时重新加载](https://ember-cli.com/extending/#link-to-addon-while-developing)的魔力，即使在公共回购中更改代码时，也要添加:

```
// bp-ember-components/index.js

module.exports = {
  isDevelopingAddon: function() {
    return true;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，[链接](https://yarnpkg.com/lang/en/docs/cli/link/)应用程序中`node_modules`的插件:

```
cd ./bp-ember-components
yarn
yarn link

cd ../admin
yarn link bp-ember-components

cd ../frontend
yarn link bp-ember-components 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。你可以开始放你的模型/组件/等等。进入`bp-ember-components`，它将在你的两个应用的**中可用。**