# 安卓插孔

> 原文：<https://dev.to/tedhagos/android-jank-1kii>

下面的代码用于触发 **ANR** (Android 无响应)错误

*清单 1。耗时任务*

```
class MainActivity : AppCompatActivity() {

 override fun onCreate(savedInstanceState: Bundle?) {
   super.onCreate(savedInstanceState)
   setContentView(R.layout.activity\_main)

   button.setOnClickListener {
     killSomeTime()
   }
 }

 private fun killSomeTime() {
   for (i in 1..20) {
     textView.text = i.toString()
     println("i:$i")
     Thread.sleep(2000)
   }
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

但现在，不再是了。它仍然是愚蠢和缓慢的，但它不会引出 ANR 错误。从 Android 4.1(果冻豆)开始，谷歌推出了“黄油项目”。它不会抛出 ANR，而是会降低帧速率。在 Project Butter 的基线上，一个应用程序的运行速度应该是 60FPS。如果你试图“在主线程上做太多的工作”，运行时将开始降低帧率。

上面的示例代码不会产生 ANR 错误，但是如果您运行它，您会注意到以下情况:

1.  你期望**文本视图**每 2 秒刷新一次，以显示 *i* 的当前值。不会的。帧会被删除，所以你看不到任何用户界面活动
2.  但是你会看到 *i* 的值，因为它在 **Logcat** 窗口中每 2 秒更新一次。这是因为 **println** 没有使用 **UI 线程**，所以不会受到丢帧的影响
3.  您可能会看到来自运行时的**编排器**的类似这样的消息

```
07-31 15:51:29.646 13403-13403/net.workingdev.ch15scratchasynctask 
I/Choreographer: Skipped 2402 frames! The application may be doing too 
much work on its main thread 
```

Enter fullscreen mode Exit fullscreen mode

清单 1 中的**kill some**函数代表了会降低 UI 速度的编程任务— *jank* 。一般来说，这些事情如下

1.  从网络上读取内容，例如下载图像、下载网页等。
2.  从存储器中读取大文件
3.  任何复杂的计算

为了避免 *jank* ，确保你没有在 UI Thead 中做这些事情。在后台线程中做*的事情。在 Android 中有很多方法可以做到这一点，其中之一就是使用 Java 中的**线程**类。清单 2 展示了如何做到这一点。

* *清单 2。使用线程来避免邱建。注释代码清单。*** 

```
import android.os.AsyncTask
import android.support.v7.app.AppCompatActivity
import android.os.Bundle
import kotlinx.android.synthetic.main.activity\_main.\*

class MainActivity : AppCompatActivity() {

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity\_main)

    button.setOnClickListener {
      Thread(Runnable { // (1)
        killSomeTime()  // (2)
      }).start()        // (3)
    }
  }

  private fun killSomeTime() {
    for (i in 1..20) {
      runOnUiThread(Runnable{ // (4)
        textView.text = i.toString()
      })
      println("i:$i")
      Thread.sleep(2000)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**(1)** 创建后台线程，需要创建一个 Runnable 类型(thread)的实例，**启动**它。**线程**构造函数采用 Runnable 类型并执行 **run** 方法中的任何内容。我在这行中使用了一个对象表达式来创建一个 Runnable 类型的实例，而没有创建一个名为子类的*——有点像 Java 的匿名类*

**(2)** 我们现在在 Runnable 的 **run** 方法里面。Runnable 类型是一个 **SAM** 类型(单一抽象方法)，所以你不必显式地写 **run** 方法，但是我们在它里面。现在我们不再在 UI 线程中，而是在后台线程|

**(3)** 别忘了在线程对象上调用**开始**

后台线程的限制之一是它**不能**做任何修改 UI 的事情。任何 UI 修改代码都必须从创建 UI 的原始线程运行——也就是 UI 线程。如果你需要从后台线程改变用户界面(就像这样)，你可以调用 **Activity** 类的 **runOnUiThread** 方法。它采用一个 Runnable 类型(再次)，你可以把所有的 UI 修改代码放在这个 Runnable 类型的 **run** 方法上

[![](../Images/718192f0986734efc71075b977b15273.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n6ltkEdn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/WorkingDev/%257E4/ZRJYu-8XNVk)