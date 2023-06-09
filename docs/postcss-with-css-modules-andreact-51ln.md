# 用 CSS 模块和 React 发布 CSS

> 原文：<https://dev.to/daveirvine/postcss-with-css-modules-andreact-51ln>

[![](img/f9d0f526b03464205424e8bebca5189b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vQFo0Q_x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fso5smcm6exzy8eqylmr.png)

## 更新

05/07/20-CSS-loader 的`localIdentName`属性现在是`modules`属性的子属性。

02/10/18 -看起来 Create React 应用程序现在增加了对 [CSS 模块](https://facebook.github.io/create-react-app/docs/adding-a-css-modules-stylesheet)的支持

## 快速介绍

CSS 模块是一种局部作用域类和动画名称的方法，有助于防止普通 CSS 所基于的默认全局作用域的失败。

PostCSS 提供了一种使用 JavaScript 插件转换样式的方法。

我们能让他们一起工作吗？我们为什么要这么做？先回答第二个问题。

## 这个为什么

[![](img/95a42744f2f006a9e4abb3ab19defa89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yFbfd7h8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/66z689a6md3ltvtl7eep.gif)

PostCSS 有一个很深很深的生态系统，我特别感兴趣的是 [postcss-preset-env](https://preset-env.cssdb.org/) 插件。使用这个插件，你可以获得最新的 CSS 特性，以及它们的填充。这个角色曾经由 [cssnext](http://cssnext.io/) 担任，但最近[弃用了](https://moox.io/blog/deprecating-cssnext/)，那么现在是探索 postcss-preset-env 的最佳时机吗？

我在使用 CSS 模块时遇到的一个早期问题是它的“变量”语法。虽然它们肯定存在，但我真的不喜欢语法，CSS 变量已经在 T2 规范中了，所以我们为什么要重新发明这个特殊的轮子呢？我不认为我是唯一有这种感觉的人，其他人似乎都在[问](https://github.com/gatsbyjs/gatsby/issues/4933)如何使用带有标准 CSS 变量的 CSS 模块，所以我们在这里。

将此与[一些](https://preset-env.cssdb.org/features#media-query-ranges) [相当](https://preset-env.cssdb.org/features#matches-pseudo-class) [甜蜜](https://preset-env.cssdb.org/features#nesting-rules) [未来](https://preset-env.cssdb.org/features#grid-layout) CSS 功能结合起来，我们有足够的理由将 PostCSS 与 CSS 模块捆绑在一起。

## 该如何如何

[![](img/c8231bef25f34c997bad8034ede865e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GNQKi7U6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gfq94zvlrv90au0nms6f.gif)

好吧，让我们开始吧。我希望你已经准备好让你的 *node_modules* 目录增长一点，我们有一些安装工作要做！

首先，让 CSS 模块在 React 应用程序的上下文中工作。

## CSS 模块

我们来获取[babel-plugin-react-CSS-modules](https://github.com/gajus/babel-plugin-react-css-modules)(有更长的 npm 包名吗？)已安装。它有一个运行时组件，所以它实际上属于你的*依赖关系*，而不是你的*依赖关系*。像这样安装它:

```
npm install babel-plugin-react-css-modules --save 
```

Enter fullscreen mode Exit fullscreen mode

确保你的*。babelrc* 或任何你用来配置 Babel 的等效物包括新的插件:

```
plugins: [‘react-css-modules’] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要配置 [Webpack](https://webpack.js.org/) 来告诉它如何加载 CSS 文件。这一次我们需要[风格加载器](https://github.com/webpack-contrib/style-loader)和 [css 加载器](https://github.com/webpack-contrib/css-loader)。这些是常规的 *devDependencies* ,所以要确保你是这样安装的。

```
npm install css-loader style-loader --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们告诉 Webpack 为 CSS 文件使用这些加载器。将以下内容添加到 webpack.config.js 中

```
{
  test: /\.css$/,
  use: [
    { loader: 'style-loader' },
    {
      loader: 'css-loader',
      options: {
        modules: {
          localIdentName: '[path]___[name]__[local]___[hash:base64:5]',
        },
      },
    },
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

那个 *localIdentName* 是怎么回事？好问题！当你打开 css 模块规范时，css-loader 会根据这个标识符改变你的 CSS 类。这意味着你可以有两个*。按钮*类在你的代码库中，它们不会冲突。

然而，这意味着当您向 React 组件添加一个类名时，您需要知道 css-loader 将把您的类名转换成什么，对吗？这就是 babel-plugin-react-css-modules 出现的原因。它会像 css-loader 一样对类名进行相同的管理，我们只需要确保它们被配置成使用相同的管理方法。

css-loader 中该选项的默认值与 babel-plugin-react-css-modules 不同，因此将其指定为*【路径】 *__【名称】* *【本地】* __【哈希:base64:5】*可以解决这个问题。

很好，现在在 React 组件中，您应该能够直接导入 CSS 文件了:

*App.css*

```
.app {
  border: 1px solid red;
} 
```

Enter fullscreen mode Exit fullscreen mode

*App.jsx*

```
import React from 'react';

import './App.css';

const App = () => (
  <div styleName="app">
    Hello, world!
  </div>
);

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

属性 *styleName* 是 babel-plugin-react-css-modules 对 *className* 的替换，但是你很快就会习惯它。

假设一切正常，您将拥有看起来像单词汤的类名:

[![](img/2dc83a4a8dd574a5da22c09c40d2ca1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YvU2Jb4S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ppngjkin15wctr63ijok.png)

## PostCSS

现在是有趣的事情。要安装的模块很多，所以让我们开始吧:

```
npm install postcss postcss-import postcss-loader postcss-preset-env postcss-url --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

我们需要更改我们的 *webpack.config.js* ,以确保 postcss-loader 得到使用:

```
{
  test: /\.css$/,
  use: [
    { loader: 'style-loader' },
    {
      loader: 'css-loader',
      options: {
        importLoaders: 1,
        modules: {
          localIdentName: '[path]___[name]__[local]___[hash:base64:5]',
        },
      },
    },
    { loader: 'postcss-loader' }
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要一个新的 *postcss.config.js* 文件

```
module.exports = {
  plugins: [
    require('postcss-import'),
    require('postcss-url'),
    require('postcss-preset-env')({
      browsers: 'last 2 versions',
      stage: 0,
    }),
  ],
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以尝试一下了！创建一个新的 *colors.css* 文件

```
:root {
  --errorRed: #e03030;
} 
```

Enter fullscreen mode Exit fullscreen mode

并更改 App.css 以使用它:

```
@import "../colors.css";

.app {
  border: 1px solid var(--errorRed);
} 
```

Enter fullscreen mode Exit fullscreen mode

那个时尚街区看起来怎么样？

[![](img/64d9e75448f90c98abeab57976a71790.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--snurvTjA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ulk9oh4j1eadfls4kxon.png)

不错！对于支持它的浏览器，您得到的是 *var()* 规则，对于不支持它的浏览器，您得到的是回退规则。

## 包装完毕

获得正确的模块组合来完成这项工作是真正的挑战，配置本身是相当容易的。

如果这对你不起作用，或者你认为我做错了，我很乐意在评论中听到你的意见。