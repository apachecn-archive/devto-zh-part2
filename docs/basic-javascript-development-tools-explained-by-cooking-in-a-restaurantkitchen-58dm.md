# 通过在餐厅厨房烹饪来解释基本的 JavaScript 开发工具

> 原文：<https://dev.to/kbk0125/basic-javascript-development-tools-explained-by-cooking-in-a-restaurantkitchen-58dm>

如果你能设身处地为餐馆厨师着想，那么你就能理解像 NPM、Webpack 和 Babel 这样的常用工具是如何协同工作的。

我要在这里对你说实话。尽管我在 18 个月的时间里自学了前端和后端 web 开发，但我从未真正理解过我在开发环境中使用的不同工具。

咕哝？大口？NPM？纱线？巴贝尔？打字稿？Webpack？手写笔？我会简单地在线阅读一个博客，然后完全复制他们的开发环境。

经过几个小时的努力，我终于让所有的部件都工作在一起，然后我会尽量避免碰那个该死的东西，这样我就不会把它弄坏了。

你可以想象…这使得添加任何新技术变得困难！

我想了解所有不同的组件，比如包管理器、模块打包器、传输器和任务运行器。如果你知道所有这些单词，但不知道它们是什么意思，这个指南是给你的！

事情是这样的——我将展示所有这些工具是如何协同工作的，就像厨师长确保食物被正确地送到餐厅的所有桌子上一样。

要理解它，你只需要在过去去过几家餐馆。不需要厨师经验🙂而且，你可能需要有一点使用 NPM 和 Webpack 这样的工具的经验。这样，你就能理解让他们一起工作的挑战。

## 餐厅-开发商环境的基本类比

首先，让我们考虑一下餐厅的两个部分。

[![KitchenToTable](img/7a4137e91eaa155e9dec2920b120dfcb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OJ-id-ua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/kitchentotable.png)

厨房想做好吃的，顾客想吃好吃的。然而，每一方处理与食物相关的信息的方式有显著的不同。厨房必须有更复杂的认识，才能快速做出各种菜品。为了支持这一目标，他们必须准备好运行许多流程。

客户只需要有限的理解。每位顾客只需要充分理解菜单，就可以点一道他们喜欢的菜。作为厨师，你不知道你的顾客对食物了解多少。你基本上只能保证他们识字。你对它没有多少控制力。

[![serverVSbrowser.png](img/8e4e65eed199713edaf65b63f1d2fe45.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8u0xCbeX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/servervsbrowser.png)

同样，服务器和浏览器可以处理的信息也有所不同。有许多语言和框架可以在服务器端使用，这取决于您如何配置它。您可能已经习惯了 node.js，这是一个 JavaScript 运行时。你对它有很大的控制权。

但是，您对浏览器中发生的事情的控制要少得多。请记住，像谷歌、Mozilla 和微软这样的公司必须构建自己的浏览器，以便在所有类型的计算机上运行，并遵循(某种程度上)一致的协议。而且，他们的浏览器的传统版本(咳咳，IE9)在未来几年内仍然会存在。他们只会运行 HTML，CSS 和 JavaScript。你对这一方的控制要少得多，你需要符合他们的标准。

因此，让我们深入了解典型开发环境中的各个工具！

## 了解包管理器(NPM)

好吧，既然你是厨师，你就要负责每周 7 天提供美味的食物。用餐高峰期已经够忙乱的了，所以你想让厨房尽可能高效地运转。

比方说，你需要储存大约 60 种不同类型的食物来准备你的每一餐:蔬菜、肉类、配菜和其他任何东西。

[![BasicFoodCans](img/ccfb1a04660f424b0bc5b256a1b2e9ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o50TWUco--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/basicfoodcans.png)

(好吧，我希望你的餐厅尽可能使用新鲜的食材，而不是罐装的食材，但是请原谅我。)

想象一下，你需要亲自跟踪所有这些原料，并确保你有足够的数量来熬过整个晚上。这绝对是一场噩梦！你会不断地发现自己的原料用完了，并急于找到解决方案。

相反，你希望你的厨房在每晚开始时自动装满食物。你需要一个工作人员来监控这一点，并确保你有一套一致的成分，这样你就永远不需要即兴发挥。

[![FullyStockedKitchen.png](img/3670a9009714f2901f79643b936407d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TW9Mxisd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/fullystockedkitchen.png)

现在，你可能会说，*“代码永远不会‘用完’，那么一个软件包管理器怎么会和这个类似呢？”*

这就是没有包管理器的生活。

1.  下载每个单独的包，并将其添加到您的 */node-modules/* 文件夹中。
2.  给团队中的每个人发一封电子邮件，这样他们也可以下载该文件
3.  希望并祈祷随着岁月的流逝，它将与所有新的软件包一起很好地工作。

随着您向堆栈中添加新的工具，这很快变得不可维护。

[![BasicFileStructure](img/771d512f003a5c245c1960a1e82a2f9f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gz1_aAvT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/basicfilestructure.png)

React、Webpack、Babel 和 Stylus 是三种不断更新的常用技术。您可以通过将新版本拖放到您的文件系统中来不断更新这 3 个版本。或者，你可以使用像 [NPM](https://www.npmjs.com/) 这样的包管理器为你自己和任何队友自动完成这项工作。这意味着不用拖放新文件夹和代码，也不用担心新包不能一起工作。

这有点像你厨房里需要的所有食材的持续清单。你的员工会一直把它们放在手边，如果你改变了你需要的配料，你所有的员工都会立即知道。

这里有一个摘自[彼得·张的《现代 JavaScript 指南》的例子。](https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70) *Package.json* 是你的服务器端代码中涉及到的所有工具的可靠记录。

[![example packagejson](img/e95b12c1fa4254f0f8327280eb087049.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rx_txK3q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/example-packagejson.png)

*dependencies* 属性跟踪生产环境中涉及的所有包。

您可以在命令行中使用 *npm install* ，这样您就不需要不断地复制和粘贴文件夹。

[![npminst.PNG](img/dcbba6d0fe082fca2916e865181af0c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DgAbBqJe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/npminst.png)

## 了解模块捆绑器(Webpack)

到目前为止，你的材料都很好。但是，你没有办法把你的开胃菜、正餐和甜点传达给顾客！你所拥有的只是菜谱、手写笔记以及厨师与厨房沟通的其他方式。

[![recipe](img/b7cc56e8b92e491a5e29140c58c0f7f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wpEJ_ZRX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/recipe.png)

想象一下，如果您的男女服务员向顾客分发一叠食谱，并依靠顾客来算出每顿饭都有什么？你的餐厅会空无一人！

相反，你需要一个菜单(显然)。菜单以简明的方式提供食物信息，让顾客能够真正理解。记住，浏览器只能理解 HTML、CSS 和普通的 JavaScript。所以你不能指望从 React 给它 JSX 文件，然后指望它工作。

您的浏览器无法访问您后端的文件，因为它不理解这些文件！即使您将文件路径链接到您在前端使用的文件，它也无法找到它。

这就是模块捆绑器的用武之地。正如术语“bundler”所暗示的，像 Webpack 这样的工具将聚合所有在服务器端编写的代码，用于前端。模块捆绑器会把它编译成一个可以在前端使用的大型普通 JavaScript 文件！有点像菜单——一个可以理解的，全面的食谱总结。

[![menu](img/765d67f5d3d801d6e164b860cae26bcf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d63PiAPj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/menu.png)

这是一个基本的例子，同样来自 Peter Jang，使用了 [Moment.js 包](https://momentjs.com/)。

这是来自后端的示例文件。

[![momentJSexample](img/fe5a3fb5a1d5235f8ca34466c441dbcc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u-VFT1Mj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/momentjsexample.png)

如您所见，有一个 require 语句，这不是普通 JavaScript 的一部分。所以，你需要一个**模块捆绑器**来让它在前端可以被理解。

在您 npm 安装 webpack 之后，这里是您的配置文件的一个基本版本。

[![webpackbasicconfig](img/78f5e23f0c97bc5710ce826f5fef10d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gJkj7X3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/webpackbasicconfig.png)

**入口文件**描述了哪些文件需要编译。这些是我们类比中的配方。而**输出文件名**就是你可以找到结果的地方——菜单！

然后，您可以在 HTML 中访问该文件，代码可以在浏览器中运行。

[![bundleOnfrontend](img/e4ae94a2d7515ea1f379dde4cf4d8652.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MlDghbD2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/bundleonfrontend.png)

这是一个直观的展示。

[![basicWebpackbundle.png](img/e1e6c4b35b4f97cba531e5c9c21667e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0RKI5Mmr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/basicwebpackbundle.png)

记住，我们上面的例子只使用了力矩！如果你捆绑了 5 种不同的工具，那么每种类型的文件都会有更多的复杂性和更多的属性。

## 了解 Transpiler(手写笔)

随着您对 CSS 和 JavaScript 有了更好的理解，您会发现默认语法缺少一些关键特性，而这些特性会让您的生活变得更加轻松。例如，如果你有一些快捷方式，有很多 CSS 的例子可以更容易地编写。

例如， [Stylus](http://stylus-lang.com/) 是 CSS 的流行替代品，它使得创建样式规则的时间变得更容易忍受。下面是一个片段。

[![stylusExample](img/6e4428668c140bc6cdb28f2a2267914f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kPOK0Wm2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/stylusexample.png)

但是，正如我们已经知道的，浏览器本身不能读取手写笔。风格文件。因此，当我们捆绑模块时，我们需要将手写笔文件转换成 CSS。

这个感觉像是模块捆绑器的扩展，因为它有相同的通用目的:使编写的服务器端代码在浏览器中可用。然而，这一个可能吸引更高级的观众，因为你需要在进入 transpiling 之前使用 Stylus、Babel 或 TypeScript。然而，几乎所有使用 React 的人都需要使用模块捆绑器。

因此，考虑到这一点，我认为 transpiling 是餐厅的菜单设计师。你可以让厨师或经理用他们所有的图形设计技巧(不太可能)，用 Microsoft Paint 或其他工具创建菜单。这仍然是可以理解的，尽管对餐馆的品牌来说，这可能不是一个伟大的决定。

或者，你可以聘请专业的菜单设计师，让你的菜单看起来更专业。这不是一个要求，但它允许你用菜单做更多的事情，而不仅仅是在 Microsoft Word 中找到的一些模板。

[![transpilerBasics](img/40090039fcd776530117537596c02fe5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---2cn8MLE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/transpilerbasics.png)

下面是代码的样子。我们将使用 webpack 的 [stylus-loader](https://github.com/shama/stylus-loader) 包。首先，安装它。

`npm install stylus-loader stylus --save-dev`

然后，您可以根据 webpack.config 文件的其余部分，以多种方式设置它。这里有一个例子。

[![stylusSetup](img/aacc88bf90582c84974ffadef6c1a1c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AjyvCF2Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/stylussetup.png)

## 了解任务运行者(NPM 脚本或咕哝)

在我们的开发环境中还有一个问题我们还没有涉及到。要执行上面的任何命令，您仍然需要在命令提示符中手动键入命令来运行代码。那真的是不可扩展！

想象一下，如果您正在编辑一个 React 文件，并且每次想要重新加载本地服务器时，您都需要在命令提示符下键入一个命令。那将是一个巨大的痛苦。

如果你是一名厨师，这将意味着所有的服务员和清洁人员将只是在厨房周围等待你的订单。你的餐厅效率会很低。

[![WaitersTablesTaskRunner.png](img/74cc6274986cd685572a816c2c24e79a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0vBww6wh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/waiterstablestaskrunner.png)

想象一下，如果每个服务员都在等待订单，比如:

*“清洁工作台 3”*

*“向 2 号桌供水”*

*“从表 1 请求订单”*

相反，你希望服务员有固定的程序。他们应该能够跟踪他们的表，而从其他任何人那里得到最少的反馈。

[![goodrestaurantTaskRunner.png](img/a8750a65c02d65b01d916f46890f78ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GwDwPxA7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeanalogies.files.wordpress.com/2018/01/goodrestauranttaskrunner.png)

在代码中，这就是为什么您需要一个**任务运行器**。您希望自动执行一些反复使用的一致任务，这样就不需要经常打开命令提示符。

实际上，您可以在您的 *package.json* 文件中直接使用 NPM 来完成这项工作。我们再来看看 webpack。您不希望每次更改 React 或手写笔文件时都运行 webpack。每当您更改 webpack 正在监视的一个文件时，您希望**自动重新运行任务**。

您可以使用 *package.json* 中的脚本部分来做到这一点。

```
"scripts": { 
  "watch": "webpack --watch"
}, 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以在命令提示符下键入 watch 命令一次，webpack 将在您每次更改指定文件夹中的文件时运行。

*$ npm 跑步手表*

## 接下来是什么

你喜欢这个教程吗？请在评论中告诉我，或者查看我对常见 web 开发挑战的其他解释。