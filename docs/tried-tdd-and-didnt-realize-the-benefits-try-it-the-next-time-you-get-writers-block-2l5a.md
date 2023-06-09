# 试过 TDD 没体会到好处？下次你有文思枯竭的时候试试吧

> 原文：<https://dev.to/ccleary00/tried-tdd-and-didnt-realize-the-benefits-try-it-the-next-time-you-get-writers-block-2l5a>

***原载于 [coreycleary.me](https://www.coreycleary.me/tried-tdd-and-didnt-realize-the-benefits-try-it-the-next-time-you-get-writers-block/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我还定期发送备忘单、其他优秀教程的链接(由其他人提供)和其他免费赠品。*

你是否曾经尝试过测试驱动开发(TDD ),认为它是经常被说成是“圣杯”,结果却感觉它毫无意义？

也许它没有给你的代码带来任何好处。也许先写测试，然后再写代码，感觉没有启发性或有局限性，或者只是错误的做事方式，特别是因为编程的方式是代码优先，而不是相反。或者这只是感觉像一件苦差事。

所有最优秀的开发人员似乎都在谈论 TDD，好像这是编码的唯一方法，如果你不这样做，那你就错了。所以你很想喜欢。但是，如果你已经尝试过了，但出于各种可能的原因，你并不喜欢它，那么练习它到底有什么意义呢？如果你能有那个使 TDD 有意义的“啊哈时刻”，你可能真的会喜欢它，并且感觉像一个“真正的开发者”

我认为 TDD 的采用经常被以错误的方式鼓励，我将向您展示如何以不同的方式思考它，这将帮助您更快地实现好处。不是因为行业知识规定这是你“应该做”的事情，或者如果你不这样开发，你的团队会看不起你，而是因为当你陷入困境时，它可以是你工具箱中帮助你的最好工具之一。

### 通常是如何被鼓励的

关于 TDD 的价值已经写了很多(首先写你的测试，然后写代码)。采用 TDD 通常有以下好处:

*   更少的错误
*   更快的整体交付
*   较小的单一职责职能

更少的 bug，更快的整体交付，更小的功能——太棒了。一些开发人员/团队真的很难做到这一点，对他们来说，好处可能会更容易点击。但是，如果您在快速交付代码时没有很多错误或问题，并且您的功能已经是单一职责，那么您可能仍然不明白为什么您应该这样做。

上面的“为什么您应该进行 TDD”的论点，虽然肯定是面向开发人员的(尤其是最后一点)，但更多的是针对管理层的。我见过那些从来没有编码的经理，如果有的话，宣称 TDD 是“万能的”,突然命令它成为新的开发风格，最终把它变成为你选择的东西，而不是你选择的东西。这没有帮助。

因此，TDD 可以成为你觉得应该做的事情，因为:

*   你老板让你这么做的
*   行业告诉你
*   如果你不这样做，你会被你的同龄人看不起
*   如果你不这样做，你就看不起自己

或者也许你根本没有这些压力——你只是没有 TDD。也许更少的错误和更容易读/写函数的长期好处现在太抽象了。

但是试图以这种心态采用 TDD，会将 TDD 变成更多的对手，而不是你所做的事情，因为它帮助你——你作为一个开发人员，正处于交付一个特性的压力之下。

### 更有关联的东西

与其从“最佳实践”的角度来理解 TDD，我发现用更具体的术语来理解更容易，一些更相关的东西。

正如这篇文章的标题所述——要让 TDD“点击”,下次你面临写作瓶颈时，试着使用它，从现在开始称为“编码者的瓶颈”。

什么是编码器的块？

你有没有遇到过这样的情况，你完全陷入了如何实现一段特定代码的困境？也许最后期限快到了，如果你不把代码写出来，你就真的完蛋了，但是你一直碰到程序员的瓶颈，不知道如何开始。在我开始使用 TDD 推进这些块之前，我习惯于浏览 Reddit、HackerNews 等。作为一种拖延的方式。要么我被一个非常难的问题压垮了，我不知道从哪里开始分解它，要么这就是那些日子中的一天。

虽然“最佳实践”是抽象的，但我敢打赌，您已经遇到过很多次编码障碍了。但是您可以在这里使用 TDD 来帮助您摆脱这种情况。不是因为有人告诉你，如果你不这样做，你就不是一个好的开发人员，而是因为这会帮助你。

补充说明:我不是 TDD 纯粹主义者。我知道首先写测试并不总是有意义的(R & D 工作，最初的概念证明/草图，纯 DOM/视图代码，等等)。).但是 TDD 作为消除作家/编码器的障碍对我来说是无价的，这就是为什么我在这里推荐它。

### 下次卡壳怎么做 TDD

为了演示您将如何做这件事，让我们想象一个简化的场景。您正在开发的在线购物应用程序有一个特性，其要求是:

*   客户必须能够在“个人资料”选项卡下输入他们的偏好
*   必须保存客户偏好
*   首选项输入字段必须匹配某些正则表达式

想象你陷入困境，不知道从哪里开始，你可以想想你**可以**编写的**第一个测试**会是什么。

这里有几个要求，但是您知道您必须管理所选择/输入的首选项的状态，所以这是一个很好的起点。假设应用程序是用 JavaScript 编写的，测试可能看起来像:

```
import {addPreferences} from '../preferences/preference.service'

import {Preferences} from '../preferences/preference.service'

let pref_service

describe('PreferenceService', () => {
  beforeEach(() => {
    pref_service = new Preferences()
  })

  it('should initialize state', () => {
    expect(pref_service.preferences).to.deep.equal({
      contact_method: null,
      phone_number: null,
      email: null,
      preferred_shipping: null
    })
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来不多，但实际上很多。我们已经弄清楚了我们的状态/偏好需要是什么样的，这是实现的一个有意义的部分。更重要的是，我们一开始根本不知道从哪里开始。

该测试的代码实现示例可能是:

```
export class Preferences {
  constructor() {
    this.preferences = {
      contact_method: null,
      phone_number: null,
      email: null,
      preferred_shipping: null
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

酷，现在另一个测试:

```
it('should add preference to preference state', () => {
  pref_service.setPreferences({phone_number: 'phone-number'});
  expect(pref_service.preferences).to.deep.equal({
    contact_method: 'phone-number',
    phone_number: null,
    email: null,
    preferred_shipping: null
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

还有那个代码:

```
setPreferences(preference) {
  this.preferences = Object.assign(this.preferences, preference)
} 
```

Enter fullscreen mode Exit fullscreen mode

从一个单元测试开始，然后是代码。另一个测试，另一段代码。现在你可能已经越过了你开始时的那个障碍。

### 包扎

以这种方式思考 TDD 将有望帮助您认识到它的威力。很多让 TDD“点击”的事情都进入了一种节奏。更重要的是，把它作为帮助你的工具，而不是你遵循的“最佳实践”。

当你开始前进并越过那个障碍时，它将开始变得更有意义。就像你如何通过写一个待办事项列表来分解一些事情，然后你做列表上的事情——使用 TDD 来克服编码器的阻塞和看起来势不可挡的特性是相同的机制。

这将最终使你成为一名更好的开发人员——通过学习理解需求和将问题分解成可管理的部分来克服障碍。你不仅会花更多的时间编码——这本身会使你成为一个更好的开发人员——而且你会知道如何使事情易于管理。

下一次你遇到困难时，试着在你写代码之前只写一个测试。只有一个。即使通过找出一个起点，这也将极大地有助于摆脱困境并给出一些方向，即使在最初的几次测试之后你没有使用 test-first，你也已经找到了实现的途径。

我认为测试应该尽可能简单，以便消除实际编写测试的障碍。卡在代码上是一回事——除了修复它，你别无选择。但是被考试卡住是另一回事——从技术上来说，你可以跳过考试。

我试图通过发送教程、备忘单和其他开发者的优秀内容的链接，使 JavaScript 的测试和其他事情变得更容易。如果你觉得这篇文章有帮助的话，这是再次注册我的简讯的链接。