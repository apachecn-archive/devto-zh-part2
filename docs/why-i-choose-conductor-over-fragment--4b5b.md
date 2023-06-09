# 为什么我选择导体而不是碎片？

> 原文：<https://dev.to/chingiz/why-i-choose-conductor-over-fragment--4b5b>

如果我们去 [Conductor](https://github.com/bluelinelabs/Conductor) 的知识库页面，上面写着“一个小而全功能的框架，允许构建基于视图的 Android 应用”。它真的做到了它所说的，我可以谈论太多的优点而不是缺点。

首先，它非常容易集成，可以帮助你为 Android 构建单活动应用程序。当活动第一次出现在文档中时，它被写为“用户可以做的单一的、集中的事情”。这意味着你可以在一个活动中做所有的事情，并在导体或片段的帮助下处理所有其他的事情。此外，通过单一活动，您可以管理:

```
- Navigation drawer in all the views
- Toolbar
- Low memory footprint (previous Activities with views exist in the stack, which can cause an out-of-memory exception in endless navigation applications) 
```

Enter fullscreen mode Exit fullscreen mode

## 那么是什么让你选择指挥呢

Conductor 有四个主要组件，您应该在开始之前先了解它们。

```
1\. Controller - The Controller is the View wrapper that will give you all of your lifecycle management features. Think of it as a lighter-weight and more predictable Fragment alternative with an easier to manage lifecycle.

2\. Router - A Router implements navigation and backstack handling for Controllers. Router objects are attached to Activity/containing ViewGroup pairs. Routers do not directly render or push Views to the container ViewGroup, but instead, defer this responsibility to the ControllerChangeHandler specified in a given transaction.

3\. ControllerChangeHandler - ControllerChangeHandlers are responsible for swapping the View for one Controller to the View of another. They can be useful for performing animations and transitions between Controllers. Several default ControllerChangeHandlers are included.

4\. ControllerTransaction - Transactions are used to define data about adding Controllers. RouterControllerTransactions are used to push a Controller to a Router with specified ControllerChangeHandlers, while ChildControllerTransactions are used to add child Controllers. 
```

Enter fullscreen mode Exit fullscreen mode

众所周知，当我们使用 Fragment 时，我们需要 FragmentManager 来替换它们。Conductor 帮助您编写最少的代码来替换视图。我们应该把我们所有的视图扩展到控制器，然后如果我们想改变视图，调用导体的重要部分`getRouter().pushController(YourAnotherViewWhichIsController())`就足够了。

Conductor 最好的一点是，它的控制器生命周期比 Fragment 更容易理解。

[![A lifecycle diagram of Controller](img/445583a7b986148eb736abfa19095f9c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_e5kbngN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/bluelinelabs/Conductor/raw/develop/docs/Controller%2520Lifecycle.jpg)

它的导航和 backstack 处理是最好的。更简单的喜欢:

```
// On MainActivity
@Override
    public void onBackPressed() {
        if (!router.handleBack()) {
            super.onBackPressed();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

总之，让我成为指挥的主要原因是它易于使用的结构，backstack 处理和简单的生命周期。

参考资料:
[https://github.com/bluelinelabs/Conductor](https://github.com/bluelinelabs/Conductor)
[https://www . polidea . com/blog/Introduction _ To _ Single _ Activity _ Applications/](https://www.polidea.com/blog/Introduction_To_Single_Activity_Applications/)

这是我在 dev.to 上的第一篇文章，我的母语不是英语。我会犯错。对此我很抱歉。