# 使用 Detox 清除应用程序中的有毒 bug！

> 原文：<https://dev.to/juliendemangeon/get-rid-of-toxic-bugs-on-your-apps-with-detox-l6j>

*注:此帖子最初发布于[marmelab.com](https://marmelab.com/blog/2018/06/05/get-rid-of-toxic-bugs-on-your-apps-with-detox.html)。*

在去年的 React Europe 会议期间，我发现了一个很有前途的 E2E 手机应用测试框架，叫做 T2 排毒。从那以后，我一直牢记着要试一试的意愿。最近，我为此开发了一个[示例应用](https://github.com/marmelab/beerexplorer-detox)。它让我看到了排毒罩下的东西。以下是我的反馈。

## 什么是端到端测试？

在软件开发中，我们列出了**三种不同的自动化测试**:

*   **单元测试**:顾名思义，他们单独测试功能，与代码库的其余部分隔离**。它们用于防止意外的代码更改，并确保函数做它们应该做的事情。**
*   **集成测试**(或**服务测试**)负责代码部分和 API 之间的正确连接。他们从技术角度对应用程序组件进行全面测试。
*   **端到端测试(E2E)** :它们允许在应用程序的执行环境中对应用程序进行整体测试**，就像人类可以做的那样。**

 **根据 Martin Fowler 的说法，所有这些类型的测试都可以归为一个[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)，从最慢/最贵到最快/最便宜。

[![Test Pyramid](img/aaa7a3aa3c9ccd22249e43ca8bded520.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iHr4tMss--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.cimg/blog/detox/test_pyramid.png)

在金字塔的底部，**单元测试**肯定是最常见的测试。当然，每个功能都必须测试。需要一些集成测试和一点点 E2E 测试来确保整个堆栈运行良好。

尽管 E2E 测试非常重要，但有些人有时会过度使用 E2E 测试。另一个名为[冰淇淋蛋卷](https://medium.com/@fistsOfReason/testing-is-good-pyramids-are-bad-ice-cream-cones-are-the-worst-ad94b9b2f05f)的图表也代表了这种反模式。

[![Ice Cream Cone](img/764c1670bb1ad5ff1da545e8e5baa5e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bCmYtscp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.cimg/blog/detox/icecream.png)

但是每个人都同意编写和调试 E2E 测试是一项单调乏味的任务。

## 介绍排毒

2016 年，在 [Wix](https://wix.com/) 工作的两位工程师[塔尔·科尔](https://twitter.com/koltal)和[罗特姆·米兹拉奇-梅丹](https://twitter.com/rotemmiz)首次发布了 Detox。Wix 是一个基于云的平台，允许非技术用户创建自己的网站。

[![Wix Engineering](img/a4e5cba0426b65e74901413d4bc5fd8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pWKXfkla--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.cimg/blog/detox/wixengineering.png)

Detox 将自己定义为**一个用于移动应用**的灰色盒子端到端测试自动化框架。这意味着它通过 [Selenium](https://www.seleniumhq.org/) 带来了我们已经在浏览器应用中使用的相同的上下文感知测试功能。通过这种方式，Detox 可以脱离耗时且不完整的手动[质量保证](https://en.wikipedia.org/wiki/Quality_assurance)测试流程。

与其主要竞争对手 Appium 相反，Detox 在服务器端和客户端都使用 JavaScript。尽管有这个强烈的要求，但是 Detox 允许使用 Jest、Mocha、AVA 或者任何你喜欢的 JavaScript 测试运行程序。

## 灰盒测试 vs 黑盒测试

作为一个灰盒测试框架，Detox 共享白盒和黑盒功能。让我们一起来看看这意味着什么。

[黑盒测试框架](https://en.wikipedia.org/wiki/Black-box_testing)允许接管执行上下文(浏览器、软件、移动应用等)并向其发送控制命令。

这种测试方法不允许访问应用程序的内部状态。这就是为什么需要手动检查元素的存在，以确保在转换后测试状态。

```
function* navigate() {
    yield driver.navigate().to(`http://localhost/#/login`);
    yield driver.wait(until.elementLocated(By.css('#loginform')));
} 
```

Enter fullscreen mode Exit fullscreen mode

灰盒框架正在扩展[白盒测试框架](https://en.wikipedia.org/wiki/White-box_testing)的能力。这样，除了访问执行上下文的内部状态之外，它们做的事情和黑盒框架一样。

[![Black Box vs Grey Box](img/abf35d9f9db48100962d846cfe5d47d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LPQZCTFh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.cimg/blog/detox/blackgreywhitebox.png)

访问执行上下文的内部状态允许知道应用程序何时空闲，并充分地同步操作。这就是为什么 Detox 比大多数经典的 E2E 测试框架更强大。

## 少片状剥落

如果你以前已经使用过 E2E 测试框架，你肯定会遇到一些奇怪的、随机的和意想不到的错误。这些误差因此被称为“片状误差”。当你遇到他们时，你会觉得自己像我们的老哈罗德，这并不好笑。

[![E2E Flakiness is not so funny](img/642e8fff3c8dfcf28131b384ba59e713.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xf6QxutK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.cimg/blog/detox/harold.jpg)

为了减轻这种行为，我们通常在测试套件中添加一些`sleep`(或`timeouts`)调用，以确保应用程序在恢复测试过程之前处于空闲状态。尽管这种“黑客”工作，但它导致测试变慢，没有真正解决问题，因为在一个慢的测试系统上，睡眠延迟有时是不够的。

```
function* login() {
    yield driver.findElement(this.elements.loginButton).click();
    yield driver.sleep(5000);
} 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，作为一个灰箱框架，Detox 能够**访问应用程序状态**，然后确定应用程序是否处于空闲状态。为了实现这个`idle`同步任务，Detox 依赖于两个本地灰盒驱动程序，分别名为 [EarlGrey](https://github.com/google/EarlGrey) (用于 iOS)和 [Espresso](https://developer.android.com/training/testing/ui-testing/espresso-testing.html) (用于 Android)。

[![Detox Inner Working](img/fe85dccbbe0e1831c4c5d7aecc5e4a5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T9mOacOg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.cimg/blog/detox/earlgrey_espresso.png)

因为 **Detox 运行在 JavaScript** 中，它使用基于 JSON 的协议与驱动程序通信，以调用设备上的控制命令。

还为 React-Native 应用程序开发了一个特殊的同步机制，因此 Detox 支持 React Native

## 一个具体的用例

正如在介绍中已经说过的，我已经开发了一个专门的应用程序来尝试排毒。由于我是一个啤酒爱好者，我忍不住创建了一个简单的啤酒注册应用程序，名为 [beerexplorer](https://github.com/marmelab/beerexplorer-detox) 。

[![Detox Example App](img/b6eb4ba736d1a6527e2efda6c451131f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rV3Ggo6x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.cimg/blog/detox/detox.gif)

大多数关于排毒的博客文章都用 [React-Native](https://facebook.github.io/react-native/) 构建应用程序，这一篇也不例外。尽管如此，重要的是要知道，Detox 是完全跨平台的，支持 iOS 和 Android。

## 排毒设置

我试着在我自己的安卓手机上运行 Detox。尽管我尽了最大努力，我还是没能使它工作。所以我回到了一个 iOS 模拟器。

排毒设置相对简单。它包括安装[排毒](https://www.npmjs.com/package/detox) npm 包，然后调用两个命令:`detox build`和`detox test`。

然后，Detox 使用在`package.json`中定义的现有配置来确定它应该使用的测试转轮和配置。所有可用的设备配置都存储在“detox.configurations”键下。Android 和 iOS 设备配置可以混合使用。

```
/* package.json */

{
  "name": "beerexplorer",
  "detox": {
    "test-runner": "jest",
    "runner-config": "e2e/config.json",
    "configurations": {
      "ios.sim.debug": {
        "binaryPath": "ios/build/Build/Products/Debug-iphonesimulator/beerexplorer.app",
        "build": "xcodebuild -project ios/beerexplorer.xcodeproj -scheme beerexplorer -configuration Debug -sdk iphonesimulator -derivedDataPath ios/build",
        "type": "ios.simulator",
        "name": "iPhone 7"
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我调用`detox test`命令时，在运行测试之前，Detox 会在`runner-config`配置文件中查找要执行的`setupTestFrameworkScriptFile`。我把这个文件叫做`init.js`。

```
// e2e/config.json

{
    "setupTestFrameworkScriptFile" : "./init.js"
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是测试初始化文件:

```
// e2e/init.js

const detox = require('detox');
const config = require('../package.json').detox;

beforeAll(async () => {
  await detox.init(config);
});

afterAll(async () => {
  await detox.cleanup();
}); 
```

Enter fullscreen mode Exit fullscreen mode

测试既可以在本地仿真器上运行，也可以在隐藏的仿真器上运行，甚至可以在像 [Travis](https://travis-ci.org/) 这样的远程 CI 上运行！

## 排毒用法

开箱即用，Detox 提供了一套小而强大的工具，允许控制设备，选择 UI 中的元素，并对这些元素执行操作。

排毒工具是异步的。因此你必须使用承诺，或者，就像我下面的代码，ES6 async/await。

### 装置

`device`对象允许直接控制设备，而不依赖于测试的应用程序。这里有一些来自[文档](https://github.com/wix/detox/blob/master/docs/APIRef.DeviceObjectAPI.md)的用法示例。

```
// Launch app with specific permissions
await device.launchApp({ permissions: { calendar: 'YES' } });

// Simulate "home" button click 
await device.sendToHome();

// Simulate geolocation
await device.setLocation(32.0853, 34.7818); 
```

Enter fullscreen mode Exit fullscreen mode

有些`device`功能是特定于给定平台的，比如针对 React-Native 的`device.reloadReactNative`和针对 iOS 的`device.shake`。

### 选择器/匹配器

与其他测试框架一样，Detox 提供了以不同方式匹配 UI 元素的可能性。

匹配元素最简单的(也是推荐的)方法是使用 id。遗憾的是，这种技术只在 React-Native 上可用。

```
// id declaration
<Touchable testID="BeerListItem">
...
</Touchable> 
// element selection
await element(by.id('BeerListItem')); 
```

Enter fullscreen mode Exit fullscreen mode

也可以用其他方法匹配元素，如`text`、`label`、`type`或`traits`。关于相应的[匹配器文档](https://github.com/wix/detox/blob/master/docs/APIRef.Matchers.md#byidid)的更多信息。

### 行动与期待

一旦选中，就有可能[触发动作](https://github.com/wix/detox/blob/master/docs/APIRef.ActionsOnElement.md)和[执行元素上的断言](https://github.com/wix/detox/blob/master/docs/APIRef.Expect.md)。作为一个例子，这里有一个来自“beerexplorer”项目主页的测试套件。

```
describe('home', () => {
  beforeEach(async () => {
    await device.reloadReactNative();
  });

  it('should have a list of beers', async () => {
    await expect(element(by.id('BeerList'))).toBeVisible();
  });

  it('should go to detail on beer touch', async () => {
    await element(by.id('BeerListItem')).atIndex(0).tap();
    await expect(element(by.id('DetailBackground'))).toBeVisible();
  });

  it('should show all beers', async () => {
    await waitFor(element(by.label('Lindemans Kriek'))).toExist().whileElement(by.id('BeerList')).scroll(50, 'down');
    await expect(element(by.label('Lindemans Kriek'))).toExist();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，测试非常有表现力，易于阅读。由于空闲状态同步，不需要在转换之间添加更多关于元素存在的测试。

## 结论

虽然最后对排毒感到满意，但我仍然对在 Android 上建立 E2E 测试套件的难度感到失望。由于我在移动应用程序测试方面的经验不足，我不会假装给你更准确的意见。但是我仍然认为这个框架(和它的文档)对于 Android 来说还有待改进。

除此之外，开发人员使用 Detox 的体验非常愉快。我从来没有发现自己在写测试的时候有困难。此外，模拟器中的实时预览非常强大。

然而，如果你只是在 iOS 上测试你的应用程序，那就试一试吧。除了对测试的清晰性和稳定性感到非常满意之外，您不会冒很多风险。

如果你想阅读其他作者关于这个主题的更多内容，我推荐以下内容:

*   [实用测试金字塔](https://martinfowler.com/articles/practical-test-pyramid.html)
*   [Detox:用于移动应用的灰盒端到端测试框架](https://hackernoon.com/detox-gray-box-End-To-End-testing-framework-for-mobile-apps-196ccd9564ce)
*   [在 React-Native - Jest &排毒](https://pillow.codes/testing-in-react-native-jest-detox-d7b3b79a166a)中测试
*   [灰盒 vs 黑盒](https://reqtest.com/testing-blog/grey-box-testing/)**