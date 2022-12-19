# 先看 webpack ~ AoaH 十一

> 原文：<https://dev.to/link2twenty/first-look-at-webpack--aoah-eleven-29en>

# 先看 webpack ~ AoaH 十一

## 欢迎光临

你好，欢迎发表另一篇博文，本周我决定学习如何使用 webpack，尽管我确信我只会触及一些皮毛。如果这是你第一次阅读我的博客，请随意看看索引页，看看以前的帖子。

我本打算在本周开始一个新的更大的项目，但没有按计划进行。我想使用聚合物 Web 组件，但是它们是以这样一种方式编写的，你需要运行一个构建来使用它们，而且我对构建项目一无所知。这让我们来到这里。

## 为什么要装网页包？

Webpack 似乎是使用最广泛的软件包。除此之外，我真的没有任何理由，我认为学习一些东西来让你的头脑了解概念是很好的，然后你可以将你所学到的应用到任何包中，即使语法有点不同。

## 你做了什么？

嗯，这是我的第一个项目，所以很自然，我做了一个简单的`hello world`程序。它从一个模板生成一个 HTML 页面，显示包的版本并运行一些 javascript，这些 JavaScript 是从两个不同的文件捆绑而来的。

有两个运行命令`npm run dev-build`和`npm run build`，分别用于开发或生产构建。

### 为了那个赚饭桶真的值得吗？

可能没有，但我找到了，就在这里。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[ignis-pwa](https://github.com/ignis-pwa)/[web pack-exercise](https://github.com/ignis-pwa/webpack-exercise)

### 学习 webpack 的快速练习

<article class="markdown-body entry-content container-lg" itemprop="text">

# 网络包-练习

学习 webpack 的快速练习

</article>

[View on GitHub](https://github.com/ignis-pwa/webpack-exercise)

## 你是怎么做到的？

首先，我初始化了项目，并添加了我想要使用的依赖项。

```
npm init
npm install --save-dev webpack
npm install --save-dev webpack-cli
npm install --save-dev html-webpack-plugin 
```

然后我创建了`webpack.config.js`和`production.config.js`来存储两个不同构建过程的配置。

最后，我将命令添加到我的`package.json`中，这样我就可以调用构建过程

```
"scripts": {
  "build": "webpack --config production.config.js",
  "dev-build": "webpack --config webpack.config.js"
} 
```

当然，我还得写一个模板文件和我的 javascript。

### 显示代码！

我认为只有模板的东西才是有趣的，我相信你们都知道 javascript 导入是什么样子的。

`.\src\templates\index.hmtl`

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    
      <%= htmlWebpackPlugin.options.title %>
    
  </head>
  <body>
    <h1>Hello, Webpack!</h1>
    <p> This is version: <%= htmlWebpackPlugin.options.packageVer %>
    </p>
  </body>
</html> 
```

然后在`config.js`中的

```
plugins: [
  new HtmlWebpackPlugin({
    template: path.resolve(__dirname, "src/templates/index.html"),
    title: package.description,
    packageVer: package.version
  })
] 
```

构建完成后，`index.html`的一个版本将会出现`.\build\`。如果你想浏览我的代码，请直接去 git。

## 帖子结束

再次感谢你阅读我的漫谈。希望有了这些知识，我就可以开始我的下一个项目。它叫做`project libratuari`，和往常一样，我将在 dev.to 上写下我的经历。

再次感谢。

🦄❤🦄🦄❤