# 看一个小的 Elm 应用程序

> 原文：<https://dev.to/nimmo/a-look-at-a-small-elm-application-2loh>

我在 Elm 上发现的一件事是，有很多很棒的资源可以学习如何从语言开始，同样也有很多很棒的资源可以教你如何通过一个[更复杂的应用程序架构，但是我认为让人们从一个更高层次的角度了解你在 Elm 应用程序中可能会发现什么是很有用的。或者至少，这是我认为对我有帮助的事情，不管怎样——希望它能帮助其他人！](https://dev.to/rtfeldman/tour-of-an-open-source-elm-spa)

对于任何对 Elm 一无所知的人来说:它是一种编译成 JavaScript 的函数式语言，关键是不允许出现运行时错误。任何可能导致运行时错误的东西要么没有包含在语言中，要么被编译器捕捉到，并产生友好的编译器消息。

像这样:

[![An image showing the Elm compiler helpfully suggesting that Dict.fromLast should be Dict.fromList](img/571dfbd1eb2025d85c3d640f6cc601ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4kgZKN0F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1hwne0mg8hrzcrtjvxl.png)

## 榆树建筑

在我们查看这个应用程序的代码之前，让我们稍微了解一下 Elm 运行时是如何工作的。

`The Elm Architecture`是如何在 Elm 应用程序中管理应用程序状态。您的应用程序有一个整体模型、一个更新功能和一个查看功能。您的视图函数能够发送消息，这些消息与您的模型一起被输入到您的更新函数中，这将返回一个新的(更新后的)模型，该模型随后用于呈现您的新视图。这方面的图表更有意义，所以:

[![](img/b64a4c7e32b31e12e52a8726d48dde92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oT29AJmn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pfdd18lau3fmjhv63bou.png)

任何熟悉 Redux 的人都应该立即认识到这一点(Redux 受到 Elm 的状态管理的很大影响)，只是对术语做了一些修改。简而言之:

| 榆树 | Redux |
| --- | --- |
| 模型 | 状态 |
| 消息 | 行为 |
| 更新 | 还原剂 |

关键的区别在于，这是 Elm 运行时的构建基础，而不是您选择加入的东西。

## 我们正在看的应用

Project Arklay 是一款超级基础的文本冒险游戏，我已经写了几次，目的是测试不同的工具、想法或语言。简而言之，你(玩家)在各个房间中穿梭，拿起物品并打开门，直到你最终遇到一个突然而令人失望的结局。

既然省了你实际玩的时间，那就来说说吧。

### 应用状态

应用程序可能处于三种状态之一(在编写时)。

要么，你还没开始玩:

[![](img/7413dacc91afb119baa1d4c4d0c6bbcf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0Ay_JIYH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2wbhendxoa5378qy4qgm.png)

或者您已经开始播放，并且正在显示当前可用的方向:

[![](img/dc6cf420c31e2dd3f04c56e3b57ebeb1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FkfbkSL_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/33n6cb3adl1embaxrah2.png)

或者，您已经开始游戏，并且显示您当前的物品清单:

[![](img/a64105a1b413a9bbefc16c47970c5651.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vjwg7tYN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1i5nrewpwaf8flt6lhqx.png)

虽然这是三个独立的有限状态，但是很明显第二个和第三个在整个父视图中是兄弟——所以我们在这里有两个视图。我们将这些称为**介绍视图**和**游戏视图**。

## 原始要求

```
The player can: 

  In the Intro view:

  + Start the game

  In the Game view:
    When the state is Displaying Directions:

      + See which directions are available to move in
      + Select a direction to move in
      + Examine the room (this will pick up any items available in the current room, and add them to the inventory)
      + Open the inventory - so long as there are items in the inventory

    When the state is Displaying Inventory: 

      + See which items are available
      + Use an item
      + Close the inventory 
```

Enter fullscreen mode Exit fullscreen mode

## 这在代码中看起来是怎样的

一行一行地浏览整个应用程序可能不会有太大的帮助，但是查看已经描述过的部分应该会让您对使用 Elm 有一个大致的了解。尽管所有的 Elm 应用程序都有一个 **Main** 模块，该模块有一个 **main** 函数，并且位于一个名为 **Main.elm** 的文件中，但是在这一点上*是值得了解的。同样值得一提的是。elm 文件是它自己的模块。*

首先，让我们看看应用程序状态——还记得我说过我们有两个视图，它们组成了我们所有可能的应用程序状态吗？我们的整个应用程序模型是一个记录(类似于 JavaScript 中的一个对象)，它包含两件事情:

[![](img/87e271c64b4ed349d949ddd3e8662b31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2n3Tt9R3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ucyafdw3l0ly2geued0e.png)

您不需要担心这里的“键”(它的存在是为了允许我们在应用程序的不同页面之间导航，同时控制 URL 中显示的内容，并且由 Elm 的内部生成)，但是 state 字段在这里被定义为保存一个**状态**，这是一个自定义类型。在这个应用程序的例子中，看起来像这样:

[![](img/5482b1d3e9633e4989d81d2a1dbd1229.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--00xskWIt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qmqk9deb5cdeenbjp3jy.png)

因此，整个应用程序的状态可以是 **ViewIntro** (在这种情况下，它需要看起来像 Intro 模块中定义为模型的东西)，或者是 **ViewGame** (在这种情况下，它需要看起来像游戏模块中定义为模型的东西)。这照顾到了我们的两个可能的应用程序状态，我们说过我们的 ViewGame 状态有两个可能的状态，所以让我们来看看什么**游戏。型号**看起来像:

[![](img/8265887c187d94dd283d83c5c92942bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mKyOqLKd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27lmt4f6k2x69081hsl9.png)

太好了，那就没惊喜了-游戏。模型还有一个**状态**键，它也保存一个自定义类型的状态。值得注意的是，这实际上是一款**游戏。State** ，我们最后看到的“State”是一个 **Main。状态**。

当您第一次看到这样的自定义类型时，可能会觉得有点奇怪，所以为了快速澄清一下 Elm 中的自定义类型是可以枚举的类型。你可以把 Bool 的工作方式当作一个例子:它是一个既可以是真也可以是假的类型，如果你要对它进行枚举，你会提前知道你将要处理(最多)这两个值。我们这里的州也不例外。它可以很容易地是一个带有布尔值的“displayingDirections”标志，但我认为有一个很好的好处，即能够查看这里的状态，并快速推理出可能性是什么，如果这是一个“displayingDirections”标志，这将会丢失(因为您不知道它为假会指示什么)。

## 这些状态是如何呈现的

前面我说过视图是基于模型呈现的，我们知道在这个应用程序中，状态也保存在模型中，我们也知道状态是一个自定义类型，所以在代码中看到这一切是如何组合在一起的就不足为奇了:

[![](img/a1139096201ea23f03da4603405d1337.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uzn9vVna--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m30cs00gnacevt0lxkfq.png)

如果这看起来有点混乱，那可能是因为 Elm 中的函数没有将它们的参数括起来，Elm 中的每个函数都有一个返回值，并且 Elm 中的每个函数都是作为单个表达式来计算的。如果上面的代码在 JS 中，它应该是这样的:

[![](img/33de63a37f3a3f94f120f7e6cff1fac5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wop1lUzY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bsplg6wkv8niskeuqyl9.png)

尽管在 JS 示例中，您可能会依赖于状态“ViewIntro”或“ViewGame”。在我们的 Elm 实现中，我们知道它是这两件事情中的一件，因为这是唯一可能的事情——所以在 Elm 中不需要缺省值，而在 JS 版本中我们需要缺省值。

在我们的游戏状态中，只有一部分视图会根据**游戏而改变。State** ，但是它以完全相同的方式工作——在视图发生变化的部分，有另一个 case 表达式:

[![](img/332ee739233ddf632c6146eb0bce5ce8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YCo9iWu5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pl6txczwn2c5vp5ib9ou.png)

## 更新是如何发生的

我有一个自定义类型的 Msg，它保存任何有消息的模块中可能的消息类型(这是你会在几乎所有的 Elm 应用程序中看到的——非常“标准”的处理消息的方式，因为你总是想要对它们运行 case 表达式)。

在我们的主模块中有一个更新函数，它是由 Elm 的运行时运行的。我的**介绍**和**游戏**模块也有自己的更新功能，每当一个**介绍。Msg** 或**游戏。Msg** 由运行时接收，它通过管道进入 Intro.update 或 Game.update，允许消息被处理，模型被正确更新。这种连接不是自动的，但也不是特别复杂——Main 的 **Msg** 类型看起来像这样:

[![](img/7cdc532ca474847c61720458dadc2aa7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yeGoaDHG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/150wvwf145gytbhx5moj.png)

这里有 **ChangedUrl** 和 **ActivatedLink** 消息，因为我需要为运行时提供一条消息，以便在这些事情发生时调用，但是这里我们感兴趣的是 **Intro。Msg** 和**游戏。Msg** 。至此，您可能已经知道我们即将查看 **Main.update** 以了解发生了什么:

[![](img/a71cc056998568136bd7a726bc429eb0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aIlxS1pm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kfvo3mmnjrc08esukvfc.png)

因此，在出现游戏消息的时候，我们会打开游戏**。Msg** 进入 **msgReceived** ，然后我们调用 **Game.update** 连同当前的 **gameModel** 一起传入消息以获得一个 **updatedGameModel** ，然后我们用它来更新我们的状态为**view game updated game model**。

现在如果我们看看这个游戏。Msg 类型，我们可以看到 Game.update 处理了哪些消息:

[![](img/e0e3a3761e5e6bd50bfd3fb32b457d4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0AUmEJlB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bvytmj3rveix8y6ld3bw.png)

当您得知这一切都如您所料时，您不会感到惊讶:

*   **项目**和**房间**都是自定义类型
*   **ToggleInventory** 切换库存是否显示(通过检查当前游戏.模型.状态并切换到另一种可能性)
*   **UseItem** 存储一个项目和一个房间，并检查所讨论的项目是否可以在所讨论的房间中使用(如果可能，就使用它)
*   **ChangeRoom** 存储一个房间并更新 Game.model 以将该房间存储在其 Game.model.room 中
*   **考场**可能有一个物品，如果有，它会将其添加到玩家的物品清单中。否则，库存保持不变。

好吧，最后一个理论上不完全正确。它应该是:

*   **考场**总有一个**也许是**，那个也许有一个项目或者什么都没有。

这听起来可能是一个细微的差别，但是这就是我们如何避免 null 或 undefined 的潜在问题——也可以用一个 case 表达式枚举一个 Maybe，就像在 Elm 中可以枚举的所有东西一样，您必须处理所有的可能性。

让我们来看看**游戏中的考场功能.更新**:

[![](img/0da469aaf8f589e1703864acfbb04fef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0AQHazw4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fwys68ilpm833d7edmuq.png)

如果您不熟悉 Elm，一下子看起来可能会很混乱——请记住，您对曾经使用过的任何其他语言也有同样的感觉！

这里发生的是:

1.  我首先检查是否有一个条目存储在(可能是条目)中，该条目与考场消息一起传递

    *   如果有的话，我会检查玩家是否持有该物品，或者他们是否已经使用过该物品。
    *   在这两种情况下，我都希望它们的库存保持不变，所以我将返回存储在 Game.model.inventory 中的列表的一个相同副本。
    *   如果玩家目前既没有持有该物品，也没有使用过该物品，那么我们将创建一个当前物品的新列表，再加上新物品。然后我们反转它，使新的条目出现在末尾，而不是开始——条目总是被添加到 Elm 列表的开始。
2.  如果一开始就没有项目，那么我将返回一个存储在 **Game.model.inventory** 中的列表的相同副本。

(*不要担心，编译和部署的代码使用存储在原始内存位置的值——但是由于 Elm 中的每个函数都返回一个值，并且 Elm 中的值不能改变，所以我们在这里从概念上返回一个相同的副本)

我们可以看看这篇文章开头描述的功能，看看它与游戏中的信息有什么关系。消息:

| 味精 | 要求 |
| --- | --- |
| 库存清单 | 玩家可以打开库存 |
| 使用项目 | 玩家可以使用物品 |
| ChangeRoom(更改室) | 玩家可以选择移动的方向 |
| 考场 | 玩家可以检查当前房间 |

这些消息没有涵盖的要求都与玩家能够看到一些东西(即可用的方向或他们当前的项目)有关，这不是巧合——用户可以看到的东西是受控的 **Game.view** 而不是 **Game.update** 。

(还有一个鞋底**介绍。 **StartGame** 的 Msg** ，符合我们的其他要求，但是不值得在这里讨论！)

我发现对 Elm 应用程序特别有用的一件事是查看需求并确定某件事情是否是 update 的责任，如果是，那么为它添加一个 Msg。

* * *

无论如何，我希望有人觉得这很有趣——我知道这不是对语言本身的特别深入的探究，但是我觉得它应该给出了一点关于如何查看应用程序以弄清楚它发生了什么的味道。就我个人而言，当我得到这么容易理解的东西时，我总是很高兴:

[![](img/b95a447f31f91a839ebc09493d68a7e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y0CUJIls--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3yhiknbhil6ask9ermf1.png)

如果你感兴趣，你可以看看完整的回购:[https://github.com/dnimmo/project-arklay-v3](https://github.com/dnimmo/project-arklay-v3)(在这篇文章发表后，事情会发生变化，所以这里的例子在回购中很可能与在这篇文章中不同——很乐意回答任何问题，随时联系)

或者你也可以在游戏还可以使用的时候玩游戏本身