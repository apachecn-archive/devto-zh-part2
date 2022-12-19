# 作为一名 iOS 开发人员，我在 10 个月后学到了 4 件事

> 原文：<https://dev.to/eleazar0425/4-things-ive-learned-after-10-months-as-an-ios-developer-3gka>

几年前，我开始了使用 android 环境和 java 进行原生移动开发的旅程。然后，由于我工作的公司的要求，我发现自己有机会成为一名 iOS 开发人员，从那时起，10 个月过去了。这是一个没有终点的漫长旅程，今天我将告诉你这次美好经历教会我的 4 件事。下面我要介绍的只是一些我在飞行中学习到的技巧，也是我在日常生活中应用的技巧。希望你觉得有用，开始吧！

# 1- MVC 又称海量视图控制器

苹果的 MVC 从长远来看是行不通的。你的 viewcontroller 迟早会做很多它不应该做的事情，这将导致一个有 600 行代码的类很难理解。最好在苹果提供的传统 MVC 之上使用一些设计模式。有很多选择:毒蛇，MVVM，MVP 等等。这将取决于你和你的项目的具体需求。前段时间我用 swift 写了一篇介绍 MVVM 的文章，请看一下:

[swift/IOs 中的 MVVM 模式样本](https://dev.to/eleazar0425/mvvm-pattern-sample-in-swiftios--58aj)

# 2-避免非可选选项！

毫无疑问，可选功能是最强大的 Swift 功能之一，但开发人员并不总是以适合用例的方式使用它。我注意到使用实际上不是期权的期权是很常见的。我的意思是，当我们的程序 100%需要一个属性时，它最终真的是“可选的”吗？让我们看一个例子。假设您有一个基本的 UIViewController，它使用一个填充 tableview 的服务。要使此 viewcontroller 正常工作，您必须已经分配了“service”属性和“tableView”属性。