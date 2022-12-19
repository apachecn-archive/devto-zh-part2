# 什么是 create-react-native-app？

> 原文：<https://dev.to/onmyway133/what-is-create-react-native-app--461i>

[![](../Images/1d8a12aaf22b4b4928215c345bd91cec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IcxQPwJk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AEmiP1QGznu8G0UrB)

作为一个从 iOS 和 Android 背景过来 React Native 的人，我喜欢 React 和 Javascript 就像喜欢 Swift 和 Kotlin 一样。React Native 是一个很酷的概念，但理论上听起来不错的东西在实践中可能并不奏效。

## 另一层抽象

到目前为止，我仍然不明白为什么大公司选择 React Native 而不是 Native，因为最终我们所做的是向最终用户提供良好的体验，而不是为开发者提供简单的开发。我看到一些公司说[再见](https://medium.com/airbnb-engineering/react-native-at-airbnb-f95aa460be1c)React Native，人们说 React Native 只是 React 哲学[学一次，写在任何地方](https://ashfurrow.com/blog/airbnb-and-react-native-expectations/)，他们选择 React Native 主要是为了 React 风格，而不是为了跨平台开发。但不可否认的事实是，人们选择 React Native 主要是为了跨平台开发。写一次，部署许多地方是如此引人注目。当然，我们仍然需要为每个平台做一些调整，但是大部分代码都是重用的。

我不得不承认，我自己花在开发上的时间比钻研编译和捆绑问题的时间还少。没有免费的东西。为了在 React Native 上取得成功，我们不仅需要对 iOS 和 Android 有深刻的理解，还需要准备好解决问题。我们不能只是打开问题，祈祷和等待，希望有人能解决它。不能一味的用其他依赖，就此收工。在 React Native 中，一个微不足道的错误可能非常难以修复，我们的项目不能等。当然，大公司有足够的资源来解决问题，但是对于小团队来说，React Native 是一个需要考虑的权衡决定。

## 一个包装纸换一个包装纸

够了。我们去官方 React Native [入门指南](https://facebook.github.io/react-native/docs/getting-started.html)看看吧。推荐[创建-反应-原生-应用](https://github.com/react-community/create-react-native-app)。作为一个制作了 electron.js 应用程序并使用 [create-react-app](https://github.com/facebook/create-react-app) 的人，我马上选择 [create-react-native-app](https://github.com/react-community/create-react-native-app) ，因为我认为它在创建 react 原生应用程序方面是等价的。我知道有 react-native init，尽管它肯定有一些问题，否则人们不会介绍 [create-react-native-app](https://github.com/react-community/create-react-native-app) 。

在玩了 [create-react-native-app](https://github.com/react-community/create-react-native-app) 和 eject 之后，在仔细阅读了它的[警告](https://facebook.github.io/react-native/docs/getting-started.html#caveats)之后，我知道是时候使用 react-native init 了。最后，我只需要一个快速的方法来引导反应原生应用程序与足够的工具(巴别塔，JSX，热重装)，然而[创建反应原生应用程序](https://github.com/react-community/create-react-native-app)只是一个有限的经验，在博览会上有自以为是的服务，如博览会推送通知[docs.expo.io/versions/latest/guides/push-notifications.html](https://docs.expo.io/versions/latest/guides/push-notifications.html)和连接到谷歌云平台和 AWS。我知道每样东西都有它的用例，但在这种情况下，它不是我的工具。依赖是万恶之源，反应原生的已经很多了，我不想再要更多不必要的东西。

Expo 的概念实际上很好，因为它允许新手使用已经设置好的推荐工具快速进入 React Native，并且能够稍后拒绝。共享零食也有助于重现问题。但是 repo create-react-native-app 的名字有点[误导](https://github.com/react-community/create-react-native-app/issues/742)。

也许只是我，或者…

*   [dev . to/kylessg/ive-released-over-100-apps-in-react-native-since-2015-ask-me-any-1m9g](https://dev.to/kylessg/ive-released-over-100-apps-in-react-native-since-2015-ask-me-anything-1m9g)

    > 不，我绝不会把世博会用于一个严肃的项目。我想象在大多数项目中最终发生的事情是，他们最终不得不放弃应用程序(例如，需要一个原生模块)，这听起来非常痛苦。

*   [dev . to/gaserd/why-I-do-use-Expo-for-react-native-don-panic-1bp](https://dev.to/gaserd/why-i-do-not-use-expo-for-react-native-dont-panic-1bp)

    > 如果你用 EXPO，你就用 wrapper-wrapper。

*   [hacker noon . com/understanding-Expo-for-react-native-7bf 23054 bbcd](https://hackernoon.com/understanding-expo-for-react-native-7bf23054bbcd)

    > Expo 是快速入门 React Native 的绝佳工具。然而，它并不总是能让你到达终点。在我看来，世博团队在产品路线图方面做出了很多重大决策。但是他们的快速发展经常导致新功能的缺陷。

*   [docs.expo.io/versions/latest/introduction/why-not-expo](https://docs.expo.io/versions/latest/introduction/why-not-expo)

    > *世博会管理的 JS 和资产需要连接到谷歌云平台和 AWS*

*   [#516](https://github.com/react-community/create-react-native-app/issues/516)

*   [http://www . Albert Gao . XYZ/2018/05/30/24-tips-for-react-native-you-possible-want-to-know-to-know](http://www.albertgao.xyz/2018/05/30/24-tips-for-react-native-you-probably-want-to-know/)

    > 如果你来自网络世界，你需要知道 create-react-native-app 并不等同于 create-react-app
    > *但是如果你想要对你的项目有更多的控制权，比如调整你的 react native Android 和 iOS 项目，我强烈建议你使用官方的 react-native-cli。不过，一个简单的命令 react-native init ProjectName 就可以了。*

*   [medium . com/@ paulsc/react-native-first-impressions-Expo-vs-native-9565 CCE 44 c 92](https://medium.com/@paulsc/react-native-first-impressions-expo-vs-native-9565cce44c92)

    > *我希望我从一开始就使用“原生”,而不是在 Expo*
    > *上浪费了很多时间，也许对我来说最大的问题是，整个事情感觉像是在已经复杂的堆栈上增加了另一层间接性和复杂性*
    > *我希望 Expo 可以从 react-native 入门指南中删除，以避免混淆新来者*

*   [level up . git connected . com/how-I-burged-Expo-for-pure-react-native-fc 0375361307](https://levelup.gitconnected.com/how-i-ditched-expo-for-pure-react-native-fc0375361307)

    > Expo dev 团队在那里做了很多好事，而且他们都是免费做的，所以我要感谢他们为这个世界提供了一个更顺畅的入口。如果他们设法解决了自定义本机代码的问题，它可能会再次成为我的首选平台。

*   [https://medium . com/@ aswinmohamme/how-I-reduced-the-size-of-my-react-native-app-by-86-27be 72 BBA 640](https://medium.com/@aswinmohanme/how-i-reduced-the-size-of-my-react-native-app-by-86-27be72bba640)

    > 我喜欢世博会的一切，除了二进制文件的大小。不管你的应用程序是什么，每个二进制文件的重量都在 25 MB 左右。因此，我做的第一件事就是将我现有的 Expo 应用迁移到 React Native。

原帖[https://medium . com/Fanta geek/what-is-create-react-native-app-9 F3 BC 5 a 6 C2 a 3](https://medium.com/fantageek/what-is-create-react-native-app-9f3bc5a6c2a3)