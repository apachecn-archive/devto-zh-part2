# 努力安装反应-自然-线性-渐变

> 原文：<https://dev.to/acro5piano/struggle-to-install-react-native-linear-gradient--2omm>

我不是 iOS 工程师，所以安装 react-native 库真的很难。

我终于成功安装了 react-native-linear-gradient，所以写这篇文章来纪念我的奋斗。

# 什么是反应-自然-线性-梯度(rnlg)

一个很棒的库，可以在 React Native 中创建渐变组件。

该 api 是清晰的，易于使用。

[https://github . com/react-native-community/react-native-linear-gradient](https://github.com/react-native-community/react-native-linear-gradient)

但是它的安装过程真的很复杂。每次 Xcode 都会产生很多错误。

# TL；博士；医生

不要使用椰子

而是手动安装。

看起来很明显是用椰子。如果您是 web 工程师，通常会使用诸如 npm、yarn、composer、rubygem、pip 等包管理系统。

但是在用 Xcode 项目进行 React Native 时，这种常识不起作用。如果你使用 CocoaPods，你会在每次构建、获取包、`pod install`、`react-native run-ios`时面临链接器错误。

我不知道这个问题是因为 coaPods 还是 ReactNative 还是`react-native link`还是 Xcode。

(有一个名为`Carthage`的 CocoaPods 替代品，所以我总有一天会尝试一下。)

让我们来看看我是如何努力安装 rnlg 的。

# 第一轮

首先我用 CocoaPods 安装了 rnlg。它的自述说我们应该做:

```
yarn add react-native-linear-gradient
react-native link 
```

哦，这很简单。只需两个命令，我们就可以添加一个库。太好了。

我满怀希望地敲击着命令和`open MyProject.xcworkspace`。

然后我就面临了这个错误:

```
Apple Match O Linker error
ld: Framework not found: BVLinearGradient 
```

# 第二轮

这可能是因为 Xcode 无法识别第三方框架。我尝试了很多东西:

*   添加`Framework search paths`
*   在`Podfile`
    *   `pod React, :path => '../node_modules/react-native/React'`
    *   `pod yoga, :path => '../node_modules/react-native/ReactCommon/yoga'`
*   干净构建
*   再次删除`Pods`和`pod install`
*   删除派生数据
*   删除`use_frameworks!`标志
*   添加 GCC 设置

但是我不能解决问题。

这一轮我在 google 上搜了很多，发现了很多问题。

*   [https://github.com/facebook/react-native/issues/17556](https://github.com/facebook/react-native/issues/17556)
*   [https://stack overflow . com/questions/31226185/are-all-this-cocoapod-files-possible-to-red-after-installing](https://stackoverflow.com/questions/31226185/are-all-these-cocoapod-files-supposed-to-be-red-after-installing)
*   [https://github . com/react-community/react-native-maps/issues/371](https://github.com/react-community/react-native-maps/issues/371)

React Native 比较新，发展速度很高。因此，半年前的解决方案在今天并不可靠。

# 第三轮

在这一点上，我注意到椰子可能会引起问题。

CocoaPods 真是个好人。它解决依赖性，下载源代码和 stati 二进制文件，然后将库与 Xcode 项目链接。

但是它有时不能设置正确的设置。

另外`react-native link`自动链接 node_modules 和 Xcode，但有时路径不正确。

所以这一轮我决定不用 CocoaPods 装 rnlg。

我又看了一遍 README。它包含手动安装的步骤。

我做了这个程序，得到了这个错误:

```
Duplicate interface definition for class 'RCTView' 
```

多亏了这个问题，我解决了它。[https://github.com/shoutem/ui/issues/134](https://github.com/shoutem/ui/issues/134)

只需更换

```
#import "RCTView.h" 
```

用

```
#import <React/RCTView.h> 
```

解决了问题，一切顺利。

安装包后手动修改导入路径很麻烦，所以我写了一个脚本来做这件事:

`bin/post_install_hooks.sh`

```
#!/bin/bash

find ./node_modules/react-native-linear-gradient/BVLinearGradient/ -type f \
    | xargs perl -i -pe 's/"(RCT.+\.h)"/<React\/\1>/' 
```

# 结论

没有太多了解就使用库是不稳定的。总有一天它会破坏你的代码。