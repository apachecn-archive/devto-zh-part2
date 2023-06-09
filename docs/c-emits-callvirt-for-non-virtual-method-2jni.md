# C#为非虚方法发出“callvirt”

> 原文：<https://dev.to/joni2nja/c-emits-callvirt-for-non-virtual-method-2jni>

这篇文章最初出现在[媒体](https://medium.com/@joni2nja/c-emits-callvirt-for-non-virtual-method-1b3d8f58ea79)上

* * *

#### C#几乎总是发出“callvirt”给非虚方法，除了…

让我们稍微深入一下这个世界。

首先，看看下面运行在 [LINQPad](https://www.linqpad.net/) 上的超级简单的 C#代码:

[![](img/f15789cc3bfa39bc3a4d0564e8963a2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A9iu1Wil--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/342/1%2A1zooSleYEpn67IUptN_6aw.png)

我们可以从 IL Results 面板中看到生成的 IL，也显示在上面的截图中。让我们只注意突出的部分。

好的，那么`new Coba().Say()`将翻译成 IL 调用。

现在，让我们将`virtual`添加到方法中。像这样:

[![](img/f3ea65ca4d3c886bfc74e82c549b7617.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TgLB73Tb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/374/1%2A96YASoiJD4iONgbRilFTWg.png)

哦，现在 IL 生成的是`callvirt`而不是`call`，有道理。

> `callvirt`指令调用对象的后期绑定方法。也就是说，方法是基于运行时类型`obj`选择的，而不是基于方法指针中可见的编译时类。`Callvirt`可以用来调用虚方法和实例方法。

以下是官方文档:

[操作码。Callvirt 字段(系统。反射.发射)](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.callvirt?view=netframework-4.7.2)

和`call`:

> call 指令调用由随指令传递的方法描述符所指示的方法。方法描述符是一个元数据标记，它指示要调用的方法、放在堆栈上要传递给该方法的参数的数量、类型和顺序，以及要使用的调用约定。

[操作码。呼叫现场(系统。反射.发射)](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.call?view=netframework-4.7.2)

现在，让我们稍微修改一下代码，将对象保存到一个实例变量中。

[![](img/92c20c3df4d1a8fddd2418fd9ffcb435.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pWP6oqNk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/380/1%2A1cgx-3yZ-Vj4S75f8AyPuw.png)

啊哦，等等，生成的 IL 不是`call`而是`callvirt`……

嗯，其实是由[设计](https://blogs.msdn.microsoft.com/ericgu/2008/07/02/why-does-c-always-use-callvirt/)以来的。净 v1。

[为什么 C#总是使用 callvirt？](https://blogs.msdn.microsoft.com/ericgu/2008/07/02/why-does-c-always-use-callvirt/)

从那以后，C#不断发展。C#现在有了`?.`空条件操作符。让我们再次修改代码来使用这个操作符。

[![](img/38f64e953927b13c4ab90adbcaf964f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pkVobXQQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/358/1%2AsZ8DmzVppu7jmYEWnFf4Tg.png)

好的，这样编译器可以确保对象不会为空，所以它可以优化它使用`call`而不是`callvirt`。

现在，让我们添加虚拟。

[![](img/67c9d47fcf3644a7c71a28fa11566d31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jCX5EFJy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/371/1%2ADdL1KRSp0NhTwPfaJFqAVA.png)

这一次，`callvirt`正在被使用，正如预期的那样。

### 基准

标杆`callvirt` vs `call`怎么样？

我们需要确定我们真的比较了`callvirt`和`call`。让我们来比较一下上面的最后两个代码。以下是输出:

[![](img/e7798ffa4ee0176a9603049dc3195dec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HjzEazh---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzNbNdPaNo8pXuctO5cNoLg.png)

我们准备好了。

这里是基准的代码。

[![](img/8b3aae79123c8c2a06c35a209626fb84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CYsIBfuX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALMG_95Mf_-Omyss85UYEiw.png)

结果是:

[![](img/5c8e5df984ddc360d470c7f8c21230e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iG_vHvjE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Arsoyab3720GgfwUTN0Xpig.png)

结果告诉我们，性能影响是如此之小。

### 总结

C#几乎总是在调用方法时发出一个`callvirt`,即使是在非虚方法上。`callvirt`具有进行隐式空检查的优势，因此当您在空对象上调用方法时会抛出`NullReferenceException`。如果编译器“知道”对象不会为空，它可以优化使用`call`来代替。性能影响很小，我们不应该真正关心它。