# 我使用 Typescript + Express 编写 REST 服务的经验

> 原文：<https://dev.to/sanijalal/my-experience-in-writing-a-rest-service-using-typescript--express-5aml>

你好。这实际上是我第二次写这个帖子，因为显然点击帮助按钮将打开帮助页面，而不保存您的条目。这是值得学习的一课。我应该试着看看能不能做个公关来解决这个问题。

我在这里分享我作为一个. NET WebApi 开发者尝试使用 TypeScript + Express 编写 REST 服务的经验。开始了。

所以让我们从创建一个 github 回购协议开始:[https://github.com/sanijalal/dailydiaryservice](https://github.com/sanijalal/dailydiaryservice)

然后，让我们运行 npm init 来设置我们的应用程序。

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

我还不知道如何在 typescript 上测试，所以我们暂时忽略它。我不知道什么是 ISC 许可证，所以我们现在就用它吧(希望它不包括把我的灵魂卖给魔鬼的条款)。

下一步是什么？我想使用 TypeScript，所以让我们安装它。

```
npm install typescript 
```

Enter fullscreen mode Exit fullscreen mode

我也知道我要用快递。所以让我们也安装它。

```
npm install express 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经在项目中安装了 TypeScript 和 Express。下一步我们应该做什么？

好吧，首先，我不知道快递是怎么运作的。让我谷歌一下如何使用 express。

谷歌搜索后..

好吧，我读过快递是怎么运作的。[[https://medium . com/@ LindaVivah/the-beginners-guide-understanding-node-js-express-js-fundamentals-e 15493462 be 1](https://medium.com/@LindaVivah/the-beginners-guide-understanding-node-js-express-js-fundamentals-e15493462be1)]但这是用 javascript。我想写打字稿。

嗯，我不知道打字稿。

我们去谷歌一下。

好了，看完这个[[https://code . tuts plus . com/tutorials/typescript-for-初学者-入门- cms-29329](https://code.tutsplus.com/tutorials/typescript-for-beginners-getting-started--cms-29329) ]我想我已经掌握了 TypeScript 的窍门。

简单地说，打字稿是:

1.  我需要写信。ts 文件，需要将其转换为。要运行的 js 文件。
2.  当我想把代码从 typescript 转换成 javascript 时，我可以调用 tsc 命令行。
3.  我需要在我的项目中有一个 tsconfig.json 文件来告诉 typescript 如何将我的 typescript 文件转换成 javascript。

我们来梳理一下第二步。我将在我们的项目中创建一个名为 tsc 的构建脚本。我该怎么做？让我们将它添加到 package.json
的脚本部分

```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "tsc"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

所以当我们调用`npm run build`时，我们将把类型脚本转换成 Javascript。

但是我们还没有告诉 TypeScript 从哪个文件夹获取 TypeScript 文件。我们现在做第三题。让我们创建一个 tsconfig.json 文件。我将使用本教程中的 tsconfig 值[[https://it next . io/building-restful-we b-APIs-with-node-js-express-MongoDB-and-typescript-part-1-2-195 bdaf 129 cf](https://itnext.io/building-restful-web-apis-with-node-js-express-mongodb-and-typescript-part-1-2-195bdaf129cf)]

```
//  tsconfig.json  {  "compilerOptions":  {  "module":  "commonjs",  "moduleResolution":  "node",  "pretty":  true,  "sourceMap":  true,  "target":  "es6",  "outDir":  "./js",  //  Javascript  files  will  be  created  in  this  directory.  Include  this  folder  in  .gitignore  to  not  add  it  to  git.  "baseUrl":  "./src"  },  "include":  [  "src/**/*.ts"  //  The  **  means  all  files  and  folder  in  the  src  folder.  People  call  it  recursive.  The  *.ts  means  all  files  that  end  with  .ts  will  be  picked  up.  ],  "exclude":  [  "node_modules"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们调用 tsc 时，typescript 会将 src 文件夹中的所有 TypeScript 文件转换为 js 文件夹中的 javascript 文件。

让我们也向我们的 package.json 添加一个启动脚本。我将使用我的 js/app.js 作为我的主项目文件。

```
"scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1",  "build":  "tsc",  "start":  "node js/app.js"  }, 
```

Enter fullscreen mode Exit fullscreen mode

显然，我还需要安装@types/express 来告诉 register 在 TypeScript 中定义 express(我想)。就这么办吧

```
npm install @types/express 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们用 src/app.ts 写我们的 Express 代码吧！

```
import express = require("express"); // We need to import the express package.

// 1) This is how set up our express app. Initialise an express instance.
const app = express();
app.set("port", 1337); // 2) We need to setup which port to listen to. I use 1337.

// 3) This registers the get endpoint with the / uri. req is the request we receive. res is what we are sending.
app.get('/', (req, res) => {
    res.send("Hi")   // 4) Here we are sending the Hi message when we hit this endpoint.
})

// export our app
export default app; // 5) To let other files use this class, we need to export it. 
```

Enter fullscreen mode Exit fullscreen mode

好吧，如果我这样写。我们还需要一个 server.ts 文件。让我们再次将 package.json 更改为调用 server.js，而不是启动脚本中的 app.js。

```
 //  package.json  scripts  "start":  "node js/server.js" 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在开始写我们的 server.ts 文件吧！查看[[https://it next . io/building-restful-we b-APIs-with-node-js-express-MongoDB-and-typescript-part-1-2-195 bdaf 129 cf](https://itnext.io/building-restful-web-apis-with-node-js-express-mongodb-and-typescript-part-1-2-195bdaf129cf)]中的示例，这是我们将要编写的:

```
// server.ts
import app from './app' // 1) We need to import the app class we created previously.

// 2) This creates the server which is listening to the port number we defined in the app class.
const server = app.listen(app.get("port"), () => {
    console.log(
        "App is running on http://localhost:%d in %s mode",
        app.get("port"),
        app.get("env")
    ) // 3) We are going to put this in console so you can see that it is actually working.
});

export default server; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经完成了这些，让我们来构建我们的应用程序吧！在终端中运行此命令。

```
npm run-script build 
```

Enter fullscreen mode Exit fullscreen mode

您将看到 js 文件夹中现在生成了 javascript 文件。喜欢吗？让我们现在启动这个应用程序。

```
npm run start 
```

Enter fullscreen mode Exit fullscreen mode

您将在控制台中看到这一点:应用程序正以开发模式在 [http://localhost:1337](http://localhost:1337) 上运行

耶！我们的应用程序正在运行！在浏览器中打开网址，你会看到嗨！这是我们之前在 app.ts
中写的

```
// app.ts
// We wrote this earlier!
// ...
app.get('/', (req, res) => {
    res.send("Hi")   // 4) Here we are sending the Hi message when we hit this endpoint.
})
// ... 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们有了运行的应用程序，我们如何停止它？我并不羞于承认，当我刚开始学习终端命令时，我不知道如何阻止这一点。但其实很简单。按 ctrl + c。这将终止进程，您的服务器现在将停止。

耶！我们有一个什么都不做的服务。至少现在还没有。在下一篇文章中，我们将使用快速路由器实现端点路由。

感谢您的阅读！