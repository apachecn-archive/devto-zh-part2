# 2018 年 jQuery Vs. Javascript(通过类比解释)

> 原文：<https://dev.to/raddevon/jquery-vs-javascript-in-2018-explained-through-analogies-21fk>

## Javascript 和 jQuery 是什么？

### javascript 就像一把锤子。

### jQuery 就像柔软舒适的手柄。

[![Javascript is the hammer, jQuery is the cushioned grip](../Images/0f4d41128b72541d22716a59f69e18c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SLhqVqf2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raddevon.com/wp-content/uploads/2018/09/tools.jpg)

**Javascript 是一个类似锤子的工具**。这个工具可以让你**给你的网页添加交互性**。这相当宽泛，但这是有意的，因为 Javascript 相当宽泛。这里只是一些你可以用它做的事情的例子:

*   拉入搜索结果，并将它们放在用户正在查看的页面上**，而不必加载新页面**
*   **验证表单**中的输入，例如，确保用户输入了正确的邮政编码
*   **建立一个计算器**，它接受用户的输入，并在页面上显示结果

 **jQuery 是对该工具的一个增强,使其使用起来更加舒适，有点像锤子上的舒适把手。它专门从事 DOM 操作(即访问或更改页面上的元素)。如果想要操作 DOM，首先需要抓住一个元素。如果您想在不使用 jQuery 的情况下获取页面上的所有段落元素，您可以这样做:

```
document.querySelectorAll('p'); 
```

Enter fullscreen mode Exit fullscreen mode

`p`只是一个选择器，就像你在 CSS 中使用的一样。将它作为一个字符串传递给`document.querySelectorAll()`以获取 Javascript 中的所有元素。要在 jQuery 中做同样的事情，您应该这样做:

```
$('p'); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想隐藏段落，你可以用普通的 Javascript 来实现(也就是说，Javascript 上面没有任何像 jQuery 这样的库):

```
document.querySelectorAll('p').style.display = 'none'; 
```

Enter fullscreen mode Exit fullscreen mode

在 jQuery 中，它看起来像这样:

```
$('p').hide(); 
```

Enter fullscreen mode Exit fullscreen mode

如果你在敲钉子的时候握住锤子的把手，感觉会比握住一个光秃秃的把手或者试图握住锤子的头来敲钉子好得多。尽管如此，**你还在用锤子**。同样，当你使用 jQuery，**时，你仍然在使用 Javascript** 。你只是使用 jQuery 的*接口*让 Javascript 做一些事情，而不是直接用 Javascript *做*。

## 该学哪个？

### Javascript 就像 iPhone 的邮件 app。

### jQuery 就像 Siri 对 Mail.app 的接口。

[![Javascript is like Mail.app, jQuery is like using Siri to send email](../Images/753094d11ee4e23b2f52816dd2f978d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DSTNrViH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raddevon.com/wp-content/uploads/2018/09/siri-vs-mail.jpg)

由于 jQuery 只是 Javascript 上的一个很好的接口，试图让它们互相竞争没有太大意义。通常，试图比较它们的人想知道他们是应该学习普通的 Javascript 还是直接跳到 jQuery。虽然 jQuery 很容易学习，并且可以让您快速操作 DOM，但是 Javascript 有更高级的功能。这里还有一个类比来说明。

Siri 让发送电子邮件变得非常简单，甚至不用看或触摸你的手机。如果你需要快速发出一封简单的电子邮件，这很好，但是如果你需要抄送给某人呢？如果需要发送附件怎么办？在这些情况下，**你需要启动邮件应用**。

同样，jQuery 在 Javascript 的某些方面提供了一个友好的界面，比如选择 DOM 元素的[、](https://api.jquery.com/jQuery/)[改变内联样式的](https://api.jquery.com/css/)和绑定事件的。如果你想做 jQuery 提供的接口之外的事情，你需要掌握常规的 Javascript 技能。如果你只知道 jQuery，那将是一个遗憾，因为，尽管 jQuery 可以做很多事情，但 Javascript 仍然拥有 jQuery 所没有的广泛能力。举个例子，如果你想[使用浏览器的存储](https://raddevon.com/articles/using-localstorage-api/)(即 LocalStorage)，你可以用 Javascript，但是 jQuery 没有给我们提供访问它的方法。

既然 jQuery 不做 Javascript 做不到的事情，**我们为什么不干脆一起跳过 jQuery**？

## 2018 年我到底该不该学 jQuery？

### Javascript 就像智能手机摄像头。

### jQuery 就像一个傻瓜相机。

[![Javascript is a smartphone camera, jQuery is a point-and-shoot](../Images/0067dc65867dd18384ea6c88a1e631cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fDEzzT_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raddevon.com/wp-content/uploads/2018/09/camera-vs-smartphone.jpg)

把自己放在一个喜欢拍照的人的头顶——不是一个*专业*摄影师，甚至不是一个业余摄影*爱好者*，而是**一个喜欢给家人和朋友拍照的普通人**。就在几年前，这种人会买一个傻瓜相机来拍照，因为智能手机相机并没有那么好。

智能手机相机继续稳步发展，直到普通摄影师不再有理由购买单独的傻瓜相机。智能手机摄像头足够近了，额外的小工具的成本和麻烦不再合理。

这类似于 Javascript 和 jQuery 的弧线。当 jQuery 出现时，它在 Javascript 之上为一些难以实现的事情提供了良好的体验。例如，Javascript 没有本地的 [`map`方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)。`map`方法允许您通过对现有数组中的每个值运行相同的函数，从现有数组创建一个新数组。该函数返回的值成为`map`创建的新数组中的一个值。在 Javascript 拥有 array `map`方法之前，jQuery 就有一个方法可以做到这一点。你当然可以通过遍历一个现有的数组，用 Javascript 手动创建自己的数组，但是这远不如`map`方法方便。

Javascript 后来添加了自己的`map`方法，使得 jQuery `map`方法变得不那么必要。jQuery 方法比原生方法做得更多:除了从现有数组构建新数组，它还可以从对象构建数组。即便如此，`map`方法的关键功能是 Javascript now **的一部分，让 jQuery 看起来更像老式的傻瓜相机**。

随着 Javascript 的进步，浏览器也在进步。jQuery 的核心——DOM 操纵——过去在不同的浏览器中工作方式略有不同。用普通的 Javascript 编写能在所有浏览器上工作的 DOM 操作代码是一件痛苦的事情。jQuery 消除了所有这些不一致，这样您就可以编写甚至可以在最特殊的浏览器上工作的代码。这让网络开发人员在 Internet Explorer 全盛时期变得更加理智。

如今，现代浏览器都支持相同的 DOM 操作 API，所以这个让 jQuery 变得简单的优势已经基本消失了。编写 jQuery 还是好一点，但是不像以前编写普通的 Javascript 代码那样痛苦了。

另一方面，作为一名 web 开发人员，你并不总是从零开始新的项目。jQuery 非常普及，所以**你可能会发现自己在从事使用它的项目**。如果你对它不熟悉，你必须为那个项目提高技能。甚至当您开始一个新项目时，您可能正在与一个默认使用 jQuery 的团队一起工作。也许你可以说服他们[他们不需要它](http://youmightnotneedjquery.com/)(如果事实上是这样的话)，但其他时候你必须排队。

回到类比:如果一个陌生人递给你一个傻瓜相机，并请你拍照，你会希望能够为他们拍照，即使你知道你自己永远不会拥有一个傻瓜相机。

## 结局如何

*   jQuery 是建立在 Javascript 之上的一个很酷的工具，它使 Javascript 的特定部分变得更容易。它不是 Javascript 的替代品，也没有囊括该语言的所有特性。
*   随着 Javascript 和浏览器对 jQuery 的支持日趋成熟，jQuery 变得非常有用，但是随着新语言特性和更符合标准的浏览器(即行为更可预测的浏览器)的出现，**jQuery 的许多优势都消失了**。
*   了解 jQuery 仍然是有用的，因为你会发现许多代码库仍然在使用它，并且团队在每个项目中都使用它。
*   jQuery 是一项很好的技能，可以添加到您的 web 开发工具包中，但是您应该将大部分精力放在学习普通的 Javascript 上。**