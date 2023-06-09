# 在 10 行以下的 Android 中快速执行微小的异步任务

> 原文：<https://dev.to/wajahatkarim/executing-tiny-asynchronous-tasks-quickly-in-android-under-10-lines-3hne>

#### 不使用任何框架如 RxJava 或管理线程

> 这篇文章是我今天学习的系列的[的一部分，最初发布在我的](https://medium.com/p/65c64e1dcb6) [TIL Github 库](https://github.com/wajahatkarim3/Today-I-Learned/)和我在 wajahatkarim.com[的网站](http://wajahatkarim.com)

今天，当我在做一个项目时，我遇到了一个情况，我不得不在 Android 的后台执行一个非常小的操作。我有很多想法，比如 AsyncTask 或 Thread，或者使用 RxJava 这样强大的框架，或者使用 JobDispatcher 或 WorkManager 等新的 API。你可以在[阿里·穆扎法](https://medium.com/u/27c3b88ddc65)的[这篇好文章](https://android.jlelse.eu/8-ways-to-do-asynchronous-processing-in-android-and-counting-f634dc6fae4e)中读到这些选项。

但是我最关心的是我的要求非常简单，而且很小。我想以经度和纬度的形式从位置获取地址。我想在后台显示进度条之类的东西。你可以看看下面的 GIF 图片，这是我做的结果。

[![](img/6abbd3e4b6a52f181cec9ad276f284ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nvq5J8MY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/320/1%2AeqYVg-vCFgF7ul2QHuWffg.gif) 

<figcaption>我正在做的 app 的一部分。</figcaption>

我在 Utilities 类中有一个简单的方法，该方法对 LatLng 对象执行地理编码，如果没有有效的地址，则以字符串或 null 返回地址。

[![](img/9dc4637fded9ee42b096905f435cd2ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--it2W6l_P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARWwY_xabWOzzpPIQe9rkZQ.png)

我设置了谷歌地图。OnCameraIdleListener 在 GoogleMap 对象上拖动地图后获取中心标记的位置，并在那里调用上面的 getAddressFromLocation()方法来获取地址，如下所示。

[![](img/adf8495f67bd1a5adad5b2cb0bbd7d8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZBw18DJQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APjn5wxm3w3wAykzCRa8Lbw.png)

这是工作，但这有一个小问题。如果地址在地理编码中需要时间，那么进度条会卡住，屏幕会显示所有 UI。这是因为长时间操作是在主线程上进行的。根据 Android 的要求，它应该发生在 Worker 或任何其他线程上，这样 UI 和主线程就不会被阻塞。

现在，我不得不把这一行调用 getAddressFromLocation 的代码放到另一个线程或者别的什么线程中，以确保主线程没有被阻塞。在一个简单的谷歌搜索中，我找到了大量类似于本文中[的方法。我想要更简单的东西。](https://android.jlelse.eu/8-ways-to-do-asynchronous-processing-in-android-and-counting-f634dc6fae4e)

我喜欢 AsyncTask 的方式，但我不想创建另一个扩展 AsyncTask 的类。因此，当我深入研究时，我发现有一个静态方法 AysncTask.execute()可以在工作线程中执行小操作，而无需创建任何额外的类。

所以，在加上我上面的代码后变成了这样:

[![](img/ab4d0c22f9fc0bc2c0506f16d868c5a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NYNqGJlv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxBRn1oYUo_T4xsF2LaokkA.png)

如您所见，使用这个简单的 AsyncTask.execute()方法使事情变得非常简单。这里还有一点需要注意的是，我们不能执行 UI 操作，比如在 TextView 上设置文本或者在这个 Worker 线程中隐藏 ProgressBar。这些只能在主/UI 线程中执行。所以，这就是我在线程中调用 runOnUiThread()方法的原因。

编译和运行应用程序后，结果是这样的。

[![](img/6abbd3e4b6a52f181cec9ad276f284ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nvq5J8MY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/320/1%2AeqYVg-vCFgF7ul2QHuWffg.gif)

**Wajahat Karim** 毕业于 NUST，是一名经验丰富的移动开发者，一名活跃的开源贡献者，也是两本书[学习 Android 意图](https://www.amazon.com/Learning-Android-Intents-Muhammad-Usama/dp/1783289635)和[用 Unity 掌握 Android 游戏开发](https://www.amazon.com/Mastering-Android-Game-Development-Unity/dp/1783550775/)的合著者。在业余时间，他喜欢花时间和家人在一起，做编码实验，喜欢写很多东西(主要是在博客和媒体上)，并且是开源的热情贡献者。2018 年 6 月，他的一个库在 [Github 趋势](https://github.com/trending)上成为第一名。他的库在 Github 上有大约 2000 颗星星，并被全球各地的开发者用于各种应用。在 Twitter 和 [Medium](https://medium.com/@wajahatkarim3) 上关注他，获得更多关于他的写作、Android 和开源工作的更新。

[Wajahat Karim (@WajahatKarim) | Twitter](https://twitter.com/WajahatKarim)

此外，如果你有任何想让他回答的问题，可以通过他的网站联系他，地址是[wajahatkarim.com](http://wajahatkarim.com)，主题栏是亲爱的瓦贾哈特。

* * *