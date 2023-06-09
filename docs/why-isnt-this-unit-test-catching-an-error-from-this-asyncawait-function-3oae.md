# 为什么这个单元测试没有从这个 async/await 函数中捕获一个错误？

> 原文：<https://dev.to/ccleary00/why-isnt-this-unit-test-catching-an-error-from-this-asyncawait-function-3oae>

***原载于 [coreycleary.me](https://www.coreycleary.me/why-isnt-this-unit-test-catching-an-error-from-this-async-await-function/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我还定期发送备忘单、其他优秀教程的链接(由其他人提供)和其他免费赠品。*

当您在 JavaScript 中为异步函数编写单元测试时，您通常想要的一个测试用例是确保异步函数在出错的情况下抛出一个错误。

让我们想象一下，为调用数据库并返回项目的项目函数编写一个测试:

```
const fetchItem = async function (itemName) {
  if (typeof itemName !== 'string') {
    throw new Error('argument should be a string')
  } else {
    return await db.select(itemName)
  }
}

module.exports = {
  fetchItem
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:通常我不喜欢对参数进行类型检查，但是这对于演示来说很容易。

一个合理的单元测试应该是这样的:

```
const { fetchItem } = require('../path/to/fn')

describe('#fetchItem', () => {
  it('should catch an error', async () => {
    await expect(fetchItem(3)).to.eventually.throw()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们用一个不是字符串的参数调用`fetchItem()`函数(这是我们的数据库查询所期望的)。这是一个异步函数，所以我们`await`它，并期望它最终`throw`，因为如果传递一个非字符串参数，函数将抛出一个`new Error`。

好像应该会过去吧？

那么为什么测试会失败，并出现一个未被捕获的错误呢？为什么错误只是出现在控制台中，而没有通过测试？

让我们来看看为什么它不工作，以及如何修复它...

### 为什么它不像你期望的那样工作？

`async/await`的美妙之处在于它让*异步*代码读起来就像是*同步*代码。如此同步，以至于很容易忘记您仍然在处理异步代码。

重要的是要记住，在 JavaScript 中，每当你使用`async`关键字时，它总是返回一个承诺。当你有一个返回承诺的函数时，它要么被解决，要么被拒绝。

当我们像在`fetchItem()`函数中那样抛出错误时，

```
if (typeof itemName !== 'string') {
    throw new Error('argument should be a string')
} 
```

Enter fullscreen mode Exit fullscreen mode

真的是**拒绝**承诺。它会因错误而拒绝，但这仍然是一个被拒绝的承诺。

### 狐狸

解决这个问题非常简单。将 [chai-as-promised](https://github.com/domenic/chai-as-promised) 导入到您的测试中，如下所示:

```
const chai = require('chai')
const chaiAsPromised = require('chai-as-promised');

const expect = chai.expect
chai.use(chaiAsPromised) 
```

Enter fullscreen mode Exit fullscreen mode

然后将测试改为:

```
describe('#fetchItem', () => {
  it('should catch an error', async () => {
    await expect(fetchItem(3)).to.be.rejected
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

所改变的是不再是`to.eventually.throw()`，而是变成了`to.be.rejected`。如果您想测试以确保它被正确的错误消息拒绝，您可以将其更改为`to.be.rejectedWith('argument should be a string')`。

### 关于返回与等待的一个注记

柴会等待承诺，所以不用`await`而用

```
await expect(fetchItem(3)).to.be.rejected 
```

Enter fullscreen mode Exit fullscreen mode

你可以使用`return`

```
return expect(fetchItem(3)).to.be.rejected 
```

Enter fullscreen mode Exit fullscreen mode

我更喜欢使用`await`，因为它提醒我正在使用一个`async`函数，但是如果你发现其他使用`return`的例子，这是值得指出的。

### 包扎

对于本机承诺，当遇到错误场景时，您显式地拒绝承诺，这更容易记住您是在测试被拒绝的承诺，而不是被捕获的错误。

我已经为抛出错误的`async/await`函数编写了大量的**工作**测试，但是仍然很容易忘记。我最近在为单元测试节点服务的[场景的帖子写代码时遇到了这个问题，其中涉及到大量的异步代码。顺便说一句，如果你正在寻找一个节点服务的常用测试列表，一定要看看那个帖子。](https://www.coreycleary.me/know-what-to-test-using-these-recipes-node-service-that-calls-a-database/)

我认为测试应该尽可能简单，以消除实际编写测试的障碍。卡在代码上是一回事——除了修复它，你别无选择。但是被考试卡住是另一回事——从技术上来说，你可以跳过考试。

我试图通过发送教程、备忘单和其他开发者的优秀内容的链接，使 JavaScript 的测试和其他事情变得更容易。如果你觉得这个教程有帮助的话，这是再次注册我的简讯的链接。