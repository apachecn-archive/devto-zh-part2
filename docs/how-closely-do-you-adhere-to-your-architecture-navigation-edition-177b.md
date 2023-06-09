# 你有多坚持你的架构:导航版

> 原文：<https://dev.to/autonomousapps/how-closely-do-you-adhere-to-your-architecture-navigation-edition-177b>

*(注:我说的不是谷歌 I/O 上公布的新[导航控制器](https://android.jlelse.eu/navigation-controller-an-android-storyboard-4dd7229ca821)，如果你觉得我应该，欢迎在评论里告诉我！)*

正如我在[这篇文章](https://dev.to/autonomousapps/rewriting-chesscoms-android-app-4aij)中提到的，Chess.com 正在从头开始重写它的 Android 应用。作为这个过程的一部分，我们已经花了很多时间来思考我们想要的架构，以及我们希望各种屏幕如何相互关联。

在当前的生产应用程序中，我们基本上遵循“一个`Activity`，多个`Fragment`的方法，嵌入到传统的泥球(或者意大利面条，如果你喜欢的话)架构中。在过去的几年里，我们在“重构”(一个被过度使用的词，如果有的话)的标题下试验了各种架构模式。我们已经使用了至少三种不同版本的 MVP(都是手工制作的)，但最近我们最终决定采用一种 MVVM，大量使用 [Android 架构组件](https://developer.android.com/topic/libraries/architecture/)，特定的视图模型和 LiveData。它们真的真的很棒——而且是可单元测试的！如果你使用[的应用](https://play.google.com/store/apps/details?id=com.chess)，你可能会有兴趣了解即将到来的版本(3.7，即将推出测试版)将会有几个新功能，都是用视图模型、LiveData 甚至分页库编写的:竞技场比赛、新闻和成就。

我们对视图模型和朋友的经验使我们决定我们的“v4”应用程序的架构将严重依赖于他们。一个特别值得关注的领域是导航。在目前的 prod 应用程序中，基本上任何屏幕都可以导航到任何其他屏幕，混乱是真实的。

# v4 如何处理导航

我们已经将导航作为一个顶级关注点，并开始编写抽象来管理它。我们有一个`Router`类，它实现了许多接口(`HomeRouter`、`LoginRouter`等)，这些接口清楚地定义了每个屏幕如何与其他屏幕绑定。由于 Dagger 的一些巧妙工作，`Router`类的方法可以被命名为类似`goToWelcome()`的东西，并且没有参数，路由器可以从那里处理一切。例如:

```
@ActivityScoped
class Router<T : BaseActivity> @Inject constructor(
    private val activity: T
) : SplashRouter, LoginRouter, OnboardingRouter, HomeRouter {

  override fun goToWelcome() {
    // startActivityAndFinish() is an extension function
    activity.startActivityAndFinish(
        OnboardingActivity.getStartIntent(activity),
        activity.crossfadeAnimation()
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们被注入了一个限定了作用域的活动，让我们可以正确处理这个类中的所有事情，还可以防止意外的内存泄漏。

这一切都很好，但它最终把我带到了这篇文章的主题:我们要走多远？

# ‘后退’和‘向上’导航呢？

目前，我们让框架处理这两种形式的导航。如果用户按下后退按钮，就会调用`super.onBackPressed()`(有一些小警告)。我们还让框架通过在清单中设置父活动并在动作栏上启用向上导航来处理向上导航。

但是...我们能做得更多吗？显然这是一个精神分裂的系统。在某些情况下，我们手动将活动(和一些片段)连接在一起，而在其他情况下，框架会“自动”处理。也许下面的会更好:

```
override fun onBackPressed() {
  viewModel.onBackPressed()
  // no call to super!
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们可以捕获“向上”导航事件，并手动处理它们。

你怎么想呢?