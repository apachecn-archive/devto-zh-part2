# 我的 Node.js 设置(摩卡&柴，Babel7，ES6)

> 原文：<https://dev.to/bnorbertjs/my-nodejs-setup-mocha--chai-babel7-es6-43ei>

<figure>[![](img/59b07a2a025948743587a09287f4605f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hG3G0PCc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Al9HLxXVl-oD2Q7SCzXSp-A.jpeg) 

<figcaption>我和 Gojira 写作单元测试</figcaption>

</figure>

每次我想写代码的时候，我都会试着为我的项目建立一个像样的开发环境。我一般安装 Babel 将 ES6/ES7 编译回 ES5，Mocha & Chai 用于单元测试，Nodemon 用于自动重启 app。

所以我决定分享我从零开始设置节点环境的经验。我希望你会发现这条信息有用。

首先，让我们为项目生成一个空的 package.json。

```
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以添加一些开发依赖项。

```
npm install --save-dev @babel/cli @babel/core @babel/node @babel/register @babel/preset-env chai mocha nodemon 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，我们的 package.json 应该如下所示:

<figure>[![](img/5351a0e1b37fbf8a430fadb92a057154.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O8wlGRiY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/540/1%2ApGPDkVuvhICkrwcfR8HugA.png) 

<figcaption></figcaption>

</figure>

Babel 7 包现在被“限定了范围”，所以旧的 babel-cli 变成了 [@babel](https://dev.to/babel) /cli。

### ES6 &节点

用一个返回字符串的简单函数创建一个 index.js 文件，这样我们就可以测试一切是否正确。我总是把索引文件放在 src 文件夹的根目录下。如果您将它放在其他地方，记得相应地调整 package.json 脚本中的路径。

。/src/index.js

```
 const sayHello = _ => "Hello guys!"

console.log(sayHello()) 
```

Enter fullscreen mode Exit fullscreen mode

要查看结果，请将以下脚本复制并粘贴到您的 package.json.

```
"start": "nodemon ./src/index.js", 
```

Enter fullscreen mode Exit fullscreen mode

在控制台/终端中键入 npm start 后，您将看到类似这样的内容:

<figure>[![](img/84a7ddb14040fca591893f75cbd48e0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yt-ny_jK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/415/1%2ACEKbkNN5tFgbcBi7Av9cMg.png) 

<figcaption>nodemon 运行</figcaption>

</figure>

Nodemon 监视代码中的每一处更改，如果您更改了什么，它会自动重新启动应用程序。将 ES6 导出语句放在 index.js 文件的末尾，然后再次运行应用程序。

```
export default sayHello 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

[![](img/e1fd4e900033c9676dbdb52c2883f017.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PcPOJE_P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/490/1%2AkiUdir5yWxMpi0mrjtHv1g.png)

节点无法识别 ES6 导出/导入关键字。为了解决这个问题，我们需要 babel 将我们的导出默认 sayHello 编译成类似 exports.default = sayHello 的内容。

为此，我们需要在项目根目录下创建一个名为. babelrc 的文件。

```
{

"presets": ["@babel/preset-env"]

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们还需要调整我们的启动脚本。

```
"start": "nodemon --exec babel-node ./src/index.js" 
```

Enter fullscreen mode Exit fullscreen mode

### 测试

好吧，让我们写一个快速测试，看看它是否有效。请记住，我们已经安装了 chai 和 mocha，所以我们可以使用它们，无需任何进一步的配置。

。/test/index.spec.js

```
 import { expect } from "chai"
import sayHello from "../src/index"

describe("index test", () => {
    describe("sayHello function", () => {
        it("should say Hello guys!", () => {

            const str = sayHello();
            expect(str).to.equal("Hello guys!")
        })
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们的包中需要一个测试脚本

```
"test": "./node_modules/.bin/mocha --compilers js:@babel/register" 
```

Enter fullscreen mode Exit fullscreen mode

关于测试脚本的三个重要事实:

*   如果你用 npm install -g 全局安装 mocha，你可以使用“mocha-compilers js:[@ babel](https://dev.to/babel)/register”来代替。
*   由于我们的测试文件位于 test 文件夹中，mocha 会自动找到我们的 index.spec.js。
*   -compilers js:[@ babel](https://dev.to/babel)/register 告诉 mocha，我们使用 ES6，所以它应该会处理它。这就是为什么我们安装了[@巴别](https://dev.to/babel)/注册。

在您的控制台/终端中键入 npm test，您将看到测试通过。

### 编译到 Dist 文件夹

如果你想知道编译后的 ES5 兼容代码是什么样的，你可以添加以下脚本到你的 package.json 中

```
"build": "babel src --out-dir ./dist --source-maps",

"serve": "node ./dist/index.js", 
```

Enter fullscreen mode Exit fullscreen mode

npm run build 命令将在 dist 文件夹中创建一个已编译的 index.js 文件，npm run serve 将运行该文件，而不是 src 文件夹中的原始文件。我们还使用- source-maps，这样当我们调试我们的。/dist/index.js 我们可以看到我们编写的实际 ES6 代码。

### 调试

我是一名 JS 开发人员，所以我宁愿使用浏览器而不是 IDE 来调试我的代码。

幸运的是，node 允许我们在浏览器中调试应用程序。这是你的 package.json 的另一个脚本

```
"debug": "node --inspect-brk ./dist/index.js" 
```

Enter fullscreen mode Exit fullscreen mode

在 npm 运行调试后，您可以看到以下消息。

<figure>[![](img/acf5f4391e50fb9c22c753edc7ead086.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DexLx0X1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/528/1%2AUkiT3sh0wdFJrlwuOB3xXA.jpeg) 

<figcaption>节点调试</figcaption>

</figure>

获取为您生成的突出显示的字符串，并将其附加到这个 URL:chrome-dev tools://dev tools/bundled/inspector . html？experiments = true & V8 only = true & ws = 127 . 0 . 0 . 1:9229/。

然后，粘贴到浏览器，就大功告成了。

<figure>[![](img/53f80ab40958e71cce72a907a98a19bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q-627D19--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/865/1%2AZ_kvpfcVgKxf9KBepe5RJg.png) 

<figcaption>快乐调试！</figcaption>

</figure>

### 结束

所以，就这样了。下一次，我还将深入探讨有趣的 JS/Python/Rust 编程概念。

如果你遇到困难或者知道更好的方法，不要犹豫，留下你的评论。

感谢阅读。请随意从[这里](https://github.com/bnorbertJS/node_starter.git)克隆存储库。