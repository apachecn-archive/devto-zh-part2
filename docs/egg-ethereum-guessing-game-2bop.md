# 鸡蛋:以太坊猜谜游戏

> 原文：<https://dev.to/dennyferra/egg-ethereum-guessing-game-2bop>

# 我造了什么

🥚彩蛋:以太坊猜谜游戏！你能看到未来吗？然后你会通过玩彩蛋赢得疯狂的网络名声。在这一轮结束前猜猜你认为以太坊的价格是多少，猜得最接近的人赢得这一轮！

*现在是营销版:*

一款创新的实时电子竞技猜谜游戏，利用以太坊分散平台，让玩家能够对未来市场交易进行最佳分析！( *ICO 即将推出*)

## 演示链接

[https://etherguess.herokuapp.com/](https://etherguess.herokuapp.com/)

## 链接到代码

我拆分了后端/前端，因此有两个回购:

*   后端:[https://github.com/dennyferra/PusherContest](https://github.com/dennyferra/PusherContest)
*   前端:[https://github.com/dennyferra/PusherContestFront](https://github.com/dennyferra/PusherContestFront)

# 我是如何建造的🤓

我开始并不真正知道我想要构建什么，但是我知道我想要使用什么栈:Node/Express，React/ [MobX](https://github.com/mobxjs/mobx) 。在此之前，我构建了几个 MobX 项目，我对尝试一些我以前没有使用过的 MobX 特性很感兴趣。我的时间非常有限，所以代码非常杂乱，用胶带和棉花糖拼凑在一起，可能有错误(没有测试)，但我可能会在以后重新访问它，并清理东西。

我第一次使用[布尔玛 CSS 框架](https://bulma.io/)。从一个模板开始，总的来说很容易使用。我还使用了一些来自 [Animate.css](https://daneden.github.io/animate.css/) 的动画。

我偶然发现了 [CodeSandbox](https://codesandbox.io) ，并最终完全使用他们的在线编辑器编写了前端。这是一个非常好的编辑器，感觉像 VS 代码，并且他们支持各种框架，如 React、Angular、Vue 和 Preact。

有趣的是，这个项目最简单的部分是使用 Pusher。我很快就建立了现场频道和公共频道。他们的文档使设置变得非常容易。我唯一的问题是 CodeSandbox 的自动重载——它最终保持通道开放，所以我无意中达到了 100 个并发用户的限制。在我意识到发生了什么之后，我在 CodeSandbox 中更改了一个设置，以完全重新加载更改，然后我的问题就消失了。

在 Heroku 上托管网站，所以当没有用户在网站上时，Dyno 会超时，但这不太可能，因为我很确定这款游戏将成为下一个脸书🔥玩得开心，谢谢！