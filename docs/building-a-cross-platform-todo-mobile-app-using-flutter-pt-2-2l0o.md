# 使用 Flutter: Pt 构建跨平台 todo 手机 app。2

> 原文：<https://dev.to/runtime-revolution/building-a-cross-platform-todo-mobile-app-using-flutter-pt-2-2l0o>

<figure>

[![Photo by Randall Ruiz on Unsplash](img/3760be5a6e6f010c1dad3beabd1c51cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8iL4wB-l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Aw7Zz6iKz6BKWrUaYwYcI8Q.jpeg)

<figcaption>Photo by Randall Ruiz on Unsplash</figcaption>

</figure>

在这个系列的第一部分[(你应该完全看一看)](https://dev.to/theoneguilherme/building-a-cross-platform-todo-mobile-app-using-flutter-5e3m)，我们建立了我们的工作空间来用 Flutter 构建应用程序。在这一部分，我们将更深入地探讨如何用它来构建东西，甚至为自己创建一个非常基本的待办事项应用程序。

您可能还记得，我们正在查看每个 Dart 应用程序的入口点，即`main.dart`文件。这个文件构建了一个简单的计数器应用程序，它有一个增加变量的按钮，然后这个变量被打印在屏幕上。那是相当没用的，不是吗？让我们建造更好的东西。我们来搭建一个待办 app。

在开始之前，我认为我们应该删除我上面提到的示例应用程序中的代码，并从头开始。为此，让您的`main.dart`文件看起来像这样: