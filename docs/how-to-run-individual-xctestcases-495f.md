# 如何运行单个 XCTestCases

> 原文：<https://dev.to/higuri/how-to-run-individual-xctestcases-495f>

# 背景

你可以通过 Xcode 上的‘运行测试’(‘产品’>‘测试’或`Command+U`)轻松运行 XCTest(你的 Xcode 项目的单元测试)。如果您对您的产品代码进行了大量的更改，并且想要一次测试所有的代码，这是非常有用的，因为“运行测试”会执行您的项目中实现的所有测试用例。

但是有时您想要运行单独的测试用例，比如在修复特定方法的错误时，并且想要只运行该方法的测试用例。

## Xcode

### 编辑方案

选择“产品”>“方案”>“编辑方案”>“测试”窗格>“信息”选项卡，然后选中和取消选中测试用例名称。通过这样做，您可以指定使用“运行测试”来执行哪些测试用例。当您实现更多的测试用例时，这种方法会更加麻烦...
[![Screenshot of Edit Scheme](img/47907ad33776f7396b21ff697cfa1d6a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--rJfZ3Q8R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/42kno5agq1o13m7j3poc.png)

### 测试导航器

在 Xcode 窗口的左侧面板显示“Test Navigator ”,并按下显示在测试用例名称右侧的“PLAY”图标。
[![Screenshot of Test Navigator](img/cae00bc22f3ef8570e4bca734dc0e18a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--1ckrTa-K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j7xyajf6c957zlzh71ol.png)

### 运行电流测试

`Control + Option + Command + U`
这执行一个测试用例，你的光标在其中。

### 重新运行上次测试

`Control + Option + Command + G`
这将重新运行上一次执行的测试用例。

## xcodebuild

如果您有许多测试用例，并且想要频繁地切换执行目标，那么您可能会发现用命令行界面指定测试用例的价值。xcodebuild 是 Xcode 附带的命令行工具之一。

### 样本用法

```
> xcodebuild test \
>   -workspace MyXCWorkSpace \
>   -scheme MyAppScheme \
>   -destination "platform=iOS,name=my_iphonex" \
>   -only-testing:MyXCTargetForTest/MyTestSuite/MyTestMethod 
```

Enter fullscreen mode Exit fullscreen mode

## 备注

Xcode 版本:9.2

## 参考文献

Xcode 6 中有没有在光标下运行单电流测试功能的键盘快捷键？
[https://stackoverflow.com/q/28057643](https://stackoverflow.com/q/28057643)

从命令行运行 iOS 应用的单个 XCTest (UI，Unit)测试用例
[https://stackoverflow.com/q/35166214](https://stackoverflow.com/q/35166214)

如何将 xcodebuild 与-only-testing 和-skip-testing 标志一起使用？
[https://stackoverflow.com/q/39427263](https://stackoverflow.com/q/39427263)