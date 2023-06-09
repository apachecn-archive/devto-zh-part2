# 使用 Heroku 和 Git 的多种环境

> 原文：<https://dev.to/uppernauts/multiple-environments-with-heroku-and-git-c9l>

在过去的几个月里，我一直使用 Heroku 作为开发环境和我的团队一起工作。它进行得很好，直到上周我们的客户要求现场演示😱。
我们不能在开发环境上做演示，主要是因为我们不能要求我们的团队在演示期间停止工作(我们都知道开发环境通常充满了猫图片、“测试”和“lorem ipsums”)，所以我们决定创建一个“演示”环境。
我直接去找 Heroku docs 了解如何处理多种环境。在这篇文章中，我将尝试涵盖我在这个问题上所学到的一切。

## Git remote

我们首先需要了解的是`git remote`。
摘自 GitHub 文档:

> 远程 URL 是 Git 对“存储代码的地方”的别出心裁的说法。

您可以运行这个命令来检查您已经在项目中配置了哪些远程:

```
git remote -v 
```

Enter fullscreen mode Exit fullscreen mode

在您的典型项目中，输出应该是这样的:

```
> origin https://your-git-repo/your-git-project (fetch)
> origin https://your-git-repo/your-git-project (push) 
```

Enter fullscreen mode Exit fullscreen mode

👆在这种情况下,`your-git-project`只配置了一个遥控器，它被称为“原点”。

*(你也可以在`your-git-project/.git/config`下查看你项目的遥控器)*

如果你想深入了解，这里有一个关于管理遥控器的 GitHub 文档

## 多个遥控器

因此，正如您可能已经猜到的，您的 git 项目可以处理多个远程，这意味着您可以将您的代码推送到任何现有的远程。

若要将新遥控器添加到项目:

```
git remote add remote-name https://your-git-repo/your-git-project.git 
```

Enter fullscreen mode Exit fullscreen mode

并把你的代码推到一个特定的远程和分支:

```
git push remote-name branch-name 
```

Enter fullscreen mode Exit fullscreen mode

# 那么 Heroku 呢？

当我们使用 Heroku CLI 创建 Heroku 应用程序时，它会自动将遥控器添加到我们的新应用程序中。事实上，如果您检查日志，您会看到类似这样的内容:

```
> Git remote heroku added 
```

Enter fullscreen mode Exit fullscreen mode

摘自 Heroku 的文档:

> 当您在 Heroku 上创建应用程序时，它会将一个新的 Git remote(通常名为 Heroku)与您的应用程序的本地 Git 存储库相关联。

所以 Heroku CLI 负责将一个名为“Heroku”的遥控器添加到您的 git 配置中。我的建议是用一个能更准确反映遥控器用途的名字来代替这个名字，尤其是如果你打算用 Heroku 来处理你的应用程序的多个环境。

## 更新 Heroku 的远程名称

要为 Heroku 遥控器设置一个更好的名称，请运行以下命令:

```
git remote rename heroku heroku-dev 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 Heroku app 时指定远程名称

您也可以在创建应用程序时定义一个更好的远程名称，就像这样:

```
heroku create myAppName --remote heroku-dev 
```

Enter fullscreen mode Exit fullscreen mode

## 配置变量

还有一件事。您的应用程序需要知道它被部署到哪个 env，这就是配置变量出现的时候。可以从我们的代码中访问配置变量，并使用它们来判断它在哪个 env 中，例如，访问特定环境的数据库(您不希望您的开发环境在您的演示数据库中插入内容)。

我们可以很容易地从 Heroku CLI 添加一个新的配置变量，就像这样:

```
heroku config:add ENV="dev" --remote heroku-dev 
```

Enter fullscreen mode Exit fullscreen mode

例如:这里有一个简单的例子，说明我如何用 Node.js、MongoDB 和 config vars 为每个环境管理不同的连接字符串:

```
// config.js
const config = {
    dev: {
        connectionString: "mongodb://my-DEV-db-connection-string"
    },
    demo: {
        connectionString: "mongodb://my-DEMO-db-connection-string"
    }
}

// app.js
const config = require('./config').get(process.env.NODE_ENV);
const mongoose = require('mongoose');
mongoose.connect(config.connectionString, { useMongoClient: true }); 
```

Enter fullscreen mode Exit fullscreen mode

查看 Heroku 文档，了解关于配置变量的更多信息

# 总结

### 如何为一个新 app 创建多个环境:

1-为您需要的每个环境创建一个 Heroku 应用程序，并为每个环境指定一个远程名称。

```
 heroku create myAppName --remote heroku-dev
    heroku create myAppName --remote heroku-demo 
```

Enter fullscreen mode Exit fullscreen mode

2-为每个环境设置一个配置变量，让你的程序知道在哪里运行

```
 heroku config:add ENV="dev" --remote heroku-dev
    heroku config:add ENV="demo" --remote heroku-demo 
```

Enter fullscreen mode Exit fullscreen mode

### 如果您已经在 Heroku 中运行了您的开发环境，并且想要添加新的 env:

1-重命名您当前的 Heroku 遥控器(可选):

```
 git remote rename heroku heroku-dev 
```

Enter fullscreen mode Exit fullscreen mode

2-为新环境创建一个 Heroku 应用程序，指定一个新的远程名称:

```
 heroku create myAppName --remote heroku-demo 
```

Enter fullscreen mode Exit fullscreen mode

3-为每个环境设置一个配置变量，让你的程序知道在哪里运行:

```
 heroku config:add ENV="dev" --remote heroku-dev
    heroku config:add ENV="demo" --remote heroku-demo 
```

Enter fullscreen mode Exit fullscreen mode

## 现在推送！

现在我们的遥控器都已经设置好了，我们可以将我们的更改推送到演示环境中，就像这样:

```
 git push heroku-demo master 
```

Enter fullscreen mode Exit fullscreen mode

Heroku 现在将检索我们的项目需要的任何依赖项，并为我们构建它，这样我们就可以几乎立即将我们的应用程序上线。

# 就是这样！

希望这对某人有所帮助，感谢阅读！