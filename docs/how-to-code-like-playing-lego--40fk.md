# 如何像玩乐高一样编码

> 原文：<https://dev.to/gmartigny/how-to-code-like-playing-lego--40fk>

模块化是一个大趋势，我不是第一个跳上这列火车的人。今天，我将向您展示使用普通的 Javascript 和一些非常棒的工具来构建多模块应用程序是多么容易。

# 菜谱

## 配料

首先，我假设你事先知道一些事情:

1.  面向对象编程
2.  如何写 JS
3.  NPM 基础知识

## 步骤

### 地面

让我们从你的项目的一个空目录开始(我们将其命名为 unicorn)并初始化它

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

并用一个老式的 JS 类
创建主文件`index.js`

```
function Unicorn(name) {
    this.name = name;
}
Unicorn.prototype = {
    shine: function() {
        // All kind of good stuff here 🦄
    }
}

var dazzle = new Unicorn("Dazzle");
dazzle.shine(); 
```

Enter fullscreen mode Exit fullscreen mode

### 脱钩

现在想象一下，你想在另一个项目中使用`Unicorn`类，或者只是对人类开源它。您可以用另一个 repo 创建另一个目录，但是让我们更聪明一些。`Unicorn`类与 Unicorn 项目联系如此紧密，为了清楚起见，我们将使用 NPM 范围的包名。

所有这些将`index.js`减少到 3 行代码。

```
import Unicorn from "@unicorn/model";

var dazzle = new Unicorn("Dazzle");
dazzle.shine(); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们为我们的模块创建一个子目录。

```
mkdir packages/model
cd packages/model
npm init # and name it @unicorn/model 
```

Enter fullscreen mode Exit fullscreen mode

其中也有一个包含类的`index.js`。既然我们留下了带有导入/导出语句的普通浏览器 JS，为什么不使用漂亮的 ES6 类语法呢？

```
export default class Unicorn {
    constructor(name) {
        this.name = name;
    }

    shine () {
        // All kind of good stuff here 🦄
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，`import`语句指向应该安装在`node_modules`子目录下的包名。我们可以使用类似`import Unicorn from "./packages/model/index.js";`的相对路径。更好的方法是在包之间创建一个链接。

幸运的是，npm 可以用 [link 命令](////docs.npmjs.com/cli/link)帮你做到这一点。这是我们案例中的情况。

```
cd packages/model
npm link
cd ..
npm link @unicorn/model 
```

Enter fullscreen mode Exit fullscreen mode

完美！
[![Perfect](../Images/eac93aaa843a54cc169f44bdbed4c077.png)T3】](//thepracticaldev.s3.amazonaws.com/i/alhu7wrnie4rlrzbevgy.gif)

### 包装

> 好的，很好，但是现在我不能在我的浏览器中使用它，你这个笨蛋！

首先，你怎么称呼我？是的，我知道，现在都是实验性的语法和东西，但是有工具可以帮你处理。我喜欢用 webpack 搭配 babel，当然这不是唯一的解决方案。

在项目的根目录下添加一些包。

```
npm install --save-dev babel-loader babel-core babel-preset-env webpack 
```

Enter fullscreen mode Exit fullscreen mode

整个 webpack 配置可以写满另一篇文章，所以我只展示一个有效例子。设置一个名为`webpack.config.js`的新文件，里面有一些指令。

```
module.exports = {
    entry: "./index.js", // Main file to read
    module: {
        rules: [{
            test: /\.js$/, // For all file ending with ".js"
            use: {
                loader: "babel-loader", // Use babel
                options: {
                    presets: ["babel-preset-env"],
                },
            },
        }],
    },
    output: {
        filename: "dist/unicorn.js", // Output the result in another file
        library: "Unicorn", // Under "Unicorn" namespace
        libraryTarget: "this",
        libraryExport: "default",
    },
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后，如果你运行`npx webpack`,它会把你所有的源代码编译成一个普通网络浏览器可以使用的文件。

### 管理

现在，您可以创建许多子模块，并将它们打包在一个文件中。你甚至可以有子子模块等等。把它们都放到`modules`目录里就行了。
随着项目的增长，管理所有这些动物园会变得越来越困难。

这就是 lerna 发挥作用的地方。

```
npm install -save-dev lerna 
```

Enter fullscreen mode Exit fullscreen mode

把它想象成类固醇。查看项目页面上的完整文档，但这里有一些有用的命令。

```
npx lerna clean # Remove all node_modules directories
npx lerna bootstrap # Install remote dependencies and link local ones
npx lerna add package # Install a package to all sub-modules
npx lerna add package --scope=sub-module # Install a package to a specific sub-module
npx lerna publish # Bump, tag and publish all your modules over NPM 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 享受

现在，您应该可以编写最优雅的项目了。我指望你了；)

如果你想要更深入的例子，我目前正在使用完全相同的技术构建~~另一个~~ [JS 绘图库](https://github.com/GMartigny/pencil.js)。

下一次，我们将讨论自动化测试，以及如何捕捉大量的错误并确保时间的一致性。