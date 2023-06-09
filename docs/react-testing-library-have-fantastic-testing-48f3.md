# 反应-测试-库有奇妙的测试

> 原文：<https://dev.to/yazanaabed/react-testing-library-have-fantastic-testing-48f3>

我不知道什么是测试，也不知道为什么人们要检查他们的实现。所以，我决定多了解一下，并且忙着为 [*肯特 c .多兹*](https://medium.com/u/db72389e89d8) *测试课程。*

*查看我的另一篇关于测试的文章，以及我所知道的关于测试的更多信息* [*“让 JavaScript 测试为你工作”*](https://medium.com/yazanaabed/what-i-know-about-testing-in-javascript-6657d2188c3a) *。*

<figure>[![](img/809fccbec329a0c1d63a0d8f95ff2883.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BL1Mg4X_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ANZHHyLwNW_0dS3HE) 

<figcaption>照片由[辛尼斯](https://unsplash.com/@siniz?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

你可以在 twitter 上关注我，或者在我的网站 yaabed.com 上查看我的最新文章。另外，我在 blog.yaabed.com 媒体的[有我的出版物。](https://medium.com/yazanaabed)

库是包含你每天都要做的事情的东西，比如 jQuery 包含了一个高效处理 DOM 元素的实现。

测试在编程阶段是很酷的事情，我不知道为什么人们讨厌它。它让你确信你的代码不会在一段时间后崩溃，特别是当应用程序每天都在变大，有新人加入你的团队时。

> 你的测试必须让你的代码更好。——[*肯特 c .多兹*](https://medium.com/u/db72389e89d8) [*工场*](https://frontendmasters.com/courses/testing-react/) *。*

那么，什么是反应测试库呢？为什么肯特·c·多兹为我们建造了它？让我们从定义什么是 react-testing-library 以及如何使用它开始。

React-testing-library 是一个很小的库，它改变了你处理 DOM 元素的方式，我的意思是，这个库让你能够使用最终结果来搜索东西，比如用户在浏览器中看到的，用户看到的是文本，而不是 div。

当您搜索文本时，这将使您确信您不是在测试组件的实现细节。例如，如果您的应用程序有一个标题或导航栏，当用户登录您的标题组件时，需要将文本从 login 更改为 username，react-testing-library 使我们变得很容易。

100%测试覆盖率的好处是什么，你的应用程序的用户甚至不能登录？对我来说，我没有机会和测试他们代码的团队一起工作。我希望这将是很快，我学习测试与我自己和观看有关这一主题的课程。

检查这个例子，当你改变测试元素的选择器时，事情是如何发生的。

[![](img/bfe81bde0c6b8acaa74109d5c95c90fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QAkHs1wI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuV-Ndde361wFQNQEI8813g.png)

[![](img/a6dcf182fc330250b62f44b344cd399e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HSLLOm-A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6Ozt07wyOekoI2_hjvosXQ.png)

如果由于错误或者由于你的团队成员之一的要求，把类选择器从“登录按钮”改成了别的什么，这将会中断你的测试，并且每次选择器改变时都需要改变测试。

为了解决这个问题，我们可以使用 react-testing-library 通过在组件内部搜索登录文本来解决选择器问题。

[![](img/fe3e5779b516ee7b1c3a2a6b4d12a41e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--35eIOxC4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aizrhxvkl5CdidapdOuV7Hw.png)

对我来说，第二个例子更容易理解为你正在搜索一个登录按钮，你想点击它。在这个例子中，您将模拟用户行为。

入口点是来自 react-testing-library 的 render 方法，它返回各种实用程序，您可以在测试中使用它。

你也可以使用它的多个入口点，查看这个库的 [Github](https://github.com/kentcdodds/react-testing-library) 帐户以获得更多信息。

上例 render 方法返回 getByText util，还有越来越多:

*   [container](https://github.com/kentcdodds/react-testing-library/blob/fd2df8d18652786a95bce34741180137f9d2cef2/README.md#container) :返回一个常规的 DOM 元素，用来包含被渲染的组件。
*   [getByLabelText](https://github.com/kentcdodds/react-testing-library/blob/fd2df8d18652786a95bce34741180137f9d2cef2/README.md#getbylabeltexttext-textmatch-options-selector-string---htmlelement) :这将用于获取一个表单元素，它的标签是输入文本。
*   [getByTestId](https://github.com/kentcdodds/react-testing-library/blob/fd2df8d18652786a95bce34741180137f9d2cef2/README.md#getbytestidtext-textmatch-htmlelement) :这将通过元素的 data-testid 属性获取元素。

另一个实用工具是 [wait](https://github.com/kentcdodds/react-testing-library/blob/fd2df8d18652786a95bce34741180137f9d2cef2/README.md#wait) 方法，它允许你等待一段时间，例如一个 API 调用。我建议你自己试一试，这太棒了。

比如，如果我的登录按钮触发了一个登录 API 来确保这是正确的用户，那么这个更改将会破坏我们的测试，因为 getByText 将会通过一个异常来表明它没有找到元素。因此，为了解决这个问题，我们将使用 react-testing-library 中的 wait 方法，它将一直等到注销文本出现在页面上。

但是，对于 API 调用，请使用一种叫做 mock with jest 的东西，我在我的另一篇文章中介绍了它。

此外，不要忘记在每次测试之前清除测试，您可以开玩笑地使用 abeforeEach 方法，也可以使用 react-testing-library util，称为每次测试后清除。你只需要这样从库中导入:导入“react-testing-library/clean up-after-each”；

[![](img/af1be9bde5046b6ff05fc8944970c668.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4Vvmr_YJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ac26CycmIpP8vIAZMMZfyzA.png)

[![](img/40b8826778ffec4c033021473cc5e2db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--65LHFViP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AeWcRFeoK0ZocwdUliKkrPQ.png)

测试是必不可少的，不要让它成为应用程序开发阶段的次要项目。这将使您确信您的应用程序在每个版本中都可以正常工作。

在我目前的团队中，我们使用 AngularJS 构建东西，我们没有信心可以删除项目或更改类名，即使我们不能删除一个我们确信这个文件在任何地方都没有使用的文件。我对任何新团队的建议是将测试作为他们团队的标准。

#### 结论

不要害怕使用这个库，试一试，阅读更多关于它的内容。我认为使用 jest 测试 react 应用程序是一个优势。我一直听到人们害怕这些东西，并说这会增加复杂性。然而，我现在与一个大团队一起工作，他们不考虑长期的东西，当你的应用程序显著进行时，这将出错，过一会儿这将降低我们前端项目的信心，这每天都给人一种痛苦的感觉。

最后，令人难以置信的是，人们每天都在努力帮助其他人简化编码工作。我希望我能很快成为他们中的一员。感谢每一个为社区做出新贡献的人。

检查我在 codesandbox 上创建的代码:

[https://medium . com/media/7 bfe 9 b 585703 DC 21 e 58 ca 4049 D1 edbc 3/href](https://medium.com/media/7bfe9b585703dc21e58ca4049d1edbc3/href)