# 如何用 webpack 4 和 Babel 7 构建自己的 React 样板文件

> 原文：<https://dev.to/itzsaga/how-to-build-your-own-react-boilerplate-with-webpack-4--babel-7-510c>

所以你已经学会了一些[反应](https://reactjs.org/)并且建造了一些东西。你可能用过 [create-react-app](https://github.com/facebook/create-react-app) 或者 [react-slingshot](https://github.com/coryhouse/react-slingshot) 以最小的配置快速起飞。然而，现在您已经准备好冒险使用自己的 React 样板文件了。

[![react boilerplate freedom](img/bb39f974bf1d793f98efca83111d50af.png)T10】](https://i.giphy.com/media/aysBMSvOiagPm/giphy.gif)

## 何处。敬。开始？？？

我最近面临着同样的困境。这是一场斗争，但最终，我有了一个可靠的回购，可以作为未来 React 项目的基础。我意识到我想使用 [webpack](https://webpack.js.org/) ,因为这是一个新的热点(也不是很新),但是我需要很多资源来解决这个问题。我对[咕噜声](https://gruntjs.com/)或[吞咽声](https://gulpjs.com/)一无所知**零**尽管我观察过它们的数量很少，但它们在自己的权利范围内做了一些很酷的事情。此外，我知道与我互动的人比我更了解使用 webpack 的人。我还有一个 reference React 样板 webpack 配置，我将使用它作为基础(我很快意识到我将重写这个配置的大部分)。

## 回到开始

让我们把它拿回来。什么是真正的样板文件？Dictionary.com 没能给我一个可行的定义。[牛津词典](https://en.oxforddictionaries.com/definition/boilerplate)另一方面端端正正的 W 着:

> 用作合同条款或计算机程序一部分的标准化文本。

这个管用。所以这是计算机程序的标准。在某些代码中，至少在理论上是一样的。因此，每次我们开始一个项目时，我们可以复制我们的样板文件，知道我们想要的所有基本部分都准备好了，就可以开始比赛，而不是必须运行类似`npm i react react-dom webpack`等的东西。我们还会知道一切都在按照我们希望的方式进行维护，因为这是我们的。在这个过程中，我们还会学到很多东西，甚至可能会做出与他人指示不同的决定。

## [走吧](#get-git)

首先，让我们创建一个本地文件夹，并将其作为 Git 存储库。

[![git init](img/ec6602ed9c0f59480930af762f478c69.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--hAxqAAIb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w51rs3xiriis0xpgf0zz.png)

## 先做第一件事

现在，如果没有`README.md`，这将是什么类型的项目？每个存储库都应该有一个自述文件。即使只是提醒你事情是如何运作的。相信我，现在看起来你会准确地记得每个脚本的作用和原因。然而，一年后这些自述文件会派上用场！相信我。所以，在项目的根中创建一个`README.md`。把这样的东西放在那里:

[![readme](img/b9a70803a08645a3c7d584ac0478d59e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k1wECuZK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h8d1twvc0ua5mx1c7tvg.png)

确保在您做出任何更改后将您的文件提交到 Git:

[![commit](img/2c59e4c8e0fecef5961d23df367a2699.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mY6jpFA8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zkjyhsrexdt4cfox82ep.png)

## 文件夹结构

这个很简单。制作如下的文件夹结构来存放所有的东西:

[![folder structure](img/2d4b101e31674800e7873bc9a75731b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OtumL2Ql--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5vgtet3pt16koso9z7uz.png)

你可以用这个命令轻松完成这个任务:

[![mkdir](img/f5f183e8cbe9f43e0fa8d10ee9bc9f32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UX4fGtJw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9db0et0jmemzc5feleh6.png)

## 。吉蒂尔

任何 Git 项目的一个重要部分就是`.gitignore`文件。这告诉 git 不要将某些文件置于版本控制之下。这对于像包和其他我们将从存储库中检索的东西是很重要的。我们不仅在版本控制中不需要这些，而且如果我们在版本控制中包含它们，实际上是有害的。至少我很确定这是有害的。在你的项目根目录下创建一个`.gitignore`文件，并添加如下内容:

[![gitignore](img/6f42aa1744b1d1e29ce208b23311ad46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NIeoVOn5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dqboxyle91yob69az0vg.png)

这个文件有生成器，比如[这个](https://www.gitignore.io/)，但是这对我们今天所做的应该足够了。

## npm

所有节点项目都必须初始化，这样我们才能使用包管理器。这将为我们创建一个`package.json`文件，它必须在版本控制中。

它包含许多内容，但最重要的是:

*   所有已安装的软件包及其允许的语义版本列表
*   剧本

要开始这个过程，请在您的终端项目的根目录中输入以下内容:

[![npm init](img/efd88d3435941e561c1063d2cdd38226.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dCovtcOq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/le4227wfcxc5ynlrghxb.png)

你将会被问到不止几个问题，你可以随意地按下回车键，让它全部空白，最后你会得到一个闪亮的新`package.json`，看起来像这样:

[![package.json](img/a6e8ffc172703380c620d6e8d5045adf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OnQxCPUV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ffaeycbt9tqzr0o9lczd.png)

## HTML

是啊！是时候写一些代码了！嗯，这可能是你写过的最无聊的 HTML 了。在你的项目的根目录下创建一个`index.html`，并像这样设置它:

[![html](img/94caec2e7a88ca6bb424fe5b081d90de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yINKmgJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aziomcyvdy2kfnlr1sqa.png)

## 做出反应

现在，让我们将 React 添加到项目中。

[![react install](img/5077a336d946184bbdbb68519e65b6bc.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--UDWvkN8Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mobsyp2ps4zf7y7ennf9.png)

创建这个文件`/src/components/App.js`，并在其中放置以下内容:

[![app 1](img/0bc9d59e41837fed9a3cfc37fb0253bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EWDEl75I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fdodi81gikggkxe8u8p6.png)

然后，我们将通过一个索引来呈现该应用程序，我们将在`/src/index.js`创建该索引，并将以下内容放入:

[![index 1](img/e35470b0f5551e0ea17d23e6bbaa0e1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--451yourh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7lsdozd9hwpruyr4q7f1.png)

## 通天塔

巴别塔很牛逼。它让我们编写最新最棒的 JavaScript，同时确保我们与发布的代码有最大的兼容性。首先，我们必须安装巴别塔和一些额外的巴别塔软件包，使这一切一起工作。别担心，很快会解释清楚的。

[![babel install](img/96a93a20e8dd5682f8f1cab48b6f342a.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ciu56s6K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5r2ucz65crhz4fz6r9pv.png)

然后，我们需要在项目的根目录下创建一个`.babelrc`，并添加以下内容:

[![bable presets](img/1748d1076709eb2c5f9521c5c16b6f1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nVaFa_Gw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ov7zhs52u7v88rwp6k2b.png)

现在，这到底是干什么的？`env`预置隐含地包括`babel-preset-es2015`、`babel-preset-es2016`、`babel-preset-es2017`和`babel-preset-latest`在一起，这意味着你可以运行 ES6、ES7 和 ES8 代码。

我认为的预置非常简单明了，但是，如果 React 是 JavaScript，你可能会想为什么我们需要它。那是因为巴别塔不知道该拿反应过来的& JSX 怎么办。

注意:我从这次更新中删除了第二阶段预设。如果你想知道为什么，请阅读巴别塔团队的这篇伟大的文章:[移除巴别塔的舞台预设](https://babeljs.io/blog/2018/07/27/removing-babels-stage-presets)。

## 测试

所以我们正在取得进展。现在我们有了一个带有`App.js`的 React 组件，让我们确保为该组件创建一个简单的关联测试。通过这种方式，我们开始确保对我们构建的东西进行测试的一些良好实践。我们将在这个项目中使用 Jest 和酶。先说第一件事，我们先把它装上:

[![jest enzyme install](img/58681e72f22912b2a60c2a83e8dba7cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bryepwyw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ee9b5g7j83e62p81nynm.png)

现在创建`/test/enzyme.setup.js`并放入:

[![enzyme setup](img/ab8b0517c2c5ecd23d05c3041edbfd43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j04-e_gn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/amunucwpq3twq8niubjf.png)

我们需要将 Jest 功能添加到我们的`package.json`中，因此添加以下内容:

[![jest functionality](img/82569929e0a96ecdc6102171667d06fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZwDcn9Qv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tmo61ptc4xppvwpiiq4m.png)

接下来，我们应该添加我们的第一个组件测试！因此，在`/test/App.test.js`创建一个文件来测试我们的应用程序组件是否按照预期呈现。我们还将实现一个快照测试，以确保我们的组件结构不会随着测试的不同而改变。要做到这一点，我们将需要以下:

[![app test](img/7ac36da7d43e114c890d408c78d434a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gmQO9Q4J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7j5vzin0bj84i2447kqq.png)

您可能想知道如何运行这个新测试。为此，我们需要将`package.json`中的`test`脚本改为:

[![test script](img/2c520a9dd7afea035a194ea7f9008e49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CbcmoeFi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/um7aeeoj2qqyq15rygm1.png)

您现在可以使用`npm test`从您的终端运行测试，应该会看到类似这样的内容:

[![jest run](img/37173dd3bd4734fdafee18b0e9fa9eb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_QGbEIU0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h9es0tjfh7n70eb4xxcf.png)

### 休息时间

如果您已经使用 React 样板文件做到了这一步，那么恭喜您！主要是因为你已经意识到我在代码中使用图像，你必须把它们全部打出来。我知道这很让人头疼。不过请相信我，通过被迫输入，你学到的东西比你知道的要多，肌肉记忆将在本教程结束后很长一段时间内为你服务。

## webpack

webpack 将允许我们模块化我们的代码，并轻松地将其捆绑到一个文件中用于生产。我认为很多人真正喜欢 webpack 的地方是开发服务器！我们将从安装 webpack 开始，使用:

[![webpack install](img/c56c1798f4e3ef385599b2c288e086fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RTFiRgQs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u1ienhv1yd8wsur2vivw.png)

默认情况下，webpack 会在项目的根目录中查找一个`webpack.config.js`文件，所以让我们创建这个文件，并向其中添加一些内容:

[![entry output](img/560e48da302aeb87d6d874bb543ebf29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_F-OO6ac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n7n4ltv80feq8d42fezb.png)

`entry`告诉 webpack 在哪里可以找到基本的 JavaScript 文件。在我们的应用程序中，这是`index.js`。然后，它告诉它在完成构建文件后将它输出到哪里。

### 网页包充电器

加载器是有用的部件，我们可以添加到 webpack 中，使它更强大，并对其他文件类型做一些事情。在 webpack 正常工作之前，我们需要设置它与 ES6 和 JSX 一起工作。我们将通过`babel-loader`来实现这一点。使用

[![babel-loader install](img/0e24315acfec11c61374386f4d9bea67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T_v9LyVB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/62a45hmtf66g3guzqy0x.png) 将`babel-loader`添加到您的项目中

然后将加载器添加到您的`webpack.config.js`中，就像这样:

[![config with babel-loader](img/3e679cdedd6aa4e5d8b9cc21e1d89188.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QvYmXbSf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yv0ecjfjiymzasjs5ywi.png)

为了利用萨斯和 SCSS，我们需要另一台装载机。现在，为了获得最佳的“性价比”,我们将把三个加载器链接在一起，这样我们的样式可以立即应用到 DOM。让我们安装装载机:

[![add sass](img/67f78bc3b93ddaa5db773443f5634131.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uZcjEsGs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1bn4yrywqwzlffneshtc.png)

并在我们的`webpack.config.js` :

[![config with sass added](img/a51d4e79eac3226c8dbf2b51c846f658.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ILhIc_Za--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/km8o0vixi0yoeclo4htz.png) 中这样配置

因为我们刚刚启用了一些样式支持，所以让我们添加一些。创建`/src/styles/style.sass`并放入:

[![sass code](img/b6696324794aed67d57605255c008405.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oDuqA0JY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8oo6ll3fa3aynk4cu72u.png)

然后把它加到你的`index.js`里像这样:

[![index with sass added](img/f9c9f765ca69eb083ffeb62eb5055c53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PCoFfA4x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hmvx7vz61hg0444r3c4e.png)

接下来是 webpack 插件。因此，我们需要一种方法将内置的 JavaScript 包含在我们的`index.html`中，当然，有一种方法可以自动完成这一任务。这也将把`index.html`文件放到我们的构建文件夹中(稍后将详细介绍构建)。让我们添加`HtmlWebPackPlugin`并将其包含在我们的`webpack.config.js`中，就像这样:

[![install html-webpack-plugin](img/91cca2fefe3d034524156e85eaab3e87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9_N5dpsB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/glggvlgf1rx6t735czhx.png)

然后:

[![webpack config with htmlwebpackplugin](img/36621255ae3434d53d6b0b71b5ee4322.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mApP-ybs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bifd0w2vedrwmz0q7r0o.png)

我们的下一个插件将确保每次运行构建时，我们放置构建的目录中的任何以前的文件都被清除。我们用这两步来做:

[![install clean-webpack-plugin](img/a68d663ada0367aff2a8f01eebd5a90d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xDvXdEp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9c7j2krodyp2q2qmp3c2.png)

然后:

[![webpack config with cleanwebpackplugin](img/4f09f3f8bc280c90a3a1f0b54d82e57f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RSp8iIZD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ww687rsebs63tanpip9.png)

我们期待已久的时刻！让我们来设置开发服务器。因此，我们将在这一步添加两个包。`webpack-cli`将用于运行我们的`package.json`文件中的命令。第一:

[![webpack dev server install](img/b5d8697c6b99b001bcbf1c1064aab6a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rOhdYnvI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pzj53agbw9z5vd7btay1.png)

然后更新我们的`webpack.config.js` :

[![add dev server](img/5a36e4d6ec837e674cf9610a941b82e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iroHijE---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fizea3t23sd9jqhudpuz.png)

最后添加到脚本部分的`package.json` :

[![start script](img/10b2a580887a9a377c3a7b06f5a432ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9vPoTDTP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y4nzik5do19kmhal9b4w.png)

现在启动您的开发服务器，您的默认浏览器将会打开。当 webpack 做它的事情的时候需要一秒钟，但是你很快就会在浏览器中看到你的反应。

[![npm start](img/1a93cce195859b10df8854a1eb1a90b9.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--BbVoDbtr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iwxkcfw54cvabqswerxx.png)

所以现在我们可以说成功了！然而，我们的代码并没有针对生产应用进行优化。然而，我们也不想在开发过程中优化我们的代码，因为这会花费更多的时间来构建。因此，让我们为我们的生产和开发环境创建单独的构建文件，并告诉 webpack 如何处理它。我保证我们快完成了。真的是我们…接下来是:

[![install webpack-merge](img/4ed9778ac28d35f86fc8354e4b7848fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gxR-Pap7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0fybexj0enyc49dugtgc.png)

这将允许我们拥有三个 webpack 配置文件。让我们开始准备吧。将您的`webpack.config.js`重命名为`webpack.common.js`。然后创建`webpack.dev.js`和`webpack.prod.js`。这个想法是，一个将在开发和生产中使用配置，一个将仅用于开发，一个将仅用于生产。首先，让我们从上一步添加的 common 中删除开发服务器代码，并将其添加到`webpack.dev.js`。我们将利用`webpack-merge`来包含来自`webpack.common.js`的所有内容，并添加`webpack.dev.js`。所以现在你的`webpack.common.js`长这样:

[![webpack common](img/96c8580be0124bff9d1197ca4a21331c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2ZYxuAB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xvcacatpcc9k51dg46hd.png)

你的`webpack.dev.js`应该是这样的:

[![webpack dev](img/9750aac6b17ee40298ba16d49b3cd233.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---KVqV3rd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x6tnvihu16wwkk0n0o1f.png)

你的`webpack.prod.js`应该是这样的:

[![webpack prod](img/f963a5fff6316268a3837925daf2fca4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NK8CF9gm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wwohb4v9jfubtn2f9mid.png)

这里的最后一步是更新我们的`package.json`文件中的`scripts`,以便在不同的时间利用这些不同的文件。你的新`scripts`章节应该是这样的:

[![final scripts](img/019b0edec01a6ab2c6b94fc39f4f6951.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AvMOjLMV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8h80fhn9qi4byb783uws.png)

## 现在怎么办？

现在，您可以开始使用:

[![npm start](img/1a93cce195859b10df8854a1eb1a90b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BbVoDbtr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iwxkcfw54cvabqswerxx.png) 启动您的开发服务器

您可以构建您的应用程序，并拥有我们所提供的所有优势。当您准备好部署您的生产应用程序时，只需运行:

[![npm run build](img/3af52c38225aefd006ce2444bf374f1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZhUfHJR_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qifhwb97oapgv1ss8g68.png)

这将把你的代码的优化版本输出到`/dist`文件夹中。把这些文件上传到你最喜欢的主机上，你就可以开始了！

## React 样板文件的下一步是什么？

好吧，我什么都没说！哈，我想你现在已经喝得够多了。希望这能让你开始创建自己的样板文件。你将提前开始你的个人项目，并真正理解每件作品的作用。我**强烈**推荐 [webpack 文档](https://webpack.js.org/concepts/)进一步阅读，如果你想继续添加配置到你的 webpack 设置。在 T4，你可以做更多的事情。你可能想做的另一件事是在你的项目中设置类似于 [ESLint](https://eslint.org/) 的东西。你也可以深入研究巴别塔和它允许你做的一切。

谢谢，我很想听听你对这个设置的看法。你可以在下面的评论中添加、删除或更改任何内容。

差点忘了！*实际上是我做的，我是在点击“发布”后添加的*。如果你想找到所有这些代码，我有一个回购[这里](https://github.com/itzsaga/react-boilerplate-blog)。

我最初是被这篇使用 webpack 3 和 Babel 6 的[样板文章启发来写我自己的最新样板文章的。](https://medium.freecodecamp.org/how-to-build-your-own-react-boilerplate-2f8cbbeb9b3f)