# 如何设置 Webpack 项目根目录？

> 原文：<https://dev.to/mindplay/how-to-set-the-webpack-project-root--1b00>

我最近才开始使用 WebPack。(用 Typescript，我用了很久了，最近开始 Preact。)

到目前为止，我对它真的很满意——它工作得很好，无论是在开发期间，还是在构建生产资产时。

我在这里的问题其实很简单:一个 webpack 项目是如何扎根的？它似乎不会自动从`package.json`或`webpack.config.json`文件的位置找到自己的根，我也找不到一个选项来找到它的根。

我有一个结构如下的项目:

```
root/
  package.json
  styleguide/
    webpack.config.js
    styleguide.tsx
    tsconfig.json 
```

Enter fullscreen mode Exit fullscreen mode

在这个项目中，除了`styleguide`之外，我还有其他的子项目——那个正好使用`webpack`,其他的使用例如`tsc`或`node-sass`等。这就是为什么它的结构是这样的。

我的 webpack 配置如下:

```
const path = require("path");
const webpack = require("webpack");

module.exports = {
    entry: {
        "styleguide-deps": ["prismjs", "preact", "js-beautify"],
        "styleguide": "./styleguide.tsx"
    },

    output: {
        filename: "[name].js",
        path: path.join(__dirname, "/../assets/demo/"),
        libraryTarget: "amd"
    },

    plugins: [
        new webpack.optimize.CommonsChunkPlugin({
            name: "styleguide-deps",
            minChunks: Infinity,
        })
    ],

    // Enable sourcemaps for debugging webpack's output.
    devtool: "source-map",

    resolve: {
        // Add '.ts' and '.tsx' as resolvable extensions.
        extensions: [".ts", ".tsx", ".js", ".json"]
    },

    module: {
        rules: [
            // All files with a '.ts' or '.tsx' extension will be handled by 'awesome-typescript-loader'.
            { test: /\.tsx?$/, loader: "awesome-typescript-loader" },

            // All output '.js' files will have any sourcemaps re-processed by 'source-map-loader'.
            { enforce: "pre", test: /\.js$/, loader: "source-map-loader" }
        ]
    },

    // Don't bundle these modules, but request them at runtime (client-side, via require.js)
    externals: {
        "kodus": "kodus"
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我的`package.json`有一个`scripts`条目，如下所示:

```
 "scripts": {
        "build-styleguide-ts": "cd styleguide && webpack",
    }, 
```

Enter fullscreen mode Exit fullscreen mode

我要问的问题是，为什么我必须`cd`进入`styleguide`才能运行 webpack？

我已经从项目根尝试了例如`webpack --config styleguide/webpack.config.json`，但是我不能让它工作。

我可以在大多数地方使用绝对路径，但不能用于`entry.styleguide`，而且似乎`./`相对于当前目录工作，而不是项目根目录。

我的反对意见是，任何脚本都不应该依赖于当前目录，因为这只是全局状态，我通常在任何地方都使用绝对路径，但这在 webpack 上对我不起作用。

如何删除对当前目录的依赖？