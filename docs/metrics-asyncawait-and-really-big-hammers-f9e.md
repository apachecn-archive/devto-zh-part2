# 度量、异步/等待和真正的大锤

> 原文：<https://dev.to/bhaibel/metrics-asyncawait-and-really-big-hammers-f9e>

有一天晚上，我的合作伙伴 Chris 在吃饭时问我，“Betsy，你如何使用 async/await 处理对度量库的调用？”

Chris 在一家电子商务公司制作开发工具。JavaScript 在他工作的栈中并不是很大的一部分，但是现在每个人都至少使用一点 JavaScript。他需要为与他一起工作的应用程序开发人员公开度量 API。他希望它们易于使用，并且不会妨碍他们的应用程序代码。

关于度量调用有两点:首先，它们需要*便宜*。当开发人员度量事物时，您不希望对他们施加运行时性能惩罚。如果你这样做了，开发者就无法度量那么多东西了！然后，当一切崩溃，他们需要*测量*东西时，每个人都陷入猜测和祈祷调试。在 JavaScript 上下文中——您不希望度量调用阻塞执行线程。

其次，我们通常对指标调用的返回值不感兴趣。我们往往甚至不在乎他们是否失败！指标调用不是为了产生用户关心的结果。它们是关于观察获得结果的过程。当我们报道只有开发者关心的事情时，我们不想妨碍用户。

所以:度量调用不应该阻塞线程，我们不关心它们的返回值。因此，Chris 认为在函数中间插入一个度量调用并等待它是一个坏主意。

```
async function showPrice(item) {
  const priceWithoutShipping = onSale ? salePrice(item) : item.price;
  const shippingCost = await shippingRate(item);
  const totalPrice = priceWithoutShipping + shippingCost; 

  await postMetrics('userCheckoutStep3', totalPrice);
  showUserTotal(totalPrice);
} 
```

他说得对。在`showUserTotal`之前的`await`意味着用户的购物车在指标发布之前不会更新！这可能意味着他们会感到沮丧，在买东西之前结账。呈现这种风险的度量 API 将会比无用更糟糕。所以 Chris 想知道——在函数末尾制定一个仅使用`await`指标的约定是否更有意义？

这个想法有一些问题。这段代码很好:

```
async function showPrice(item) {
  const priceWithoutShipping = onSale ? salePrice(item) : item.price;
  const shippingCost = await shippingRate(item);
  const totalPrice = priceWithoutShipping + shippingCost; 

  showUserTotal(totalPrice);
  await postMetrics('userCheckoutStep3', totalPrice);
} 
```

但是下一段代码——实际上是相同的——有一个错误。你能发现它吗？

```
async function showPrice(item) {
  const priceWithoutShipping = onSale ? salePrice(item) : item.price;
  const shippingCost = await shippingRate(item);
  const totalPrice = priceWithoutShipping + shippingCost; 

  showUserTotal(totalPrice);
  return totalPrice;

  await postMetrics('userCheckoutStep3', totalPrice);
} 
```

不同之处在于，我们在函数中间添加了一个 return 语句。执行永远不会超出 return 语句，因此度量调用永远不会发生。

如果我们改变它，使指标调用在返回的最后一行之前的最后一行*会怎么样？*

```
async function showPrice(item) {
  // beginning of function

  await postMetrics('userCheckoutStep3', totalPrice);
  return totalPrice;
} 
```

这对我们没什么帮助。我们又回到了阻塞函数工作的一部分——返回语句——对指标调用的结果！我们不知道需要多长时间才能完成。我们确实知道——计算机就是计算机——它可能很慢而且不稳定。我们不希望这惹恼我们的用户。

这是个难题。

让我们重新检查一下编写这段代码时的假设。我们真的需要用`await`吗？如果我们移除它，会发生什么？

```
async function showPrice(item) {
  // beginning of function

  showUserTotal(totalPrice);
  postMetrics('userCheckoutStep3', totalPrice);
  return totalPrice;
} 
```

答案是:它工作得很好！因为我们之前一直在等待`postMetrics`，我们知道这个函数返回一个承诺。但是仅仅因为它返回一个承诺并不意味着我们需要用这个承诺做任何事情。毕竟，我们不需要它解析的值。我们可以删除关键字`await`。`postMetrics`会执行它的网络调用或者套接字写什么的。它会在后台愉快地运行。用户不需要关心，我们也不需要。

想一想，`postMetrics`连承诺都需要回吗？我们来看看目前对`postMetrics`的定义:

```
async function postMetrics(eventName, metadata) {
  return fetch(METRICS_URL, {method: 'POST', data: { eventName, metadata }}).catch(noop);
} 
```

这是一个异步函数，它返回 fetch 的结果 fetch 也返回一个承诺。但是如果我们没有让它回报这个承诺呢？我们通过删除 return 语句来做到这一点。我们也可以删除 async 关键字。这里不相关。我们不需要保证`postMetrics`返回一个承诺，也不需要`await`在里面。

```
function postMetrics(eventName, metadata) {
  fetch(METRICS_URL, {method: 'POST', data: { eventName, metadata }}).catch(noop);
} 
```

仅仅因为一个函数确实异步工作，并不意味着它需要用`async`关键字来标记，或者返回一个承诺。您可能希望从函数中返回承诺有三个原因:

*   你关心承诺的价值。
*   您关心操作是成功还是失败。
*   在你继续下去之前，你关心副作用*已经发生*。

因为这些都不是这里的情况，所以不从`postMetrics`返回任何东西是安全的。

对于“有时候事情悄无声息地失败也没什么大不了的”这句话来说，这实在是太多了。这里有什么更深层次的寓意吗？

Chris 没有问我“我应该如何设计我的指标 API？”他问，“我应该如何在我的度量 API 中使用`await`”相反。

现在，async/await 是 JavaScript 领域中很酷的新生事物。Chris 已经很多年没怎么用过 JS 了，但是即使是他也知道这是他现在“应该”使用的。每当我们学习一种新的编程技术时，就很容易患上锤子和钉子综合症。

Async/await 是一把真正闪亮的锤子。我们在 JavaScript 中用异步代码做的很多事情并不是复杂的事件代码。只是在等东西。它在等待 API 结果或数据库更新，做一个小计算，然后等待其他的东西。它和 Python 中相同代码的唯一区别是，在我们等待的时候，执行线程在做别的事情。Async/await 使我们的代码看起来像 Python 中的代码一样，而不是跳进承诺链的地狱。

这是一场如此明显的胜利，以至于很容易认为这应该是一场无处不在的胜利。

但这意味着我们开始审视我们的代码并问，“我如何应用这个解决方案？”这很危险，因为这意味着我们直接跳过“我在试图解决什么问题？”

Async/await 可以解决很多问题。但事实上，它通常是答案，并不意味着我们可以跳过问这个问题。

* * *

想了解更多关于 async/await 或异步 JavaScript 的知识吗？报名[解开异步 JavaScript](/products/untangling-asynchronous-javascript-free-email-course) ！这个免费的电子邮件课程着眼于异步 JavaScript 的历史。它将 async primitves(如事件发射器、承诺和 async/await)放在人们试图解决的问题的上下文中。这让我们来讨论一下它们各自的优点，以及它们的缺点。