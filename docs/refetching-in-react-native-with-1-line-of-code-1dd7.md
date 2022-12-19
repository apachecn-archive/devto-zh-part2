# 用一行代码在 React Native 中进行重取

> 原文：<https://dev.to/bidah/refetching-in-react-native-with-1-line-of-code-1dd7>

我来自一个 Vue.js 背景，我经历过每次你导航到另一个路径/组件时组件都被卸载的情况。因此，正如您所看到的，使用像`mounted`或`created`这样的生命周期方法是在需要时重新运行逻辑的常见模式。例如，在新闻提要中，如果您从提要条目屏幕返回到提要，并且您需要显示来自 API 的最新提要，您可能需要这样做。

所以回到 React Native，我发现自己处在一个新的场景中，组件不会在屏幕之间挂载/卸载。那怎么办呢？原来答案就在导航中。

我正在使用[反应导航](https://reactnavigation.org/)，它[使您能够发出事件](https://reactnavigation.org/docs/en/navigation-prop.html#addlistener-subscribe-to-updates-to-navigation-lifecycle)来筛选订阅它们的组件。通过该功能，您可以使用最适合自己事件。你得到`willFocus`、`didFocus`、`willBlur`和`didBlur`。

在我的特定用例中，当从上一个屏幕返回时，我需要重新提取。当用户按下后退按钮时，我需要反映这些变化，所以在渲染开始之前，我使用了`willFocus`来获取。

我想在返回主 feed 之前使用 feed item 屏幕上的`willBlur`来保存存储的获取响应，但是`willFocus`工作得很好。

言归正传，要解决 refetch 你需要的那一个 liner 是:

```
this.props.navigation.addListener( "willFocus", e => this.fetch()) 
```

Enter fullscreen mode Exit fullscreen mode

您可以在类的新属性上或在生命周期方法中调用此侦听器。

如何提取取决于你自己，但是有了 React 导航和监听器，你的提取就简单明了了了。

在其他用例中，像 Relay 的`QueryRenderer`这样的解决方案将是您所需要的。这可能涉及更复杂的场景，如需要前台/后台抓取，但现在对我来说，这就是我所需要的。希望对你也有帮助。快乐重绘。