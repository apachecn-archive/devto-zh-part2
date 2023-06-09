# 如何修复导致浏览器错误的节点依赖关系

> 原文：<https://dev.to/errietta/-how-to-fix-your-node-dependencies-es6-causing-browser-errors-37di>

# 如何修复导致浏览器错误的节点依赖关系

如果你在这个时代使用现代 JavaScript 做任何事情，你可能会使用 es6，并使用 babel 将其转换回 es5，这适用于大多数浏览器。

这对您编写的代码来说很好，但是您的依赖关系呢？通常他们自己提供编译好的代码，所以不用担心。然而，有些可能不会(特别是如果他们的主要焦点不是用在浏览器上的话，嗯…你可能会在旧的浏览器(如 IE11)中得到如下错误:

```
 SCRIPT1014: Invalid character
    SCRIPT5009: 'webpackJsonp' is undefined 
```

[![A screenshot of the IE11 error screen showing ‘Invalid character’ and ‘webpackJsonp’ is undefined](img/778b2152d24f35ac5a8a5b8e79e1c8f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kkUYkqun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.errietta.me/annoyance.png)*IE11 错误屏幕截图显示“无效字符”和“webpackJsonp”未定义*

你的第一反应可能是离开这个行业，去放牧小山羊。

[![A baby pygmy goat looking out of a cave at Mudchute park & farm](img/2478e68042bdb7dc6dae525e43930b82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mO3p38XP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.errietta.me/goat.png)

然而，还有一个选择。或者至少，我在 github 上找到了[一个解决方案](https://github.com/vuejs-templates/webpack/issues/826#issuecomment-318769204)经过多次~~发拉~~谷歌搜索:

首先，不幸的是，您需要找出是哪个模块导致了这个问题。幸运的是，您的浏览器应该显示错误发生的代码。在本例中，该模块使用的是 es6 模板字符串，IE11 不支持:

[![A screenshot of the IE11 debug console showing an error occuring because the module in question was using es6 template strings](img/e70015d1def41f72c4e0911f669ee7d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mqeskcy---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.errietta.me/annoyance2.png)

如果您正在捆绑您的依赖项，错误在哪里并不明显。在这种情况下，我运气好，稍微向上滚动一下就很容易找到了罪魁祸首；结果是`csv-parse`(是的，我的邪恶实验让我在浏览器中解析 CSV 文件……)

[![A screenshot of the debug console showing part of csv-parse’s code where you can clearly see its name in a comment](img/94fbdb805e2be29f89a7a30683635ebe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JtOoRJw7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.errietta.me/annoyance3.png)

一旦你发现问题所在，你要做的就是告诉 babel 也要传输那个模块，因为你通常会排除所有的`node_modules`。

首先，如果你的 webpack.config.json 中有`exclude: /node_modules`，你必须去掉它。相反，使用`include: ['src']`(或者任何到源目录的路径)。

现在，您还必须添加有问题的模块。在这种情况下，这就是我的配置现在的样子(用有问题的模块名替换 CSV-parse):

```
 {
      test: /\.js$/,
      include: ['src', require.resolve('csv-parse') ],
      use: {
        loader: 'babel-loader',
        options: {
        }
      }
    }, 
```

此外，确保使用`babel-polyfill`来获取所有 es6 代码的 es5 polyfills。

例如，我将条目更改为:

```
 entry: [ 'babel-polyfill', './src/client/index.ts' ], 
```

就是这样。一旦你做了这些事情，在你重新编译之后，所有的错误都应该消失，你应该有从你的 es6 依赖项编译的普通的旧 es5 代码！

下面是我为了比较而做的改动:

```
 diff --git a/.babelrc b/.babelrc
    index 76a26b7..244d28b 100644
    --- a/.babelrc
    +++ b/.babelrc
    @@ -1,4 +1,3 @@
     {
    -    "presets": [ "es2015" ],
    -    "plugins": ["transform-runtime"]
    +    "presets": [ "es2015" ]
     }
    diff --git a/webpack.config.js b/webpack.config.js
    index 3796b22..3112198 100644
    --- a/webpack.config.js
    +++ b/webpack.config.js
    @@ -6,7 +6,7 @@ const env = process.env.NODE_ENV
     const UglifyJSPlugin = require('uglifyjs-webpack-plugin')

     module.exports = {
    -  entry: [ './src/client/index.ts' ],
    +  entry: [ 'babel-polyfill', './src/client/index.ts' ],
       output: {
         filename: 'dist/public/bundle.js'
       },
    @@ -21,11 +21,10 @@ module.exports = {
           },
           {
             test: /\.js$/,
    -        exclude: /(node_modules|bower_components)/,
    +        include: ['src', require.resolve('csv-parse') ],
             use: {
               loader: 'babel-loader',
               options: {
    -            presets: ['[@babel/preset-env](http://twitter.com/babel/preset-env)']
               }
             }
           }, 
```

非常感谢 github 上的 [KagamiChan](https://github.com/KagamiChan) 和 [johnwebbcole](https://github.com/johnwebbcole) 发现了这个[问题](https://github.com/vuejs-templates/webpack/issues/826)并发表了一个[解决方案](https://github.com/johnwebbcole/vue-webpack-bable-issue/commits/master)，我只是报道以防它帮助了其他人，而不是试图邀功:)