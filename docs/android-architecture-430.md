# Android 架构

> 原文：<https://dev.to/tedhagos/android-architecture-430>

Android 最明显的部分，至少对开发者来说，是它的操作系统。操作系统是一个复杂的东西，但在大多数情况下，它是用户和硬件之间的桥梁。这太简单了，但对我们的目的来说已经足够了。我所说的“用户”并不是指最终用户或个人。我的意思是它是一个应用程序，一段程序员创造的代码，就像文字处理器或电子邮件客户端。

以电子邮件应用程序为例，当你在按键上键入每个字符时，该应用程序需要与硬件通信，以便消息到达你的屏幕和硬盘，并最终通过你的网络发送到云端。这是一个比我在这里描述的更复杂的过程，但这是基本的想法。最简单地说，一个操作系统做三件事

1.  代表应用程序管理硬件
2.  为网络、安全和内存管理等应用提供服务。
3.  管理应用程序的执行，这是允许我们几乎同时运行多个应用程序的部分。

图 1 显示了 Android 平台的逻辑架构。

[![Androidarchitecture](../Images/4b3bd5bf3efa89747498b69f2eeaa898.png "androidarchitecture.png")](https://res.cloudinary.com/practicaldev/image/fetch/s--5fQulu1g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-hFcqoGfxzPg/W4AXA2ozdZI/AAAAAAAABQg/bU9JK9IIKYY2lPAhhURn7rnujqdpuYsDACHMYCw/androidarchitecture.png%3Fimgmax%3D1600) 

**图 1。安卓的逻辑架构**

图的最底层是 Linux 内核。它主要负责与硬件接口。它还负责各种服务，如内存管理和进程执行。

Linux 是一个非常稳定的操作系统，并且无处不在，你可以发现这个操作系统被广泛使用。它可以在小到手表，大到服务器农场的东西上运行。Android 内部有一个嵌入式 Linux，处理硬件接口和一些其他内核功能。

在 Linux 内核之上是低级库，如 SQLite、OpenGL 等。这些不是 Linux 内核的一部分，但仍然是低级的，因此，大部分是用 C/C++编写的。在同一层面上，你会发现 android 运行时(android 类库+ dalvik 虚拟机)，这是 Android 应用程序运行的地方。

接下来是应用程序框架层。它位于底层库和 android 运行时之上，因为它需要这两者。这是我们作为应用程序开发人员将与之交互的层，因为它包含了我们编写应用程序所需的所有库。

最后，最上面是应用层。这是我们所有应用程序的所在地，包括我们编写的和预构建的应用程序。应该指出的是，与我们将要编写的应用程序相比，设备自带的预构建应用程序没有任何特权。如果不喜欢手机的邮箱 app，可以自己写，自己更换。安卓就是这样民主的。

[![](../Images/b1670d60ba171a04a73fb387496157eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BS2EzixO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/WorkingDev/%257E4/ClZGsk9-CHE)