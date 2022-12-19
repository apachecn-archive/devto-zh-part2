# React Native 中的导航

> 原文：<https://dev.to/brightdevs/navigation-in-react-native-2530>

在创建 React 原生应用程序时，屏幕之间的导航可能是您最先关注的问题之一。一旦你开始，你可能会惊讶，没有内置的，功能齐全的导航，将在两个平台上工作。虽然 [React Native 推荐](https://facebook.github.io/react-native/docs/navigation.html)使用 [React 导航](https://reactnavigation.org)，但是你会很快发现这个库完全基于 JavaScript，它并没有直接使用 iOS 和 Android 上的原生导航 API。正如[另一个库所说的](https://github.com/wix/react-native-navigation#quick-links)，如果你试图交付一个与最好的本地应用不相上下的用户体验，你就不能在基于 JS 的组件上妥协，试图伪造真实的东西。

[![image](../Images/263a434ddb9f90797d26b07c8bf5c933.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G0RD_fU5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a307onm7jm7p6d351qry.jpeg)

## 反应导航

[React 导航](https://github.com/react-navigation/react-navigation)由 React 原生社区维护，目前是最受欢迎的 React 原生导航库。它完全是用 JavaScript 编写的，并没有使用原生 API，而是重新创建了它们的一些子集。做出这一选择是为了让用户无需学习 iOS 或 Android 导航逻辑就可以定制导航体验的任何部分。因为 React 导航的大部分逻辑在 JavaScript 中运行，而不是在本机中运行，所以通常对阻塞 JavaScript 线程的担心开始起作用。正如库[自称的](https://reactnavigation.org/docs/pitch.html)，如果你需要确切的平台行为，你最好使用另一个包装平台 API 的库。

当你完成[配置](https://reactnavigation.org/docs/getting-started.html)(这纯粹是基于 JS 的，所以没有平台特定的配置和链接)，你将了解到你需要做的就是创建一个 [`StackNavigator`](https://reactnavigation.org/docs/hello-react-navigation.html#creating-a-stacknavigator) ，它为你的应用程序提供了一种在屏幕之间转换和管理导航历史的方式。这在概念上类似于 web 浏览器处理导航状态的方式，一个关键的区别是`StackNavigator`提供了类似于 Android 和 iOS 上的手势和动画。

你可以在这个博览会小吃上找到一个简单的 React 导航的例子。

React Navigation 具有现成的 Redux 和深度链接支持，加上精心编写的文档和振动社区，使其成为在应用程序中实现导航的合理选择。

#### 问题

如果你是一个移动开发者而不是 web 开发者，迟早你会在离开屏幕之前或之后寻找一些回调，类似于`viewWillAppear(_:)`和其他`UIViewController`的方法。虽然你可以在根屏幕上使用`onNavigateStateChange`并比较之前和当前的状态来推断哪个屏幕被隐藏了，但这和你在`UIViewController`中发现的完全不同。

## 反应本地路由器

[React 本地路由器](https://github.com/aksonov/react-native-router-flux)基于 React 导航，但提供不同的 API 与之交互。它允许您在一个中心位置定义场景转换，而不必传递导航器对象，并且可以在代码中的任何位置轻松访问。

最新的 [beta 版本- 4](https://github.com/aksonov/react-native-router-flux) ，除了其他变化之外，引入了抽屉支持和 Mob-X 驱动的导航状态机，将导航逻辑与表示层分离。

## 通过 Wix 反应原生导航

另一个流行的导航库是 Wix 开发的 [React Native Navigation](https://github.com/wix/react-native-navigation) 。它最大的优势是 100%的原生平台导航被具有开箱即用 Redux 支持的跨平台接口所覆盖。

你需要为 [iOS](https://wix.github.io/react-native-navigation/#/installation-ios) 和 [Android](https://wix.github.io/react-native-navigation/#/installation-android) 分别配置这个包，其中包括链接 iOS 库、更新 iOS 头搜索路径、在 Android `MainActivity`中扩展`SplashActivity`而不是`ReactActivity`以及文档中详细描述的几个附加步骤。完成后，你只需要[注册你的应用程序的所有屏幕](https://wix.github.io/react-native-navigation/#/usage)并启动应用程序。瞧啊。

在 React Native Navigation 中，你可以使用 [API，非常类似于你在原生平台上找到的 API(它在下面调用原生 API)，所以你可以`push`你的屏幕，或者使用`showModal`呈现它，然后`pop`它，或者使用`dismissModal`关闭它。它还内置了对菜单抽屉、深度链接和灯箱(提醒)的支持。](https://wix.github.io/react-native-navigation/#/screen-api)

要使用屏幕导航事件和深层链接，你只需使用`Navigator`的回调`onNavigatorEvent` :

```
onNavigatorEvent(event: NavigatorEvent) {
    if (event.id === 'willAppear') {
        // do some fancy stuff
    } else if (event.type === 'DeepLink') {
        const parts = event.link.split('/')
        if (parts[0] === 'your-deep-link') {
          // navigate
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### v2 -工作进行中

React Native Navigation 有几个问题在其当前架构中无法解决。它们源于一个问题，即您无法指定您希望在哪个屏幕上进行操作，默认情况下，该操作源于您当前屏幕。为了解决这个问题，在 v2 中，每个屏幕都接收它的`containerId`作为一个道具，每当你想从那个屏幕做出一个动作，你就把它传递给函数。

版本 2 是用测试驱动开发(TDD)编写的，这使得接受拉请求变得非常容易——如果测试通过，你的拉请求就会被接受。可以查看 [v2 路线图](https://github.com/wix/react-native-navigation/tree/v2#v2-roadmap)跟踪开发进度。

## 替代品

[![image](../Images/29b5350ba7b2b61ade3d5a3b124d9df5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZtDCJz-D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yqu0rtob4n1wr9b0rrbg.jpeg)

如果你的目标只是 iOS，你可以考虑使用内置的 [NavigatorIOS](https://facebook.github.io/react-native/docs/navigation.html#navigatorios) ，它是原生 [`UINavigationController`](https://developer.apple.com/documentation/uikit/uinavigationcontroller) 组件的包装器。但是，如果您计划在将来使用任何其他平台，您应该从一开始就考虑使用任何其他解决方案。

另一种选择是[原生导航](http://airbnb.io/native-navigation)，这是 Airbnb 开发的一个项目，Airbnb 是最受欢迎的 React 原生适配器之一。原生导航目前处于测试阶段，其作者不鼓励该库的生产使用，直到它达到稳定版本，你可以按照路线图到 1.0 [这里](http://airbnb.io/native-navigation/docs/roadmap.html)。Aribnb 在将这个库与内部导航基础设施分离时面临的最大挑战是实现高水平的可扩展性，这需要他们进行大量的代码重构和测试。这个包还没有深度链接支持，文档也不完整，所以你应该考虑在一个严肃的项目中使用其他的库。

## 我选择的武器

我个人觉得 React 原生应用**中使用 100%平台原生导航的可能性是最有价值的**，这也是我选择 [React 原生导航](https://github.com/wix/react-native-navigation)的一个原因。虽然我给了 [React 导航](https://github.com/react-navigation/react-navigation)一个机会，但我错过了`UIViewController`像`viewWillDisappear(_:)`或`viewDidAppear(_:)`的方法，以及在屏幕间导航时真正的本土感觉。然而，如果您更喜欢纯 JavaScript 库，并且不那么拘泥于原生平台的感觉，那么您至少有两种选择可以尝试。

无论您选择哪个选项，您都需要意识到这样一个事实，即那些只是第三方库，它们可能不适合您的场景，或者它们可能包含 bug。我们不得不分支和改变 React 本机导航三次，以避免一些问题，即使这些问题已经在主存储库中得到解决，您可能会遇到一些新的问题。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Mateusz Klimczak,《推动事物前进》@光明的发明

[推特](https://twitter.com/Klimczak_M)，[邮件](//mateusz.klimczak@brightinventions.pl)