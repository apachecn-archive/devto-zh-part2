# 使用 Vue.js 探索包裹，webpack 替代方案

> 原文：<https://dev.to/ycmjason/exploring-parcel-with-vuejs-the-webpack-alternative-21hj>

# 用 Vue.js 探索包裹，webpack 的替代品

> 这篇文章非常适合初学者，因为我将演示如何从头开始建立一个网站。

就在 2018 年开始之前，webdev 社区似乎开始意识到前端工具的配置地狱的疯狂。Webpack，babel，sass，postCSS，postHTML，开发服务器，产品服务器...用于配置开发工具的行数几乎赶上了应用程序的实际行数！

这就是术语“零配置”开始流行的地方。我在 11 月下旬发现了 Poi，并打赌这将是 2018 年 Q1 前端开发的最大事件。然后[包裹](https://parceljs.org/)在 12 月出现，向我们展示了一个更加通用的捆扎机。然后 [@vue/cli 3.0](https://github.com/vuejs/vue-cli) 在[vue . js Amsterdam Conference 2018](https://www.youtube.com/watch?v=TRJMT9yjONQ)中呈现，介绍了另一种创建 Vue 项目的零配置方式。

今天，我们将研究 package，看看在我们的项目中采用它有多容易。如果你以前使用过 webpack，你会惊讶于它的简单程度。

# 创建项目目录，启动项目

> 本节截图:[https://github . com/ycmjason/parcel-article/tree/28fc 86 bb 20634504 FD 6 e F6 c 8 C5 d 2596 a F3 a 370](https://github.com/ycmjason/parcel-article/tree/28fc86bb20634504fd6ef6c8c5d2596a6af3a370)

```
mkdir parcel-article && cd $_ 
```

Enter fullscreen mode Exit fullscreen mode

上面是一个非常常见的命令，用于创建一个新目录并直接进入它。

```
git init
gitignore init node
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

*   `git init`创建`.git/`。
*   `gitignore init node`创建我的预定义节点[giignorer](https://dev.to/ycmjason/how-to-create-gitignore-file-with-predefined-templates-dfg)配置文件。
*   `npm init -y`创建`package.json`，我们可以记录项目所需的依赖关系。

```
git add -A
git commit -m 'initial commit' 
```

Enter fullscreen mode Exit fullscreen mode

*   告诉 git 跟踪当前目录中的所有文件
*   `git commit -m 'initial commit'`用消息“初始提交”提交文件更改

# “你好世界”网站

> 本节截图:[https://github . com/ycmjason/parcel-article/tree/2cf 411 c 403335032859 d7b 496153869 f 597 d72e 0](https://github.com/ycmjason/parcel-article/tree/2cf411c403335032859d7b496153869f597d72e0)

因此，让我们创建一个简单的静态网站，显示“hello world”并将“Hello world”记录到控制台。

```
<!-- index.html -->
<html>
    <body>
        Hello world!
        <script src="src/main.js"></script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
// src/main.js
console.log('hello world'); 
```

Enter fullscreen mode Exit fullscreen mode

让我们安装为我们提供开发服务器的包。

```
npm i --save-dev parcel-bundler 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令会将`parcel-bundler`安装到`./node_modules/`中，并将其添加到`package.json`中的`devDependencies`中。这使得我们的应用程序可以被构建，即使`parcel-bundler`没有被全球安装。

```
// package.json
{
  "name": "parcel-article",
  "version": "0.0.1",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "parcel index.html", // !!! this is added !!!
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "Jason Yu <me@ycmjason.com>",
  "license": "ISC",
  "devDependencies": {
    "parcel-bundler": "^1.7.1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我添加了`dev` npm 脚本来启动 parcel 提供的 dev 服务器。我们可以通过执行以下操作来运行这个 npm 脚本:

```
$ npm run dev

> parcel-article@0.0.1 dev /tmp/parcel-article
> parcel index.html

Server running at http://localhost:1234 
✨  Built in 521ms. 
```

Enter fullscreen mode Exit fullscreen mode

打开你最喜欢的浏览器，进入 [http://localhost:1234](http://localhost:1234) ，我们应该会看到“Hello world！”已显示。查看控制台时，我们还应该看到`hello world`。

该服务器还提供热模块更换。为了演示这一点，保持服务器运行，并将`src/main.js`更改为:

```
// src/main.js
console.log('bye world'); 
```

Enter fullscreen mode Exit fullscreen mode

回到你的浏览器，你会在你的控制台上看到另一个日志，上面写着`bye world`。

现在我们可以提交我们的更改了。

```
$ git status
On branch master                                                 
Your branch is up to date with 'origin/master'.                            

Changes not staged for commit:                             
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   package.json                                

Untracked files:                
  (use "git add <file>..." to include in what will be committed)

        .cache/                                                 
        dist/
        index.html                                               
        package-lock.json                                  
        src/     

no changes added to commit (use "git add" and/or "git commit -a") 
```

Enter fullscreen mode Exit fullscreen mode

注意，git 说我们有未被跟踪的`.cache/`和`dist/`？`.cache/`是包裹所使用的，它为什么跑得这么快背后的秘密。`dist/`是打包我们的应用程序的地方。我们不必将它们提交给 git。所以我把他们加到了`.gitignore`里忽略他们。

# 接下来是什么？

在接下来的文章中，我们将看看如何轻松地将 Vue、babel 和 sass 添加到我们的项目中。