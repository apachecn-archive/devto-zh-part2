# 设置 Android 工具

> 原文：<https://dev.to/adammc331/setting-up-android-tooling-4oea>

这篇文章将引导你开始使用 Android Studio。它的目的应该是开始使用您的工具，并确保您可以运行默认的应用程序。作为一名学生，在开始开发之前，您可以使用它来确保您的工具工作正常。作为导师，您可以在研讨会之前将此邮件发送给学生，以便做好准备。

*免责声明:*本文的第一次迭代是在已经安装了 Android SDK 的苹果 MacBook 上编写的。如果你遇到任何新的步骤，或者发现一些特定于 Windows 的东西，请在评论中告诉我，我可以相应地更新帖子。随着信息的到来，我会把它添加到相关的地方。

让我们开始吧。

# 下载 Android Studio

你可以在这里下载最新版本的 Android Studio:[https://developer.android.com/studio/](https://developer.android.com/studio/)

一旦您点击下载按钮，安装程序将出现在您的下载，就像任何其他程序。一旦完成，您就可以像对任何其他程序一样遵循安装向导了——所以我选择不在这里写这个。如果你在这个步骤中遇到任何问题，请在评论中告诉我！

### Linux 下载

在评论中指出，在 Linux 上安装之后，您必须遵循一些额外的步骤。

1.  提取以任何方式下载的档案。
2.  如果通过终端提取，结果文件夹将在`android-studio`中，如果通过右键单击提取，结果文件夹将在`android-studio-ide-xxx`中。
3.  将文件夹移动到首选位置。这可能是`/user/local`也可能是`/opt`。
4.  要安装，输入 directy `/opt/android-studio/bin`(或在步骤 3 中移动它的任何地方)并运行`./studio.sh`。这将从下一个会话启动设置屏幕。
5.  当设置向导完成后，转到`Configure > Create Desktop Entry`可以像其他应用程序一样启动 Android Studio，而不是通过终端。

你可以在这里找到一个关于 Linux 安装的视频。

# 设置 Android Studio

第一次打开 Android Studio 时，需要遵循一些步骤。

首先，我们需要通过 Android Studio 设置向导。

[![](../Images/bcb83d938ea685d18d9924aa54645c0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aVWMyFnV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szqz5jzbmv2rnmbpudxp.png)

接下来，我们选择安装类型。让我们保持简单的标准安装。

[![](../Images/6a569e2ac68b5c33249868f10cfec893.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fmbd09Az--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a8armloa8bkqn222ubl7.png)

然后，我们要选择一个主题。我偏 Darcula 和其他黑暗编辑，但这个决定完全取决于你。

[![](../Images/4e0ecdce3e7aa515b2d935c8b500f8db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ndexX7JT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d6lqznkocgwnyeg3f2uf.png)

最后，我们验证所有设置，然后单击 finish。

[![](../Images/c430fa99d1e5aba7bbbe9401851ea986.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8iogv8U5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/32fnz9dtw6lsqcy3agf6.png)

这将需要一段时间来完成设置，然后你会看到 Android Studio 登录页面。

[![](../Images/06d40c872714c7411759d7b6398304f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3LjeBUQI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g060q45gxqs44rl22fcz.png)

# 创建您的第一个项目

这一节不会带你去构建一个应用程序。然而，它将向您展示如何创建一个可以在设备上运行的基本应用程序，以确保工具正常工作。

一旦您点击了我们上面看到的登录页面，我们就可以单击按钮来创建一个新项目。从那里，我们可以命名我们的项目，给它一个包名，并选择我们是否需要任何额外的语言支持。默认是 Java，但是我们也可以选择用 Kotlin 构建一个应用程序。

[![](../Images/9d18a894ce9d113061f879202bc68343.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xolWUgWw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/94zdp073o1193x2jclkq.png)

接下来，我们需要确定应用程序的最低 API 版本。这定义了我们的应用程序可以运行的 Android 的最早版本。社区在这一点上分歧很大，但是为了测试我们的工具，你可以选择你想要的。

[![](../Images/166aa25d8697cca94c23b98a0202d677.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--59CNCkGX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ozu9hxkcnh7yaastenc1.png)

现在该选模板了。这个模板将用于您创建的活动，并且可以填充样板文件，例如导航抽屉、登录活动或其他内容。这里，为了简单起见，我们只选择空活动。

[![](../Images/35c788d7959a4362b126ee3c616ff527.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xV2T7Zs9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hinly1heyz4yl7tio0c8.png)

命名您的活动可能与您的项目相关。你应该给它起一个反映它在做什么的名字。然而，由于这仅仅是关于启动和运行，您可以保留默认值。

[![](../Images/5a842de3990a61547c31e020e907726d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YD27Z7Fa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j0ag8nmjjd1bmzoocho3.png)

点击之后，Android Studio 会在后台做一个 gradle build，很快就会打开你的项目。

[![](../Images/fdaeae62de2b9479162a97c6f3b30560.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S9ncMP1h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d93a4enwtzvyteove6jp.png)

# 仿真器设置

创建了一个项目后，下一步是确保您可以运行它。本节将展示如何使用 Android 手机模拟器来实现这一点。有关如何使用物理设备实现这一点的信息，请参见下一节。

让我们从打开 AVD 管理器(Android 虚拟设备)开始。

[![](../Images/52d64ed8b121f721eb7159167d1ed5b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BhaBM6k2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qeiapuk73snwyvwi8y2t.png)

您将看到一个登录页面，解释您没有设置任何设备，并提示您添加一个。

[![](../Images/fc0e5806295a871679199831f72c0f64.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ziTrt_FM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mym3taq3euplms5bzwhg.png)

在这个菜单中，您将选择一种要模拟的电话类型。

[![](../Images/8e2ded27383e7ecf3f385fa440bcb8d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RyNb3xCs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7q7ori8zthgrqmeizbnd.png)

接下来，我们需要为我们的手机选择一个操作系统。您可能需要首先下载适当的 SDK。

[![](../Images/3dadd61a5050747fcc4ad4aaa69c0fe8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ChHe6kEd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fael82utbcofkzvrzrdz.png)

您可以自定义模拟器的名称和启动配置，但是让我们继续使用默认值。

[![](../Images/3b206470fac077eb8f887d67133c86cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fpJBOnCS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4sh3tu2t9emp2ewv9v9k.png)

现在，我们可以看到我们的设备列表。我们可以使用小运行图标来启动我们的模拟器。

[![](../Images/1ea6c819265b8eff4b33732f77795f62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lKM4_g5b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q16c18muiu38qr0xwyts.png)

[![](../Images/524128dee3394b823617fe24208d4c46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QeX554LU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lik0ywxvr379cgd2629p.png)

接下来，我们可以返回 Android Studio，找到项目的运行图标。

[![](../Images/f9f931182fd0f4ae3ba5d4d851af0759.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xVR3k3Iv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ak5ysxwzdyvzczuo4u1q.png)

点击此按钮将显示已连接设备的列表。选择我们的模拟器，然后我们应该看到默认的应用程序运行。

[![](../Images/bb8135e8589b463decc477eb5eca8be4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UhmA0141--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/16bcuw0ptagv8wjn9w1g.png)

[![](../Images/ccf6d608c1351016140a52f29d7ee625.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D7kfKTOD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/txf88ivcnha0aznpm5wj.png)

恭喜你。现在，您已经准备好开始学习 Android 开发，或者在您的第一个研讨会中继续学习。如开头所述，如果你在这个过程中发现任何问题，请在评论中告诉我。

# 设置物理设备

为了在物理设备上运行 Android 应用程序，您必须首先打开 USB 调试。如果您以前从未这样做过，请遵循以下步骤:

1.  进入设置。
2.  选择“系统”。
3.  选择“关于手机”。
4.  向下滚动到“内部版本号”。
5.  轻按“内部版本号”7 次。
6.  回到“系统”，你会看到“开发者选项”。
7.  选择“开发者选项”，找到打开“USB 调试”的开关

一旦你这样做了，如果你从 Android Studio 返回运行你的应用程序，你应该看到你的物理设备出现在模拟器旁边:

[![](../Images/90871599c43cd5f1e3a23cd38b4d3d55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t9l4DvxD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9jjzjaahyk33uokm0dlt.png)

恭喜你。现在，您已经准备好开始学习 Android 开发，或者在您的第一个研讨会中继续学习。如开头所述，如果你在这个过程中发现任何问题，请在评论中告诉我。