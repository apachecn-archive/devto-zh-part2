# ESLint + Visual Studio 代码:编辑器集成致胜

> 原文：<https://dev.to/psyked/eslint-visual-studio-code-editor-integration-for-the-win-jb8>

编码是复杂的——需要记住大量的最佳实践、遵循大量的指导方针和避免“已知问题”——所以在工作时有一只“援助之手”会有所帮助。

<figure>[![](../Images/262acc5889ca83f0c5c47ea94874d6d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---GoJn40g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/398/1%2ATPkhIqPgVzFSSpwdlVwhVw.png)

<figcaption>ESLint—[https://eslint.org/](https://eslint.org/)</figcaption>

</figure>

[可插入 ESLint 的 JavaScript linter](https://eslint.org/)

ESLint 是一个可扩展的 linter——或者代码检查工具——它可以被设置为一个独立的工具或者集成到 Visual Studio 代码编辑器中。

> linter 或 lint 指的是分析源代码以标记编程错误、bug、风格错误和可疑结构的工具。[【维基百科】](https://en.wikipedia.org/wiki/Lint_(software))

通过在编辑器中正确配置 ESLint，您的开发体验几乎可以——就好像您在不断地与(一个非常礼貌和博学的)万事通进行结对编程。尽管这可能令人望而生畏，但它将帮助您产生更高质量的代码，并在您工作时教会您。

### 用 ESLint 配置 Visual Studio 代码

首先，序言:我将首先使用 create-react-app 建立一个 React 项目，它在项目的根目录下给我一个 package.json，并通过 NPM 管理依赖项。

要设置新项目，请运行以下命令:

[Facebook/create-react-app](https://github.com/facebook/create-react-app)

首先，安装[创建-反应-应用](https://github.com/facebook/create-react-app) :

```
npm i -g create-react-app 
```

然后，创建一个新项目:

```
create-react-app eslint-integration-example 
```

当我们想的时候，我们可以这样运行这个项目:

```
cd eslint-integration-example
npm start 
```

#### 设置 ESLint

ESLint 可以在全球范围内安装，但是为了更好的可移植性和弹性，让我们用下面的命令将它以及一些插件和设置添加到项目中:

```
npm install --save-dev \
eslint \
babel-eslint \
eslint-config-airbnb \
eslint-config-babel \
eslint-config-prettier \
eslint-plugin-import \
eslint-plugin-react 
```

安装了所有这些插件后，我的 package.json 文件如下所示:

<figure>[![](../Images/4c41a65a3ca8addfb90ef96d3d312733.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P2lnbJZ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxsrntccR6nCD51Gr9zKwvg.png) 

<figcaption>带有 ESLint 配置和插件的 package.json 文件</figcaption>

</figure>

严格地说，添加 eslint 的必要插件实际上只是 ESLint，但我在这个例子中添加了其他插件，因为它们是我当前在活动项目中使用的插件。

除了这些 npm 包，我们还将向项目的根目录添加一个配置文件，名为。eslintrc，看起来像这样:

<figure>[![](../Images/a411409740c89287db4ff4af63d5f1b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zO1fw0gD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAgug9EAS4QMlUuLOMNyv8Q.png) 

<figcaption>我的。eslintrc 文件</figcaption>

</figure>

这告诉 ESLint 导入 **airbnb** 、**beautiful**和 **jsx-a11y** 配置(我们在之前的命令中安装了这些配置)作为起点，并为我们将来扩展配置提供了一个跳板。

要对我们的代码运行 eslint，我们可以使用下面的命令:

```
npx eslint src/App.js 
```

这在终端中给出了以下输出:

<figure>[![](../Images/12b331ac3b4f2b2077c8e91fa4db68b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4gA47_Q3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_P4YPpbVg4L-Utu7egF-vg.png) 

<figcaption>从端子</figcaption>

</figure>

测试输出

为了更容易运行，我们还可以将该命令添加到 package.json 文件的脚本部分，如下所示:

<figure>[![](../Images/b6470d5f38983de6f47ef8ec1a6b0f2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5iMhQfZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/770/1%2AWIn_eNNTYewBEd9AAuO8-g.png)

<figcaption>package . JSON 文件的脚本段</figcaption>

</figure>

能够运行 ESLint 并像这样突出我们代码的问题已经很不错了，但真正让它更上一层楼的是编辑器集成。

#### 与 Visual Studio 代码集成

ESLint 插件可以从 Visual Studio Marketplace 安装，并提供与 Visual Studio 代码的轻松集成。

[ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

但是它实际上做什么呢？它增加了内嵌代码错误突出显示，这改变了您的编辑体验(如下图所示)

[![](../Images/40065c63ffa8f852e92b554c899b3fae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dpH0qcDy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AO6Q5VDIKC4UM_sfJOlE82w.png)

<figure>[![](../Images/7556387c6588a0bebb8be2f2f9837dc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YV-WcCAD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2An20krHQQGci7D347-4HELQ.png) 

<figcaption>左边:无 ESLint 集成，右边:有 ESLint 集成</figcaption>

</figure>

安装好插件后，ESLint 不再是一个需要手动调用的任务，而是自动运行，显示内嵌在代码中的结果，并在您键入时更新，这样您就可以获得拼写检查器的用户体验，但 ESLint 规则很复杂。

不过，这并不完全是关于内联验证的，它还与编辑器的其他部分进行了一些巧妙的集成，比如“问题”面板:

<figure>[![](../Images/1ea1e0ee07ec7f679fc46e8c80695da7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kv5XTahh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdvvNjcPydPLfeOswJ9u-Sg.png) 

<figcaption>(上图)ESLint 与‘问题’面板的集成</figcaption>

</figure>

#### 分机建议

看到这个编辑器集成确实依赖于安装正确的插件，如果你在开发团队中，这可能是一个有点乏味的任务。为此，我们可以添加一个 VSCode 扩展配置文件，该文件将提示 Visual Studio 代码在适当的时候建议安装扩展。

在名为的目录中创建名为 extensions.json 的文件。项目根中的 vscode，内容如下:

```
{
 "recommendations": [
 "dbaeumer.vscode-eslint",
 "esbenp.prettier-vscode",
 "dzannotti.vscode-babel-coloring"
 ]
} 
```

此功能的官方文档可在此处找到:

[在 Visual Studio 代码中管理扩展](https://code.visualstudio.com/docs/editor/extension-gallery#_workspace-recommended-extensions)

#### 为什么斯洛文尼亚语？

ESLint 的酷之处在于，它可以被扩展以检查不仅仅是逻辑代码错误，它还可以检查可访问性问题或不一致的代码格式，这就是为什么我在插件列表中添加了 [react-jsx-a11y](https://github.com/evcohen/eslint-plugin-jsx-a11y) 和[beauty](https://github.com/prettier/prettier-eslint)插件。

通过所有这些设置，您可以体验将程序与坚持一致性和最佳实践的人配对是什么感觉，而不需要实际进行配对。

<figure>[![](../Images/cd9b2e80941dd2ce53355682d909f505.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KUxp06wi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A84wQvgcY-qeS92WYYvJBTA.jpeg)</figure>

* * *