# 在 iOS 上使用 CircleCI 2.0

> 原文：<https://dev.to/onmyway133/using-circleci-20-43n8>

原帖[https://github.com/onmyway133/blog/issues/158](https://github.com/onmyway133/blog/issues/158)

我们已经在许多开源项目中使用 CircleCI。从去年年底 2017 年开始，2.0 版本开始出来，我们认为现在是和 [Swift 4.1](https://swift.org/blog/swift-4-1-released/) 和 [Xcode 9.3](https://developer.apple.com/news/?id=01242018d) 一起尝试的好时机

[版本 2.0](https://circleci.com/docs/2.0/) 的问题是它太强大了，有很多很酷的新功能，比如作业和工作流，但这需要去文档了解如何[迁移](https://circleci.com/docs/2.0/migrating-from-1-2/)配置文件。

### 创造`config.yml`

第一件事是在文件夹`.circleci`中创建一个新的`config.yml`

> 将现有的 circle.yml 文件复制到名为。circleci 位于项目存储库的根位置。

接下来是声明版本和作业

> 将版本 2 添加到。circleci/config.yml 文件。

### 检查`xcodebuild`

对于简单的情况，我们只是使用`xcodebuild`来构建和测试项目，所以最好在本地尝试，以避免大量的尝试提交来触发 CircleCI。可以看看这个 PR[https://github.com/hyperoslo/Cheers/pull/20](https://github.com/hyperoslo/Cheers/pull/20)

之前我们 1.0 版本的配置文件看起来是这样的

```
- set -o pipefail && xcodebuild -project Cheers.xcodeproj -scheme "Cheers-iOS" -sdk iphonesimulator -destination 'platform=iOS Simulator,name=iPhone 8,OS=11.0' -enableCodeCoverage YES test 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该把`pipefail`放在`shell`里面，按照[https://github . com/circle ci-Public/circle ci-demo-IOs/blob/master/。circleci/config.yml](https://github.com/CircleCI-Public/circleci-demo-ios/blob/master/.circleci/config.yml)

> shell:/bin/bash-log in-o pipe fail

在由于`destination`参数
导致多次失败后，现在是实际的尝试`xcodebuild`

```
xcodebuild: error: Unable to find a destination matching the provided destination specifier:
        { platform:iOS Simulator, OS:11.3 }

    Missing required device specifier option.
    The device type “iOS Simulator” requires that either “name” or “id” be specified.
    Please supply either “name” or “id”. 
```

Enter fullscreen mode Exit fullscreen mode

```
xcodebuild: error: option 'Destination' requires at least one parameter of the form 'key=value' 
```

Enter fullscreen mode Exit fullscreen mode

我发现这很有效，在与你的`xcodeproj`
相同的文件夹中运行它

```
xcodebuild -project Cheers.xcodeproj -scheme "Cheers-iOS" -sdk iphonesimulator -destination "platform=iOS Simulator,OS=11.3,name=iPhone X" -enableCodeCoverage YES test 
```

Enter fullscreen mode Exit fullscreen mode

### 添加工作流

2.0 版本引入了[工作流](https://circleci.com/docs/2.0/workflows/)，帮助组织工作

> 工作流是一组用于定义作业集合及其运行顺序的规则。工作流使用一组简单的配置密钥支持复杂的作业编排，帮助您更快地解决故障。

对于我们的简单用例，我们添加了这个工作流

```
workflows:
  version: 2
  build-and-test:
    jobs:
      - build-and-test 
```

Enter fullscreen mode Exit fullscreen mode