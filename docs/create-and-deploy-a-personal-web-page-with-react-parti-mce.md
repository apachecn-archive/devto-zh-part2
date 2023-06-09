# 用 React 创建和部署个人网页(第一部分)

> 原文：<https://dev.to/migueloop/create-and-deploy-a-personal-web-page-with-react-parti-mce>

## 先决条件

您需要在您的计算机上安装

*   NodeJS(版本> 6.10 就好)
*   Npm(版本> 5.12 即可)

您还需要:

*   GitHub 账户
*   命令行 Git 客户端

## 项目创建

检查一切安装正确后。我们可以使用下一个命令
`npx create-react-app my-app`
开始我们的项目，然后我们将安装 gh-pages 包作为 app
`cd my-app`
`npm install gh-pages --save-dev`
**属性**
的“开发依赖项”，我们将向 package.json 添加一些属性

```
//...
"homepage": "http://gitname.github.io/my-app" 
```

Enter fullscreen mode Exit fullscreen mode

**【gitname】**→你的 github 用户名
**【我的应用】** →你的项目路径，在这个例子中是我的应用
同样，让我们为 gh-pages 部署添加一些自定义脚本，在我们的 package.json
的“脚本”:{ … }部分

```
...
"predeploy": "npm run build",
"deploy": "gh-pages -d build" 
```

Enter fullscreen mode Exit fullscreen mode

**链接到仓库**
在我们的项目路径下让我们创建一个 git repo:
`git init`
这将创建一个空的。git 文件。现在我们必须向我们的 repo 添加一个远程存储库 URL:
`git remote add origin https://github.com/gitname/my-app.git`

## 部署

现在让我们生成您的应用程序的生产版本，并将其部署到 GitHub 页面。
`npm run deploy`

[![Console](img/e96a729018768453f35dde71d027707b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8G8X7ZfT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e01jiocljuwdewuplso2.png)

现在，您应该可以通过指定的 URL 访问您的应用程序。

这里发生了什么？

当我们执行 run 命令时，库将所有构建的文件推送到 gh-pages 分支，github 自动获取这个分支并创建域和发布我们的代码。

*   *(可选)将源文件推送到主服务器*我建议将源文件推送到主分支，以保持源文件可以随时随地进行更改。

```
git add .
git commit -m "First source code commit"
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

后续步骤:如何添加内容

这是我的项目的第一个结果。

[![](img/236ced6abb02cfc1ca224a735918e674.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w_dSc7QZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s9p82u891vfbnhujccl0.png)

如果你现在检查这个页面，它是完全不同的。我是如何创造了你所能看到的一切？查看下一篇文章了解更多。