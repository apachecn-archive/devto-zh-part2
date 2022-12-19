# 揭秘 Redux

> 原文：<https://dev.to/apium_hub/demystifying-redux-3p47>

Redux 是一个帮助你管理应用状态的库。在本文中，揭开 redux 的神秘面纱，我们将不讨论为什么使用它以及如何正确使用它，我们将只关注如何通过接近最终结果来创建我们自己的实现，而不必涵盖它的所有方面、细节和工具。目标是理解它是如何工作的，以消除这个简单库背后的大部分神秘。

我们神秘的 redux 例子是基于一个 Korfbal 球员的名单，我们将能够在下周的比赛中添加召唤。这是一个典型的例子，但带有个人色彩；)

为了看到所有的部分，创建了一个带有不同分支的存储库，在其中我们可以观察和理解不同的块或部分。

*   设置: [Github 链接](https://github.com/parronator/vanilla-typescript-redux-store/tree/1-Setup)
*   初始化存储: [Github 链接](https://github.com/parronator/vanilla-typescript-redux-store/tree/2-Init-Store)
*   动作: [Github 链接](https://github.com/parronator/vanilla-typescript-redux-store/tree/3-Actions-dispatch)
*   减速器: [Github 链接](https://github.com/parronator/vanilla-typescript-redux-store/tree/4-Reducers)
*   商店订阅: [Github 链接](https://github.com/parronator/vanilla-typescript-redux-store/tree/5-Subscriptions)
*   可视化: [Github 链接](https://github.com/parronator/vanilla-typescript-redux-store/tree/final)

## 揭秘 redux:设置

首先，我们将使用 Webpack 来配置整个环境，除了使用 ATL 模块加载 Typescript 文件之外，它还将通过 devServer 在端口 3000 上为我们提供 html。我们还将创建一个 package.json，它将负责公开启动脚本以及项目的必要依赖项。

> 有关更多信息:
> 
> *   [https://webpack.js.org/configuration/dev-serve](https://webpack.js.org/configuration/dev-serve)
> *   [https://github.com/s-panferov/awesome-typescript-loader](https://github.com/s-panferov/awesome-typescript-loader)

我们将从一个 index.html 文件开始，该文件将显示我们拥有的球员人数和一个添加新球员的按钮输入。

[![redux](../Images/fd753e9e702ab08528e4007f2c650c34.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Screen-Shot-2018-08-22-at-12.31.58.png)

我们将创建一个玩家界面，并用它来区分不同类型的玩家。对于本文，我们将不使用类和构造器，只使用类型化的平面对象。

*src/models/Player/entity . ts*

[![](../Images/64e9989a2719a0e1133c53edb8029cbd.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.06.07.png)

为了揭开 redux 示例的神秘面纱，我们将创建一个 app.ts 文件，其中包含“add player”按钮的选项/选择器和另一个输入选择器。接下来，我们将向按钮注册一个事件，这样，当它被单击时，通过一个小的验证，它通过控制台向我们打印一个 player 对象并重置输入。

*src/app.ts*

[![](../Images/94cad6697fc76b262fdbf2ab7e3f14e9.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.08.36.png)

配置和创建了所有这些之后，我们就可以开始项目了，安装依赖项并在控制台中执行 start 命令。

[![](../Images/18c67518cafb26f061b6f4ae90d0ece3.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.09.43.png)

现在，我们应该可以打开浏览器，转到 apiumhub.com: 3000，您将能够看到我们的 html 并单击按钮，通过浏览器控制台查看我们的球员状态。

在我们的示例中，我们可以在事件侦听器中看到第三个布尔类型的参数。根据我的经验，知道这个参数“useCapture”的人并不是很常见所以，我给你一篇解释的很好的文章:[https://javascript.info/bubbling-and-capturing](https://javascript.info/bubbling-and-capturing)

## 解密 redux: Init Store

现在，让我们从这篇文章的重要内容开始。

我们开始创建商店。

*src/store/store.ts*

*[![](../Images/33e42d5fd43857526eb6a4f0004538cd.png)](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.12.32.png)T4】*

*src/models/store.ts*

[![](../Images/b130118b7b2a67cde84643ebd9ebf207.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.13.25.png)

正如我们所看到的，这是一个类，它基本上包含了一个存储的订阅者数组，它将管理我们的 Reducers 列表，还将有一个 status，它只不过是一个平面对象。

构造函数的第一个参数是 reducers，第二个参数是 initial state，默认情况下，我们将它们都设置为空对象。为了创建存储的实例，我们将在构造函数中将初始状态绑定到存储的状态。

最后，由于我们可能需要随时了解应用程序的状态，由于 Typescript 的 get 属性，我们将创建一个 getter 来访问已被定义为 private 的状态值。

为了查看我们的第一步，我们将在 store 文件夹中创建一个桶文件，该文件将导出我们商店的所有内容，我们将在 app.ts 中导入这个名为 fromStore 的文件，并且我们将使用一个空对象(如 reduce)和一个默认状态创建一个新的商店实例。

最后，我们将创建一个 console.log 来记录应用程序的状态值。

*src/app.ts*

[![](../Images/103c69a6af67c32f9b0acb7ab1ce20e4.png)](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.14.55.png) 

如果我们刷新浏览器，应该会看到我们通过控制台定义的初始状态。

## 揭秘 redux:动作

我们将创建第一个操作，当它从“add player”按钮执行时，更新商店的状态。一个动作总是有一个类似 string 的类型，也可以有一个有效负载，为了执行，我们的存储必须有一个按照惯例称为“dispatch”的方法，该方法接收这个动作作为参数。

*src/models/store.ts*

*[![](../Images/870174e454cd15549e236b95bdebdb0c.png)](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.16.29.png)T4】*

*src/store/store.ts*

[![](../Images/15f2f4df8cb67a91900a4c11ce551c42.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.17.09.png)

目前，我们将对玩家的分派方法进行状态更改，因为 reducer 将负责稍后开发的过程。

要改变状态，必须以不可变的方式完成，为此我们将生成一个新的对象，使用 spread 操作符将以前的状态与新的玩家状态合并。我们还在 dispatch 方法中添加了 console.log，以查看应用程序的状态是否已被正确修改。

最后，在我们的 app.ts 中，我们将控制台日志从初始状态中移除，通过使用类型和播放器的有效负载执行 dispatch 方法，从事件侦听器方法中更改 console.log。

*src/app.ts*

[![](../Images/98b8ffde16906bfd8beb54ad301532da.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.18.36.png)

现在，我们应该在控制台中验证当我们添加新玩家时状态是否被正确修改。

## 揭秘 redux: reducers

在这一部分，我们为玩家创建一个减速器。

*src/store/reducers.ts*

*[![](../Images/d2400a9d7e5d941399a3f8ab977e4c98.png)](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.19.54.png)src/模型/玩家/状态. ts*

[![](../Images/bff9465af2a50277eb41e1b58036d35d.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.20.48.png)

你可以想象，在状态中我们可以有多个属性。我们已经用 loaded 属性创建了一个初始状态，它将指示玩家是否已经加载(例如，一个 http 调用)，而 loading 将指示它是否在那个进程中。

这两个属性将不被使用，因为我们没有任何异步调用来改变这种状态，但是它作为一个例子来更好地理解这种管理为我们提供了什么以及在控制台中看到更多的数据。我们还将创建属性数据，这将是一个数组，其中我们将存储所有球员的值，我们通过参数传递给存储实例，以获得示例数据。

我们还将创建属性数据，这将是一个数组，我们将在其中存储所有球员的值，我们通过参数传递给 store 实例，以获得示例数据。

playersReducer 函数接收第一个状态参数，默认情况下，该参数具有我们创建的初始状态，作为第二个参数，它接收一个动作，正如我们已经解释的那样，该动作具有一个类型和一个可选的有效负载。

在里面，我们将使用一个开关来评估动作的类型，作为一个例子，我们将添加我们在按钮事件的动作分派中使用的类型“ADD_PLAYER”。我们用收到的有效负载(在本例中是播放器)组成新的数据状态，最后我们以不可变的方式返回修改后的状态的值。

现在，我们需要注册此减速器，方法是将 reducers . ts 文件的所有内容导出到 bottle 文件，然后将其导入 app.ts。

我们创建了一个名为 reducers 的常量，这是一个对象，它的 reduce 函数指定了一个项目 players。当我们学习 Redux 模式时，一个有趣的部分是我们可以说我们想要几个 Redux 来改变存储状态的不同部分。

现在我们可以用新的对象 reducers 替换 Store 实例的 reducers 参数。

*src/app.ts*

[![](../Images/91164ba0e068bd1395792cac87fedb80.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.21.41.png)

在存储的构造函数中，我们将第一个参数绑定到 reducer，在 dispatch 方法中，我们可以通过一个名为 reduce 的方法来更改状态的修改。我们不应该把它和数组的原型 reduce 混淆，它是我们类的一个函数。使用存储的当前状态和已执行的操作来调用 Reduce。

*src/store/store.ts*

[![](../Images/3a71b63b6e5568394b29261531aa215b.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.22.41.png)

接下来是很重要的部分，所以要注意。

首先记住我们的对象归约者:

*src/app.ts*

[![](../Images/8e34b8bfbd4999d323fa9cbbe0ef58ef.png)](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.23.31.png)

reduce 方法负责生成一个对象，该对象具有与对象 reducer 相同的键，但将所分配的函数 reducer 的返回值作为值分配给每个键。

这个函数，在这个例子中是特定的 playersReducer，给定一个状态和一个动作，用开关评估动作，它返回修改的状态或未修改的状态。

传递给 playersReducer 的状态是特定的迭代器(也就是键的最后一个值)，这样它就不会无法访问我们存储的其他状态。这样我们就能看到我们商店的新面貌。

*src/app.ts*

[![](../Images/8e98c3a2ca61ca19589d11ed175b49a7.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.24.30.png)

为了完成这一部分，我们将构造函数中的状态绑定替换为对空操作 reduce 的调用，并在分配给按钮的事件中添加了分派后应用程序状态值的 console.log。

## 揭秘 redux:商店订阅

到目前为止，我们几乎已经完成了我们的商店，但我们还没有做任何 DOM。我们没有更新元素或显示信息。

如果我们去商店，我们会看到我们已经覆盖了减少者，我们已经覆盖了应用程序的状态，但没有覆盖订阅。

首先，我们将在构造函数中为订阅分配一个空数组。接下来，我们将创建一个 subscribe 方法，允许我们在应用程序的任何位置接收来自商店的更改。这个方法基本上会做的是接收一个函数，将其作为订阅者添加到数组中，并调用每个订阅者，传递存储的值，即我们应用程序的状态。让我们称这个调用过程为 notify。

此外，我们将在每次派单时通知所有订户，以便他们在每次修改时都能收到新的状态。

*src/store/store.ts*

[![](../Images/91306d9facf1e1f70805f5c2008e8c75.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.31.52.png)

接下来，我们将从 eventListener 中删除 console.log，并在我们的 app.ts 中进行订阅，我们将把它作为一个参数来接收一个生成 console.log 的函数。

*src/app.ts*

[![](../Images/e63abd4d884ad383f3f53048a0d7d1e7.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.29.11.png)

如果我们刷新浏览器，我们会看到初始状态是由控制台打印的，当我们添加一个玩家时，我们的订阅者会用应用程序的新状态创建 console.log。

## 去神秘化还原:虚拟化

所有这些都开始成形，但为了让它更华丽一些，我们将在 DOM 上添加行为，并创建一个带有相应按钮的 unsubscribe 方法，这样我们就不会有订阅商店的内存泄漏，无论我们改变场景、删除组件、重新呈现组件还是其他原因都会给我们带来这个问题。

我们将创建一个 renderPlayers 函数，该函数允许我们使用类“Players”将元素迭代地添加到元素 ul 内的 DOM 中。

该函数负责通过参数接收球员列表，在 DOM 中添加球员总数，并为每个球员添加一个带有球员姓名和删除按钮的列表项。此外，我们将更改通过参数传递给它的函数，以订阅这个新的函数，并将订阅分配给变量 unsubscribe。

[![redux](../Images/ebed0073e90e9345e26df34922efdfa2.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Screen-Shot-2018-08-22-at-13.24.18.png)

就性能而言，这不是更新 DOM 的最正确的方法，但是对于我们的例子来说，这是可以的。

将 subscription 赋值给“unsubscribe”变量将允许我们从 Store 的数组订阅中删除该函数，这样当执行 notify()方法时，它就不再在里面，因此不会运行。为此，我们只需在 subscribe 方法中返回一个函数，用一个简单的过滤器处理这项任务。

*src/store/store.ts*

[![](../Images/d219a0486e640f923a814d1bd7dd0b9d.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.33.21.png)

最后，为了证明我们可以取消订阅，我们将在 html 的末尾创建一个按钮，并且我们将添加一个执行该函数的事件。

[![re](../Images/d60bb7d6d3f511dfde9f35247c516db6.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Screen-Shot-2018-08-22-at-13.21.38.png)

*src/app.ts*

[![](../Images/c9551725260786df22fb1e457753de2a.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.34.41.png)

这样，我们可以进入浏览器，添加播放器，查看 DOM 是如何更新的，单击“取消订阅”按钮，查看 DOM 是如何停止更新的，尽管通过控制台我们可以看到状态是如何不断变化的。

至此，我们可以说已经完成了 Redux 的实现。为了完成我们去神秘化的 redux 示例，我们应该在我们的 reducer 中添加更多对

动作的评估，以便它按照预期的方式修改状态。

*src/store/reducer.ts*

[![](../Images/3621ebb975a982b5032ad3968040927b.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Captura-de-pantalla-2018-08-23-a-las-10.35.33.png)

这个新的评估负责以不变的方式过滤通过动作接收的玩家。

最后，我们将事件添加到玩家列表中，方法是定位事件元素，并让它调度一个 REMOVE_PLAYER，将玩家分配给 data-player 属性。

[![](../Images/d8b96776d9b03a27b76471e4243122f4.png)T2】](https://apiumhub.com/wp-content/uploads/2018/08/Screen-Shot-2018-08-22-at-12.34.43.png)

## 揭秘雷杜:结论

正如我们所看到的，这是一个简单的库，我们可以用它来管理我们的应用程序的状态。我们一步一步消除了背后所有的神秘感。现在我们只需要开发一些工具，让我们在使用它的时候有更多的功能或者更少的繁琐。

如果你想了解更多关于揭开 redux 神秘面纱的信息，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月简讯。

## 如果你觉得这篇关于揭开 redux 神秘面纱的文章很有趣，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

[在 Java 中使用 Vavr 功能更强](https://dev.to/apium_hub/be-more-functional-in-java-with-vavr-5b4i)

揭秘替身的帖子[最早出现在](https://apiumhub.com/tech-blog-barcelona/demystifying-redux/) [Apiumhub](https://apiumhub.com) 上。