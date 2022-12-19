# 有效利用迦太基

> 原文：<https://dev.to/himaratsu/use-carthage-effectively-4c02>

# 袁绍

Carthage 是 iOS 最流行的依赖管理工具之一。

在我文章中，我介绍了几个方便的命令来节省您的时间。

# 命令

## 迦太基更新—平台 ios

当执行 carthage update 时，carthage 从您准备的 Cartfile 中创建 Cartfile.resolved。然后，
建立框架。

在此命令中，您应该指定要在其中使用框架的平台。

迦太基更新—平台 ios

Carthage 通常创建多个平台框架(iOS、watchOS、tvOS 和 macOS)，但可能不会全部使用这些。

## Carthage bootstrap—平台 IOs—缓存—构建

当执行 carthage bootstrap 时，carthage 从 Cartfile.resolved 文件构建框架。不是从 Cartfile。

carthage bootstrap —平台 iOS—缓存—构建

—cache-builds 意味着如果您已经在 Carthage/Checkouts/
中构建了框架，就不要重新构建框架。

幸运的是，carthage 会自动检查框架是否建立在相同的 Swift 版本中。如果没有，重建它。

# 指定库

Cartfile 在多个平台之间共享。比如 iOS 和 watchOS。

基本上，命令在这里:
Carthage bootstrap—平台 iOS，watchOS

但是它有问题。在很多情况下，watchOS 不会使用 Cartfile 中定义的所有依赖项。

所以，我的建议是:

Carthage bootstrap—平台 iOS
Carthage bootstrap—平台 watchos SWXMLHash

分离命令，并将库名传递给 watchOS 的构建命令。

# 使用最新版本的 OSS

为了缩短构建时间，可以使用框架二进制文件。它是由库作者提供的，所以您可以隐式使用。

要使用它，您的项目 swift 版本需要与二进制文件的 swift 版本相同。如果您使用 Swift 4.2，请使用 Swift 4.2 中的版本构建库。

Carthage 跳过签出和构建步骤，因此非常高效。

# 重述

迦太基是伟大的工具，但建造时间是瓶颈。我介绍解决这个问题的技巧。

希望这篇文章能帮到你！

—-

我在学习英语。如果你注意到英语错误，请在评论中告诉我。