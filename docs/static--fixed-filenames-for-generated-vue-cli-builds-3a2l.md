# 生成的 vue-cli 版本的静态/固定文件名

> 原文：<https://dev.to/coolgoose/static--fixed-filenames-for-generated-vue-cli-builds-3a2l>

如你所知， [vue-cli](https://cli.vuejs.org/) 3 的发布已经临近(目前处于 RC3 状态)。

我真的很喜欢开始项目的简化方式和良好的开发基线(特别是当试图在内部建立一个良好的起点时，它有文档记录并且正在积极开发)。

然而，默认设置对遗留项目并不友好，因为 vue-cli 会隐式地在生成的文件名中添加一个哈希。如果你开始一个新的项目/SPA，这很好，因为它就像一个内置的缓存克星，但如果你试图将它与你最喜欢的 c#/php/python/ruby 等应用程序集成，这并没有帮助。

为了改变这一点，让我们快速浏览一下下面的配置

```
let assetsDir = "assets";

module.exports = {
  assetsDir: assetsDir,

  configureWebpack: {
    output: {
      filename: assetsDir + "/[name].js",
      chunkFilename: assetsDir + "/[name].js"
    }
  },

  chainWebpack: config => {
    if (config.plugins.has("extract-css")) {
      const extractCSSPlugin = config.plugin("extract-css");
      extractCSSPlugin &&
        extractCSSPlugin.tap(() => [
          {
            filename: assetsDir + "/[name].css",
            chunkFilename: assetsDir + "/[name].css"
          }
        ]);
    }

    config.plugins
      .delete("html")
      .delete("prefetch")
      .delete("preload");
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

由于`assetsDir` [不适用于自定义文件名](https://github.com/vuejs/vue-cli/issues/1753)，我们做了一个小的变通，定义了一个变量，并将其用于我们生成的文件名。

然后，我们使用各自的选项设置 javascript 和 css 文件名，并删除生成 index.html 文件的 html 插件及其“依赖项”预取和预加载。

现在你可以自由使用`npm run build --modern`并设置[现代模式](https://cli.vuejs.org/guide/browser-compatibility.html#modern-mode)

> 最新消息:
> 
> 官方文件以一种更清晰的方式引用了这个设置