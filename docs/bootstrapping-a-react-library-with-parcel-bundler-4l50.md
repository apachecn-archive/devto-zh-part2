# 用 package Bundler 引导 React 库

> 原文：<https://dev.to/josemunoz/bootstrapping-a-react-library-with-parcel-bundler-4l50>

##### *照片由 jn 在 Unsplash 上拍摄*

通常在做一个项目时，我会创建一些整洁的组件，我想在将来重用它们，并把它们保存在一个像样的回购中，供任何感兴趣的人使用。这些副业项目中的大部分都以回购墓地告终，或者甚至没有从原始项目中分离出来。将 webpack 和 babel 配置为 transpile 并公开 UMD 模块和演示页面可能是一项艰巨的任务。虽然 Webpack 4 已经引入了无配置操作，但今天我想与[package-bundler](https://parceljs.org/)分享我的经验。

默认情况下，package 是无配置运行的，它是一个非常快速灵活的捆绑器，可以与 Babel 7 无缝协作。我想向您展示实现轻松的库设置是多么容易。

## 文件夹结构

首先，我们需要用我们项目的名字创建我们的根文件夹，并用 yarn(或者 NPM，如果你喜欢的话)初始化它。

```
mkdir my-awesome-component 
cd my-awesome-component
yarn init 
```

Enter fullscreen mode Exit fullscreen mode

一旦文件夹被初始化，我们需要添加一些东西

```
 mkdir lib
mkdir example/src 
```

Enter fullscreen mode Exit fullscreen mode

这些文件夹将保存我们的源代码和我们将要编写的例子。这些并不是我们要使用的唯一文件夹，但现在，这是我们所需要的全部。

## 依赖关系

我将只谈论你完成一个环境所需要的一般依赖，这个环境提供你从 [CRA](https://github.com/facebook/create-react-app) 得到的大部分功能，使用 babel 7 和 Parcel 进行最少的设置。

注意:在编写这个包的时候，1.10 还在测试阶段，所以我将使用测试版，因为当前的稳定版本不支持 babel 7。

在最基本的层面上，我们需要 babel 来:转换到 ES5，聚合填充生成器，并支持[对象传播语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)。我们也将把包裹作为我们的捆扎机。

```
 yarn add --dev @babel/cli @babel/core @babel/plugin-proposal-object-rest-spread @babel/plugin-transform-regenerator @babel/plugin-transform-runtime parcel-bundler@^1.10 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用 [lodash.js](https://lodash.com) 或 [moment.js](https://momentjs.com) 你可能想添加巴别塔插件来从你的包中去掉那些额外的 kb。

既然我们在这里讨论 React，我将添加强制依赖项。请注意， *react-dom* 是为了在我们的演示中使用而添加的，并不一定在我们的库中。

```
yarn add react react-dom prop-types 
```

Enter fullscreen mode Exit fullscreen mode

## 配置通天塔 7

package 提供了一个默认的 babel 配置，它会为你做大部分的事情。但是，您可以定义自己的*。babelrc* 和 Parcel 将获取它并将其添加到他们的配置中。包将自动添加一些预置，如*环境*和*反应*，所以我们不需要设置，如果你检查巴别塔依赖我们甚至不需要包括一个预置，所以我们在这里只是设置我们的插件。我用的是 atom，随便用你喜欢的编辑器！

```
 atom .babelrc 
```

Enter fullscreen mode Exit fullscreen mode

然后添加

```
{
  "plugins": [
    "@babel/transform-runtime",
    "@babel/transform-regenerator",
    "@babel/proposal-object-rest-spread"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

巴贝尔就这样完了！

## 脚本

现在有趣的部分开始了。我们需要设置脚本，让我们的项目变得生动起来！打开你的 *package.json* ，前往*脚本*部分。

```
 atom package.json 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个 *start* 脚本来监视我们的源代码的变化，并将其实时编译到 */dist* (或者无论你怎么称呼它，我都将使用默认值)。

我们需要一个 *build* 脚本来编译和缩小我们的包，并为发布做好准备。

我们需要一个 *start-demo* 脚本来查看我们在 *examples/src* 中的例子的源代码

我们需要一个*构建演示*，这样我们的演示也可以被捆绑，并准备好在 [github 页面](https://pages.github.com/)上提供

我们还需要一个*发布-演示*脚本，这样我们就可以将这个包推送到 GitHub。

```
{
  "start": "parcel watch lib/index.js",
  "build": "parcel build lib/index.js --global my-awesome-component",
  "start-demo": "parcel example/src/index.html --out-dir examples/dist --open",
  "build-demo": "parcel build example/src/index.html --out-dir examples/dist --public-url ./",
  "publish-demo": "gh-pages -d examples/dist",
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意: **- global** 选项表示 UMD 软件包的名称，因此您需要提供您的 NPM 软件包将要使用的名称。此外，确保您的示例'**index.html**有一个引用您的 **index.jsx** 的脚本标签，并且 parcel 会处理剩下的部分*

## 额外

默认情况下，使用宗地生成源地图，它们是分析和优化包大小的强大工具。让我们利用他们。

[Source-map-explorer](https://github.com/danvk/source-map-explorer) 是一个 CLI，它基于您现有的源地图生成报告，我们将使用该工具来生成我们的包的可视化表示。

```
 yarn add --dev source-map-explorer 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加一个脚本来生成我们的源地图的 HTML 表示。

```
"view-source-map": "source-map-explorer --html dist/index.js dist/index.map > source.html && open source.html" 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以在你的浏览器上打开*source.html*,看看你能在哪里去掉那些多余的知识。

*注意:如果你运行的是 windows，你需要移除**打开**来启动生成的 html 到你的默认浏览器*

## 结论

有了我们在这里讨论的配置，你可以加入一些你在 CRA 做的东西，只需做很小的改动，然后把它作为自己的库发布，并在 GitHub 页面上提供一个很好的演示。我希望这能鼓励其他开发者开始使用 package，并让他们的想法更快地发表。感谢您的阅读。如果您读到这里，请留下评论分享您的想法！