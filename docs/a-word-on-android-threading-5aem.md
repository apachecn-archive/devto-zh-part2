# 关于 Android 线程的一句话

> 原文：<https://dev.to/levimoreira/a-word-on-android-threading-5aem>

几天前，我看到一个 StackOverflow 的问题:“Android 中的 main 方法在哪里？”。我对自己说，这是一个非常有趣的问题，我想我会写一些关于它和其他线程的东西。是的，我们在 Android 中看不到`main()`方法的事实与线程化有很大关系。

## 你在哪里，总管？

先从上面的问题说起。Android 中的主要方法在哪里？正如任何 java 应用程序一样，入口点是 main 方法，尽管我们看不到它，但每个应用程序都是从 main()方法开始的，就像其他 java 应用程序一样。持有它的类是 [ActivityThread](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/app/ActivityThread.java) :

ActivityThread 负责:

*   准备主打环器——这是主线程将要运行的打环器(稍后会详细介绍)。
*   为这个主活套指定一个特殊的处理器
*   启动主循环程序(通过调用其 loop()方法)。
*   一大堆其他的东西...

这基本上是说,`ActivityThread`将指定一个类来执行许多必要的方法来呈现你的应用程序的 UI 并保持它运行。

现在你可能会问自己:*好的，李维，但是你没有提到我的主要活动是如何命名的。你刚刚谈到一个负责管理 UI 的线程*

我会告诉你:*冷静，我们会到达那个*

在`ActivityThread.java`文件(这是一个很大的文件)中，有另一个类，简称为`H`,它扩展了 Handler 类，以前我们给主 Looper 指定一个特殊的 Handler 时使用过。该类在整个 ActivityThread 中被广泛使用，因为它是一个特殊的处理程序，处理我们的(或任何其他 Android 应用程序)可能的状态。
每个处理程序都有一个叫做`handleMessage`的小方法，这个特殊处理程序的方法如下:

```
public void handleMessage(Message msg) {
            if (DEBUG_MESSAGES) Slog.v(TAG, ">>> handling: " + msg.what);
            switch (msg.what) {
                case LAUNCH_ACTIVITY: {
                    ActivityClientRecord r = (ActivityClientRecord)msg.obj;
                    r.packageInfo = getPackageInfoNoCheck(
                            r.activityInfo.applicationInfo, r.compatInfo);
                    handleLaunchActivity(r, null);
                } break;
                .... 
```

Enter fullscreen mode Exit fullscreen mode

当然，这只是其中的一小部分，这是一个处理每个不同状态的巨大开关。但在这一部分，我们可以看到它在处理`LAUNCH_ACTIVITY`案例。在那里，它调用了方法`handleLaunchActivity()`，后者又调用了`performLaunchActivity()`方法。

```
 private void handleLaunchActivity(ActivityClientRecord r, Intent customIntent) {
 ...
        Activity a = performLaunchActivity(r, customIntent);
 ...
 } 
```

Enter fullscreen mode Exit fullscreen mode

后者非常重要，因为它增加了一堆我们习惯于在 Android 中简单使用的已知结构，如应用程序、上下文和意图。它还在我们的主活动中调用将调用我们亲爱的`onCreate()`方法的`mInstrumentation.callActivityOnCreate()`。

所以，总结一下，从`main()`到`onCreate()`是一条漫长的道路:

1.  执行 ActivityThread 的 main
2.  UI 线程是用它的 Looper、特殊处理程序等来设置的。
3.  特殊处理程序启动创建活动对象的过程
4.  活动对象是用相关属性创建的
5.  调用 onCreate 方法。

## 神奇词汇:处理程序、消息、消息队列、循环程序

现在，我们已经讨论了如何从`main()`到`onCreate()`，我们已经使用了一些漂亮的词，如 Handler 和 Looper，当你开发一个应用程序时，你很少在代码中看到这些(可能在这里或那里有一个 Handler，但 Looper 是非常罕见的，除非你正在处理像低级多线程这样的黑暗事物)。当我们的应用程序崩溃时，我们经常看到这些:

[![alt text](img/fa29595110dc95a484980458743797a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZMxXLMz9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.android.com/studimg/debug/android-monitor-logcat_2-2_2x.png)

这是因为它们是 android 执行多线程的基础。让我们从一些定义开始:

### 活套

[Looper](https://developer.android.com/reference/android/os/Looper) 是一个低级类，负责为一个线程执行消息循环。如果你看看这个类中 loop()方法的实现，这就变得很清楚了:

```
 for (;;) {
            Message msg = queue.next(); // might block
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个无限循环，从 MessageQueue 读取消息并执行它们。有趣的是，当我在大学的时候，我们经常把线程放在一个无限循环中。我想这是有用的，对吧？此外，我们通过线程进行循环，因为在 java 中创建线程会产生开销，如果我们重用一些线程，这种开销可以减少。线程意味着运行一次就消失了，但是有了循环，我们可以让它们存在更多的时间。

### 报文队列

一个[消息队列](https://developer.android.com/reference/android/os/MessageQueue)就是给出消息的队列。没什么好说的了。

### 消息

一个[消息](https://developer.android.com/reference/android/os/Message)是一个特殊的包装类，它包装了一些可以在线程间交换的数据。

### 可运行

一个 [Runnable](https://developer.android.com/reference/java/lang/Runnable) 简单来说就是一个你希望执行的工作(一个任务，因为没有更好的词)的包装器。

### 处理程序

一个[处理程序](https://developer.android.com/reference/android/os/Handler)处理消息(或可运行的消息)并将它们的处理调度给一个循环，也就是说，它接收消息并向/从与之相关的消息队列添加消息。一个处理程序只与一个线程和它的 MessageQueue 相关联，但是一个线程可以有(并且通常会有)多个处理程序。

```
handler.sendMessage(msg); //adds to the MessageQueue

handler.post(new Runnable() {
    @Override
public void run() {
        // this will run in the MQ thread
    }
});

public void handleMessage(Message msg) {
        // process messages from the MQ
    } 
```

Enter fullscreen mode Exit fullscreen mode

#### 那么，它们是如何连接在一起的呢？

处理消息或 Runnable(Android 中的工作单元)的处理器将它们中的一个添加到与循环和线程相关联的 MessageQueue 中。Looper 保持线程在它们之间循环，并要求处理程序执行它从 MessageQueue 中弹出的消息。

我们通常不自己创建这些类，而是使用 HandlerThread 为我们自动创建一个带有线程的 Looper。

这只是一个更深入和复杂的主题的介绍(不仅仅是在 Android 中)，但我希望它对其他人有用:)