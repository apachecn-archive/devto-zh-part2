# 你确定要离开吗？—卸载事件前的浏览器

> 原文：<https://dev.to/chromiumdev/sure-you-want-to-leavebrowser-beforeunload-event-4eg5>

[https://www.youtube.com/embed/QQukWZcIptM](https://www.youtube.com/embed/QQukWZcIptM)

在视频中，我解释了一下`beforeunload`事件——它让你提示或警告你的用户他们将要离开你的页面。如果被误用，这可能会让你的用户感到沮丧——你为什么要使用它呢？💁‍♂️ℹ️

✅你的用户正在填写表格，例如购物
✅正在发送网络帖子，例如保存偏好
✅你的用户正在写博客帖子或评论，但是会丢失
🤷视频或音乐将停止播放
⛔你的用户还没有读完一篇文章
⛔电子邮件客户端有一封未读邮件
⛔有一个时间敏感优惠！立即购买！🙄💸

## 重要的要记住

在我们进入代码之前，什么是**TL；我视频里的**博士？📺👨‍🏫

*   使用`beforeunload`事件来警告用户他们将要关闭你的页面，但是只有在重要的时候
*   一个`Promise`物体中的`Set`可以用来控制`beforeunload`
*   …或许你可以使用`sendBeacon`而不是提示！

如果你想了解更多，请继续阅读！⬇️📖

* * *

## 卸载基础

如果你想提示或警告你的用户他们将要关闭你的页面，你需要添加代码来设置`beforeunload`事件上的`.returnValue`:

```
window.addEventListener('beforeunload', (event) => {
  event.returnValue = `Are you sure you want to leave?`;
}); 
```

Enter fullscreen mode Exit fullscreen mode

有两件事要记住。

1.  大多数现代浏览器(Chrome 51+，Safari 9.1+等)会忽略你说的话，只给出一个通用的信息。这可以防止网页作者写出过分的信息，例如，“关闭这个标签会使你的电脑爆炸！💣".

2.  不保证会显示提示。就像[在网上播放音频](https://www.youtube.com/watch?v=9JnoKSwh1HU)一样，如果用户没有与你的页面互动，浏览器可以忽略你的请求。作为一个用户，想象打开和关闭一个你从来没有切换到的标签——背景标签应该*而不是*能够提示你它正在关闭。

## 随意显示

您可以添加一个简单的条件，通过检查事件处理程序中的某些内容来控制是否提示用户。这是相当基本的良好实践，如果您只是想警告用户他们还没有完成一个静态表单的填写，这可能会很有效。例如:

```
let formChanged = false;
myForm.addEventListener('change', () => formChanged = true);
window.addEventListener('beforeunload', (event) => {
  if (formChanged) {
    event.returnValue = 'You have unfinished changes!';
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果你的网页或网络应用相当复杂，这种检查可能会变得难以操作。当然，您可以添加越来越多的检查，但是一个好的抽象层可以帮助您并有其他好处——我将在后面谈到。👷‍♀️

* * *

## 承诺

因此，让我们围绕`Promise`对象构建一个抽象层，它代表工作的*未来结果*——就像来自网络的响应`fetch()`。

人们被教导承诺的传统方式是将它们视为单一操作，可能需要几个步骤——从服务器获取、更新 DOM、保存到数据库。然而，通过共享`Promise`，其他代码可以利用它来观察它何时完成。

### 待定工作

这里有一个跟踪未完成工作的例子。通过用一个`Promise`调用`addToPendingWork`——例如，从`fetch()`返回的一个——我们将控制是否警告用户他们将要卸载你的页面。

```
const pendingOps = new Set();

window.addEventListener('beforeunload', (event) => {
  if (pendingOps.size) {
    event.returnValue = 'There is pending work. Sure you want to leave?';
  }
});

function addToPendingWork(promise) {
  pendingOps.add(promise);
  const cleanup = () => pendingOps.delete(promise);
  promise.then(cleanup).catch(cleanup);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，你需要做的就是打电话给`addToPendingWork(p)`要一个承诺，也许是一个从`fetch()`返回的承诺。这对于网络操作来说很有效——他们自然会返回一个`Promise`,因为你在网页控制范围之外的东西上被屏蔽了。

### 忙碌的微调器

正如我在上面的视频中谈到的📺🔝，我们也可以使用未决工作集来控制繁忙的微调器。这是对`addToPendingWork`函数的一个非常简单的扩展:

```
function addToPendingWork(promise) {
  busyspinner.hidden = false;
  pendingOps.add(promise);

  const cleanup = () => {
    pendingOps.delete(promise);
    busyspinner.hidden = (pendingOps.size === 0);
  };
  promise.then(cleanup).catch(cleanup);
} 
```

Enter fullscreen mode Exit fullscreen mode

当添加新的`Promise`时，我们显示微调器(通过将其`.hidden`属性设置为`false`)。当任何一个承诺完成时，我们会检测是否不再有工作——如果`pendingOps`为空，则隐藏微调器。

[![Simple checkboxes and busy spinner](../Images/de0bc59e7f8eba8b54d35f9e79c8ed24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kU4I3huo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lk82obf0jlovbxee6zfc.gif)

我不是 UX 设计师，所以构建一个视觉上吸引人的忙碌的旋转器是留给读者的 UX 练习！👩‍🎨

### 待定表单

但是对于上面的例子——一个挂起的表单呢？这里有两个选择。您可以添加第二个*处理程序* `beforeunload`，就像本文顶部的那个:一个简单的布尔检查。

但是如果你对使用`Promise`机制感兴趣，甚至对一个表单感兴趣，我们可以承诺用户完成表单的*概念*。这个想法有两个部分。

首先，我们创建自己的`Promise`并在用户开始输入某些内容时将它添加到我们的待定工作 it 中:

```
// create a Promise and send it when the user starts typing
let resolvePendingFormPromise;
const pendingFormPromise =
    new Promise((resolve) => resolvePendingFormPromise = resolve);

// when the user types in the form, add the promise to pending work
myForm.addEventListener('change', () => addToPendingWork(pendingFormPromise)); 
```

Enter fullscreen mode Exit fullscreen mode

然后，当提交表单时(可能通过`fetch()`)，我们可以用网络操作的结果来“解决”最初的承诺:

```
myForm.addEventListener('submit', (event) => {
  event.preventDefault();  // submitting via fetch()

  const p = window.fetch('/submit', ...).then((r) => r.json());
  p.then((out) => { /* update the page with JSON output */ });

  // resolve our "pending work" when the fetch() is done
  resolvePendingFormPromise(p);
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后[瞧啊](https://google.com/search?q=define%3Avoil%C3%A0)！如果用户已经在表单中输入了内容，我们可以像以前一样使用相同的挂起工作习语来阻止页面卸载。当然，你忙碌的纺织工可能不应该说“储蓄！”。

* * *

## 发送信标

我已经谈了很多悬而未决的工作，听 a `fetch()`完成承诺。但是，正如我在视频中提到的，你可能根本不需要提示用户。

如果你发出一个没有任何有用结果的网络请求——你只是把它发送给一个服务器，你不关心结果——你可以使用现代浏览器调用`navigator.sendBeacon()`。它字面上有*没有*返回值，所以你*不能*等待它的结果(无论那是成功*还是*失败)。但是，它被明确设计为即使在页面关闭后也能运行。

```
window.addEventListener('beforeunload', () => {
  const data = 'page-closed';
  navigator.sendBeacon('/analytics', data);
}); 
```

Enter fullscreen mode Exit fullscreen mode

当然，您不必只在`beforeunload`中使用`sendBeacon`——您可以在页面关闭之前使用*，然后您可能根本不必实现`beforeunload`处理程序，因为您没有等待的`Promise`！*

### 聚合填充

如果你的浏览器不支持`sendBeacon`，几乎完全等同于通过`fetch()`发送一个 POST 请求。您可以使用如下代码进行回退:

```
if (!navigator.sendBeacon) {
  navigator.sendBeacon = (url, data) =>
      window.fetch(url, {method: 'POST', body: data, credentials: 'include'}).
} 
```

Enter fullscreen mode Exit fullscreen mode

⚠️:如果你试图在`beforeunload`中发出网络请求，这甚至是值得的，因为一些浏览器仍然会成功通过`fetch()`，尽管规范并不保证这一点。

### 表情符号举例

当你在[表情输入器](https://emojityper.com)上选择表情符号时，我用`navigator.sendBeacon()`来记录，生成‘趋势’📈列表和表情符号流行度🔥。它很适合放在那里，因为我不需要等待响应，请求甚至可以在您关闭页面时发出。😂👍

* * *

我希望你喜欢这一集的[标准](https://www.youtube.com/playlist?list=PLNYkxOF6rcIB1V2i_qfRtDMcY6YZK1lkt)和略长的解释！

你有问题吗？请在下面留下评论，或者在 [Twitter](https://twitter.com/samthor) 上联系我。我也渴望听到你的建议或改进。🕵️