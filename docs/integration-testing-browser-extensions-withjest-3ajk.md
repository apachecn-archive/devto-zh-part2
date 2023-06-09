# 用 Jest 集成测试浏览器扩展

> 原文：<https://dev.to/pureooze/integration-testing-browser-extensions-withjest-3ajk>

之前我写了关于[我如何成为萨卡人](https://github.com/lusakasa/saka)的维护者，这是一个开源浏览器扩展，允许用户搜索和加载打开的标签，浏览历史和书签。[我谈到了我如何想出一个对扩展](https://dev.to/pureooze/unit-testing-browser-extensions-39a5)进行单元测试的解决方案，以给我对代码变更的信心。我还提到了我遇到的集成测试问题，这使得测试依赖于浏览器 API 的组件变得困难。

今天，我很高兴地告诉大家，我已经找到了一种对扩展进行集成测试的方法，并希望在本帖中与大家分享。但是在我们深入这个特殊的兔子洞之前，让我们先讨论一下集成测试，以及为什么它对验证软件有用。

## 测试奖杯

[![The Testing Trophy](img/a3e64ff2ee0cde792158cde1b8dd6531.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--laLpuaWQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xqzp0ej2grrku8dxex96.jpg)

肯特·c·多兹写了一些他称之为“测试奖杯”的东西。如果你之前听说过[测试金字塔](https://martinfowler.com/articles/practical-test-pyramid.html)，这是一个类似的概念——它是你应该如何在应用程序中优先考虑不同类型测试的可视化。肯特邮报的标题说明了一切:

> 编写测试。不太多。主要是整合。

他为什么这么说？Kent 指出，单元测试的问题在于，它们只能证明单个单元按预期工作——它们不能证明单元可以作为一个整体一起工作。另一方面，集成测试证明了我们项目中的所有组件实际上可以像预期的那样一起工作。

## 需要进行集成测试

让我们离开软件世界，看一个真实的例子。假设我们想为浴室建一个水槽。这个水槽有 4 个部件:水龙头、面盆、排水系统和水管。由于排水管和水管是建筑自带的，我们只需要担心增加水龙头和面盆。我们去商店挑选一个我们喜欢的水龙头和脸盆。我们把它们带到现场，单独组装。我们确认水龙头和面盆都能正常工作，没有任何缺陷。最后，我们组装完整的水槽——把水龙头接到水管上，把面盆接到排水管上。在我们所有的劳动之后，我们很高兴看到我们的水槽在工作，所以我们打开水龙头，发生了什么？嗯…

[![Source: X Unit Tests, 0 Integration Tests](img/c14f2f392d3b5cd7eebb3cea65169e30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2-WBex4Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zbqnfbjkl7p67met4sus.gif)

哎呀！虽然我们确实检查了水龙头和面盆是否能正常工作，但我们忘记了检查这两者是否真的兼容。这就是为什么集成测试是有价值的——它证明了不同的组件、模块和库可以像预期的那样协同工作。

肯特·c·多兹——写作测试。不太多。主要是整合。

[Ulrika Malmgren — X 个单元测试，0 个集成测试](https://natooktesting.wordpress.com/2017/08/24/x-unit-tests-0-integration-tests/)

# 解

自从写了我的上一篇文章，我已经设法让 Jest 与 Preact 一起工作，Preact 是用来创建萨卡人的框架。Jest 是一个现代测试框架，可以在 Node 或 JSDOM 中运行。我还将使用 dom-testing-library 在我的组件上执行呈现和断言。

请记住，虽然我的特定解决方案是为 Preact 定制的，但它们仍然适用于其他框架——尤其是 React——只需针对特定于框架的库稍加修改。

这里有一个带有 Jest 设置的 Preact 扩展示例供参考:[https://github.com/pureooze/extension-testing-example](https://github.com/pureooze/extension-testing-example)

## 安装

> 如果您使用不同的框架，则不需要安装 preact 包

首先你需要安装所需的依赖项:

```
yarn add --dev babel-jest babel-plugin-transform-class-properties babel-plugin-transform-react-jsx babel-preset-env babel-preset-react jest sinon-chrome 
```

Enter fullscreen mode Exit fullscreen mode

如果使用 Preact，您还需要运行以下命令:

```
yarn add --dev preact-compat preact-render-to-string preact-test-utils preact-testing-library 
```

Enter fullscreen mode Exit fullscreen mode

注意，就像上一篇文章一样，我们将使用 sinon-chrome 来模拟所有的浏览器 API。

## 配置笑话

***仅用于预反应，不用于反应。*T3】**

安装 jest 后，您现在需要创建一个配置来告诉 Jest 如何处理解析 Preact。如果你使用另一个框架，比如 React，你不需要这样做。在项目的根目录下创建一个 jest.config.js 文件，内容如下: