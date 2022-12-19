# 在 Netlify 上托管 Blazor

> 原文：<https://dev.to/mattferderer/hosting-blazor-on-netlify-p8>

由于 Blazor 是一个前端框架，我们可以在任何无服务器或静态 web 主机上托管我们的 Blazor 应用程序。唯一的要求是，我们需要添加较小的配置重定向网址，使所有的网址指向我们的 index.html 页面。 [Netlify](https://www.netlify.com/) 非常适合这个。 [Netlify](https://www.netlify.com/) 也恰好是我最喜欢的静态网站主机。

## 先决条件

*   [。NET Core 2.1 或更高版本](https://go.microsoft.com/fwlink/?linkid=873092)

*   应该安装 Blazor 模板。`dotnet new -i Microsoft.AspNetCore.Blazor.Templates`

## 创建演示 Blazor 应用程序

使用命令:`dotnet new blazor -o staticDemo`创建一个新的 Blazor 应用程序

通过运行`dotnet run`确保您的应用程序正常工作

## Netlify 具体步骤

在您的`wwwroot`目录中添加一个名为`_redirects`的文件。

将以下行添加到您的`_redirects`文件中:

```
/*    /index.html   200 
```

这会将任何 URL 重定向到 index.html 文件。因为 Blazor 是一个 SPA 工具，如果有人输入一个 URL，比如 oursite.com/counter，我们不希望服务器寻找计数器文件。相反，服务器应该将请求发送到 index.html，由它负责路由。

如果您在另一个静态主机或无服务器环境上发布，请查看他们的文档，了解如何编写类似的重定向。

## 发布到 Netlify

一旦我们完成了这些，部署到 [Netlify](https://www.netlify.com/) 的最简单的方法就是将它添加到 GitHub repo 中。

Netlify 不能为我们建立这个项目，因为他们不能建立 dotnet。我们需要处理构建和添加文件到我们的仓库。

默认情况下。用我们的项目创建的 gitignore 文件忽略了默认的构建目录。您可以修改您的。gitignore 文件以不忽略默认的发布目录。然后就可以运行`dotnet publish -c release`。

另一个选择是使用`-o`参数发布到不同的目录。这里有一个例子:`dotnet publish -c release -o ./dist`

一旦你完成了上面的任何一项，就把你的修改推送到 GitHub 库。

之后你可以去 Netlify &从你的 GitHub 库创建一个新的站点。将您的站点链接到您的 repo 后，系统会提示您进行部署设置。您可以忽略 build 命令。对于发布目录，添加发布到的目录。它应该是这样的:

```
_framework
css
sample-data
_redirects
index.html 
```

每次您将更改推送到存储库时，Netlify 都会重新部署它们。

如果你遇到问题或者你在其他地方部署了这个，请在下面的评论中告诉我。

*最初发布于[mattferderer.com](https://mattferderer.com/host-blazor-on-netlify)。*