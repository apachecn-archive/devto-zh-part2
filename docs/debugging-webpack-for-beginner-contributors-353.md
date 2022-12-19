# 为初学者调试 Webpack

> 原文：<https://dev.to/gabethere/debugging-webpack-for-beginner-contributors-353>

我最近开始思考如何为开源做出更多贡献。我认为这是我们作为开发人员在职业生涯中遇到的一两次想法。我的意思是，我们使用了如此多的开源代码，回馈社区的想法本身似乎是一个简单的任务，对吗？

我环顾四周，看看我想为什么类型的项目做贡献，我决定首先看看我在日常工作中最常用的项目，或者作为我的副业项目的一部分。

Webpack 对我来说似乎就是那个项目:)

## 令人生畏的代码库

想到 Webpack 是我的第一个开源项目，我感到非常害怕。Webpack 本身是巨大的，并且有一个相当健康和非常固执己见的社区围绕着它(当然这没有错)。然而，作为一个有抱负的开源贡献者，我认为理解它是如何工作的是一个巨大的挑战。

## 调试

这给了我第一个挑战，我到底该如何调试这个庞大的代码库？毕竟，我们大多数人认为调试代码库是理解控制流和逻辑的第一步。

### 假设

我假设您已经将一个版本的 Webpack 分支并克隆到了您的本地机器上。在我的例子中，我分叉了最新的 Webpack 3(或最新的)分支。

`git checkout remotes/origin/webpack-3`

然后，你很可能想要运行`npm i`或`yarn`来获得所有的`node_modules`好东西。

### VSCode 设置

我假设您也在使用 VSCode，所以我们需要设置我们的调试配置。

下面是您开始工作所需的最低配置:

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "attach",
      "name": "Attack-Webpack-Debug",
      "port": 9229,
      "skipFiles": ["${workspaceFolder}/node_modules/**/*.js"]
    }
  ]
} 
```

这里我们只是说，我们希望附加到端口 9229 上正在运行的调试进程，并且我们希望避免调试或遍历`node_modules`下的文件。

另一个重要/方便的步骤是启用`Auto Attach`。您可以在 VSCode 的底部设置栏上切换此设置:

[![alt text](../Images/bdcbab51e81747af56a396c554d37bda.png "Auto attach VS Code")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yTGB6eZT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b1gpk7iujs9ob5lvlueb.png)

一旦我们执行了`node --inspect-brk`命令，这将允许您自动切换调试器屏幕。

### 设置要调试的项目

好的，所以我们需要一个样例项目来为我们打包 Webpack。这很简单，在分叉的 Webpack 项目文件夹之外设置另一个文件夹。

1.  `mkdir sample-webpack`
2.  `touch client.js`
3.  `vim client.js` - >添加几行代码，像`console.log('Test')`等。

差不多就是这样！现在回到您的 Webpack repo，然后我们终于可以运行我们的调试命令了！

### 节点巡查

在您的 Webpack repo 上，我们将运行一个快速命令来捆绑`client.js`文件。

运行以下命令:

`node --inspect-brk bin/webpack.js --entry ./client.js --output-filename bundle.js`

这将触发调试器窗口，并将在第一行中断。然后，您可以在 VSCode 上设置一个断点，并逐句通过 Webpack 源代码！

[![alt text](../Images/7b7505b94d1d0cea7f57787906d08442.png "Debug VS Code")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0L0yb0fl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rw6916e9k0cq732k8rxf.png)

### 就是这样！✅

去玩吧！如果您想进行更复杂的 Webpack 配置调试，只需从示例项目中传递一个`webpack.config.js`,如下所示:

`node --inspect-brk bin/webpack.js --config ./webpack.config.js`

如果您有任何问题或者这些步骤不适合您的设置，请随时在下面留下您的评论💯