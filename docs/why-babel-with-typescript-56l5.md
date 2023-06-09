# 为什么巴别塔有打字稿？

> 原文：<https://dev.to/saltyshiomix/why-babel-with-typescript-56l5>

一开始我很奇怪为什么 Babel 支持 TypeScript。我无法想象如何在 Babel 中使用 TypeScript。

这篇文章解释了我如何在 Babel 和 Webpack 中使用 TypeScript。

## TLDR

看这个`webpack.config.js` :

```
module.exports = {
  resolve: {
    extensions: ['.js', '.jsx', '.ts', '.tsx']
  },
  module: {
    rules: [
      {
        test: [/\.jsx?$/, /\.tsx?$/],
        use: 'babel-loader',
        exclude: /node_modules/
      }
    ]
  }
} 
```

使用`babel-loader`到`/\.tsx?$/`和`/\.jsx?$/`一样？！

是的，`babel-loader`。我一步一步地解释如何做到这一点。

## 它是如何工作的？

如此简单。

多亏了`@babel/preset-typescript`，我们才能通过`babel-loader`来处理`/\.tsx?$/`。

参见下面的`.babelrc`:

```
{  "presets":  [  "@babel/typescript"  ],  "plugins":  [  "@babel/proposal-class-properties",  "@babel/proposal-object-rest-spread"  ]  } 
```

## 演示

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[saltyshiomix](https://github.com/saltyshiomix)/[web pack-type script-react-starter](https://github.com/saltyshiomix/webpack-typescript-react-starter)

### Webpack + TypeScript + React = ❤️

<article class="markdown-body entry-content container-lg" itemprop="text">

网络包+打字稿+反应= <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji>

[![Package License (MIT)](img/31ecc76acf96ae6a77da82568de7ab53.png)T2】](https://camo.githubusercontent.com/26e1ae492338d184e243858cb7c72f8e86bda784/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f73616c74797368696f6d69782f7765627061636b2d747970657363726970742d72656163742d737461727465722e737667)

从 Babel v7 开始，现在我们可以像处理`.js`或`.jsx`文件一样处理`.ts`或`.tsx`文件，如下所示:

```
// webpack.config.js
module.exports = {
  resolve: {
    extensions: ['.js', '.jsx', '.ts', '.tsx'],
  },
  module: {
    rules: [
      {
        test: [/\.jsx?$/, /\.tsx?$/],
        use: 'babel-loader',
        exclude: /node_modules/,
      },
    ],
  },
}
```

**用`babel-loader`到`/\.tsx?$/`？！**

是的，`babel-loader`。参见`.babelrc`:

```
{
  "presets": [
    "@babel/env"
    "@babel/react"
    "@babel/typescript"
  ]
}
```

感谢`@babel/preset-typescript`，我们可以像处理`/\.jsx?$/`文件一样处理`/\.tsx?$/`文件:)

## 使用

```
# installation
$ yarn
# development mode
# it automatically opens `http://localhost:8080` in your default browser,
```

…</article>

[View on GitHub](https://github.com/saltyshiomix/webpack-typescript-react-starter)

**package.json** 非常简单，所以你可以想象如何使用:

```
{  "name":  "babel-typescript-react-boilerplate",  "scripts":  {  "check-types":  "tsc",  "start":  "webpack-dev-server --mode development"  },  "dependencies":  {  "react":  "^16.6.3",  "react-dom":  "^16.6.3"  },  "devDependencies":  {  "@babel/core":  "^7.1.6",  "@babel/plugin-proposal-class-properties":  "^7.1.0",  "@babel/plugin-proposal-object-rest-spread":  "^7.0.0",  "@babel/preset-env":  "^7.1.6",  "@babel/preset-react":  "^7.0.0",  "@babel/preset-typescript":  "^7.1.0",  "@types/react":  "^16.7.6",  "@types/react-dom":  "^16.0.9",  "babel-loader":  "^8.0.4",  "html-webpack-plugin":  "^3.2.0",  "typescript":  "^3.1.6",  "webpack":  "^4.25.1",  "webpack-cli":  "^3.1.2",  "webpack-dev-server":  "^3.1.10"  }  } 
```

## 结论

在`bable@7`之前，我们需要通过`babel-loader`web pack JavaScript 文件，通过`ts-loader`或【TypeScript 文件。

因此，设置文件`webpack.config.js`有时非常复杂，难以维护。

TypeScript with Babel 使它变得更容易，并为我们提供了更好的开发体验:)

## 其他引用

[与巴别塔的打字稿:一场美丽的婚姻](https://iamturns.com/typescript-babel/)