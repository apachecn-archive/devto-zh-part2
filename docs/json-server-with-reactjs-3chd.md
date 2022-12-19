# 带有 ReactJS 的 jSON 服务器

> 原文：<https://dev.to/mariorodeghiero/json-server-with-reactjs-3chd>

# 带有 ReactJS 的 jSON 服务器

JSON Server 帮助您简单快速地建立 REST API。正如他们在 Github 资源库中报告的那样，你可以在不到 30 秒的时间内创建。在本文中，我们将看到如何安装和提供一个 jSON 文件。

## 启动项目

1 -我们使用 created-react-app
 `$ npx create-react-app my-app
$ cd my-app` 创建了项目

2 -我们安装了 jSON-Server
 `$ npm install -g json-server` 

## 创建 JSON 文件

在 my-app 文件夹中，我们将使用以下数据创建一个名为“db.json”的文件:

```
{
  "posts": [
    {
      "title": "CopyRight Automatico",
      "tags": "[JS]"
    },
    {
      "title": "HtmlWebpackPlugin Multiples HTML files",
      "tags": "[JS, webpack]"
    }
  ]
} 
```

然后我们通过运行下面的命令来运行“json-server”和“react-app”:
 `$ json-server --watch db.json --port 3001` 
 `$ npm run start` 

## 配置 App.js 文件

现在我们已经有了运行在“localhost:3001/posts”下的“json-server ”,我们可以进行咨询了。因此，在 App.js 文件中插入以下代码，当在 react 中安装组件时，我们使用“fetch”执行查询，并在序列中使用“map”返回每个项目。

[![code](../Images/0775bf5d731272ed15dcc193b17f5b2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GFyw6fn2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mrazcswbi53aygu4fnu3.png)

## 结果

现在，如果我们查看“React App”页面，我们将得到以下结果。

[![Result](../Images/7bd6752670a7042b2be946855c3695d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QDS4x1BA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c17cd1bp4i2njwjtkyib.png)

## 结论

通过遵循本文中的步骤，您可以使用 ReactJS 和 jSON SERVER 为您的应用程序创建一个优秀的开发环境。
代码中使用的主题，我受“Vue.js”的启发创建的，你可以在“VS_Code”中安装，搜索[“Vue Theme”](https://www.mariorodeghiero.com/vue-theme)。

谢谢！！！