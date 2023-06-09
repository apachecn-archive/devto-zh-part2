# 解释 JavaScript 单元测试，就像我五岁一样

> 原文：<https://dev.to/ccleary00/explain-javascript-unit-testing-like-im-five-3k73>

***原载于 [coreycleary.me](https://www.coreycleary.me/explain-javascript-unit-testing-like-im-five/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我还定期发送备忘单、其他开发人员的优秀教程的链接以及其他赠品！*

单元测试对于好的软件开发来说是如此重要，然而对于初学者(以及许多有经验的专业人员)来说，一开始可能会感到陌生和不舒服。它可能是你知道你应该做的事情，但是没有时间去学习，或者试图去学习但是没有取得很大进展。这也可能是你以前从未听说过的事情。

特别是当你是一个新的 JavaScript 或节点开发人员，并且你有上百万的其他东西要学的时候，很容易“卸载”它，为你当前关注的事情腾出更多的精神计算能力。

无论您是否在学习的过程中觉得可以学习单元测试，对它们是什么以及它们的用途有一个很好的高层次的理解将会帮助您现在和不久的将来。

### ELI5(像我五岁一样解释)

在高层次上，单元测试是证明您的代码按预期工作的测试。它们就像一张“安全网”还记得你在数学课上不得不做证明吗？他们有点像那样。[旁注:有其他可能更好的类比，称为形式方法，但现在不要担心这个]

当您正在开发一个应用程序，并且希望对现有代码进行更改时，会发生什么情况呢？这会破坏应用程序吗？你怎么知道的？

你怎么知道最初为什么要写代码？系统或业务需求是什么？

这就是单元测试的目的。如果您确实对一个函数进行了更改，并且已经编写了足够好的测试来涵盖该更改，那么您应该能够满怀信心地进行更改。它们也应该作为你的应用程序的一种文档形式，当你不能从代码中理解这一点时，你可以去阅读并理解应用程序的意图。

### 了解结构

所有的项目都不同，但是许多项目都遵循将代码放在 *src/* 中并将测试放在 *test/* (或者 *tests/* )中的文件夹结构。我将在这里链接一个我自己的 [GitHub repos 来演示，因为这是我的代码，我很了解它。这个回购协议在结构上也非常简单，所以这也使得演示单元测试变得容易。](https://github.com/coreyc/es6-curry-compose-pipe)

在 *tests/* 文件夹中有一个测试文件(一个`*.spec.js`文件)映射到 *src/* 中的每个文件。有时测试文件可能是`*.test.js`或`something-test.js`的格式，但这只是格式化。重要的是它们都遵循相同的格式。同样，所有的项目都有不同的结构，但是你通常会发现每个 JS 文件都有一个测试文件。

让我们来看看其中的一个测试:

```
const compose = require('../src/compose')
const expect = require('chai').expect

describe('COMPOSE', () => {
  it('should compose a function from right to left', () => {
    const minus2 = num => num - 2
    const times2 = num => num * 2
    const result = compose(times2, minus2)(4)
    expect(result).to.not.equal(6)
    expect(result).to.equal(4)
  })
  it('should compose a function with one function', () => {
    const minus2 = num => num - 2
    const result = compose(minus2)(4)
    expect(result).to.equal(2)
  })
  it('should compose a function with more than one function', () => {
    const minus1 = num => num - 1
    const times2 = num => num * 2
    const result = compose(times2, minus1)(4)
    expect(result).to.equal(6)
  })
  it('rightmost function should be variadic (accept more than one argument)', () => {
    const addGreetings = (greeting1, greeting2) => greeting1 + '  ' + greeting2
    const sayItLoud = greeting => greeting.toUpperCase()
    const oneArgProvided = compose(sayItLoud, addGreetings)('hi')
    const allArgsProvided = compose(sayItLoud, addGreetings)('hi', 'there')
    expect(oneArgProvided).to.equal('HI UNDEFINED')
    expect(allArgsProvided).to.equal('HI THERE')
  })
  it('all other functions besides rightmost should be unary (accept only one argument)', () => {
    const addGreetings = (greeting1, greeting2) => greeting1 + '  ' + greeting2
    const addMoreGreetings = (addedGreetings, addtlGreeting) => addedGreetings + '  ' + addtlGreeting
    const allArgsProvided = compose(addMoreGreetings, addGreetings)('hi', 'there', 'tests')
    expect(allArgsProvided).to.equal('hi there undefined')
  })
}) 
```

您可以看到，在文件顶部的`compose.spec.js`测试中，您将导入想要为其编写测试的代码:

```
const compose = require('../src/compose') 
```

然后在测试文件的主体中，你会发现一个`describe()`，它可以被认为是一组测试，后面跟着一串`it()`，它们是单元测试本身(称为“断言”)，例如:

```
it('should compose a function from right to left', () => { etc....} 
```

```
it('should compose a function with one function', () => { etc...} 
```

诸如此类。

在很大程度上，这种`describe (grouping of tests) -> it (assertion for specific unit)`模式是您在 JavaScript 单元测试中会发现的。

这些测试声明了`compose`模块在一组给定的环境下应该做什么，这是您作为开发人员想出来的。测试是有指导原则的——它应该接受正确的参数类型，应该返回应该返回的东西，等等。-但这在很大程度上取决于应用程序以及该功能在应用程序中的行为方式。在这一点上，你可以运用你最大的判断力来弄清楚它们应该如何被编写，并且这在实践和理解需求时发展得最好。

这让我想到了更重要的一点:

单元测试在*单元*级别测试事物，这意味着*最小的*功能块有意义测试。单元测试将**而不是**测试类似这样的东西:“用户应该能够点击一个按钮，这个按钮应该调用一个服务，这个服务应该注册用户，这个服务应该向用户返回一个成功的消息。”这将被认为是一个*端到端的*测试，如果你发现自己在编写这样的单元测试，你需要进一步分解它们。您可以将端到端测试按照每个“应该”进行分解，这将更接近于单元测试，当然这取决于代码。

提示:一个好的通用经验法则是对代码库中的每个公共函数进行单元测试。

最后，在编程界有很多关于代码覆盖率应该是多少的讨论。

当你第一次学习单元测试时，这根本不是你要关心的事情，甚至当你对编写测试感到满意时，关于“你应该有什么样的覆盖率”的讨论可能会误导你。

误导，因为它通常是错误的指标。您应该编写好的测试，而不是达到一些任意的指标，这些指标通常被管理层用作“复选框”指标。

但是什么是好的测试呢？

好的测试是这样的，别人可以阅读并找出你为什么以这种方式写东西，应用程序的需求是什么，并且应该随着代码的破坏而中断。这是一个基本的启发式规则，当然，可以根据你的特定应用/团队/场景等进行添加。

### 从这里去哪里

您不必今天就开始编写单元测试来利用它们。了解了它们是什么以及如何在您的项目结构中导航它们，您就可以开始四处探索，看看当前有哪些测试。

您可以对测试中的代码进行小的修改，运行单元测试(通常在您的项目中是`npm test`),然后看看会发生什么。

当你觉得你开始掌握它时，你可以从为你写的一段代码添加一个小测试开始，然后从那里开始。

虽然单元测试看起来很难开始，但这通常是一种幻想。从小处着手，逐步提升。我确信你可以在一周内开始添加小测试。您从中获得的好处将是巨大的——为您的代码提供文档，为进行更改提供安全保障。

我正在编写许多新的内容来帮助 JavaScript(以及一般的 JavaScript)的测试变得更容易。更容易，因为我觉得不需要像有时候那么复杂。如果你不想错过这些新帖子中的一个，这里是再次链接[订阅我的时事通讯！](https://www.coreycleary.me/about/)T3】