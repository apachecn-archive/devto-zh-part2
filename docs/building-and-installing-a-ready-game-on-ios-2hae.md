# 在 iOS 上构建和安装一个现成的游戏

> 原文：<https://dev.to/booyaa/building-and-installing-a-ready-game-on-ios-2hae>

*[图片](https://unsplash.com/photos/8yLcHIClVto) by 你好我是 Unsplash 上的 Nik(裁剪)*

我一直在玩 [Ready](https://www.getready.io/) ，这是一个可爱且易于使用的工具，可以让你创建视频游戏。Ready 旨在以有趣的方式教授孩子们编程的基础知识，如果你看过 [Scratch](https://scratch.mit.edu/) ，你就会知道我在说什么。

Ready 是使用 Unity 制作的，它还可以输出你的作品，并在 Unity 上制作。很自然，一旦你可以在 Unity 中构建它们，为什么不继续把它们部署到 iOS 设备上呢？

我应该指出，如果你安装了 Ready 应用程序，你可以在 iOS 设备上播放你自己的作品。所以这只是在你对定制游戏行为感兴趣的情况下，或者你想知道如何为 iOS 构建你的 Unity 游戏。

这篇博文假设你已经在同一台电脑上安装了 Ready，Unity 和 Xcode。

我将使用个人/私人使用 iPhone 开发者许可证(免费)，但如果你有一个付费的 iPhone 开发者许可证，说明是相同的。我用过 Xcode 9.4.1 (9F2000)

Unity 有一个免费层，只要你的新游戏利润不超过 10，000 美元，你就不需要购买许可证😉！我用过 Unity 2018.1.0f2，我还在 m iOS 设备上安装了[Unity Remote 5](https://itunes.apple.com/us/app/unity-remote-5/id871767552)以便在我们将游戏导出到 Xcode 之前进行测试。

## 准备好游戏并导出

[https://www.youtube.com/embed/GoFUHUvkFnM](https://www.youtube.com/embed/GoFUHUvkFnM)

正如你所看到的，我已经做了一个非常基本的有拖拽行为的动画精灵。这个游戏是为风景模式设计的。稍后知道这一点很重要。

在 Ready 中，点击齿轮图标(游戏设置)，然后点击`Export to Unity3D`按钮。

*   创建一个新文件夹，并选择它作为导出目标。

## 在 Unity 中可玩

1.  打开 Unity >点击`Open`按钮(在`My Account`链接旁边)并点击进入你新导出的游戏文件夹，然后点击`Open`按钮。你想打开项目，在那里你可以看到`Assets`和`ProjectSettings`文件夹。
2.  在[项目的](https://docs.unity3d.com/Manual/ProjectView.html)窗口中。
    1.  选择`Assets`文件夹。
    2.  双击`ReadyPackage`资产解压数据。
3.  在[导入 Unity 包](https://docs.unity3d.com/Manual/AssetPackages.html#ImportingPackages)对话框中，点击`Import`按钮。这可能需要一段时间。
4.  回到项目窗口。
    1.  选择`Scenes`文件夹。
    2.  双击`PlayerScene`资产进行加载。
5.  在 Unity 菜单栏中，点击`Ready` > `Recreate Project`。
6.  点击`Play`图标来测试构建。
7.  点击`Play`图标停止播放。

## 可在 Unity 遥控器上玩

1.  在 Unity 菜单栏中点击`File` > `Build Settings…`。
2.  点击`Add Open Scenes`按钮。
3.  选择`iOS`作为您的平台。
4.  点击`Switch Platform`。这可能需要一段时间。
5.  关闭弹出窗口
6.  将您的 iOS 设备连接到 Mac。
7.  在 Unity 菜单栏中点击`Edit` > `Project Settings` > `Editor`
8.  在`Unity Remote`部分，从下拉列表中选择您连接的`Device`
9.  在您的 iOS 设备上启动 **Unity Remote 5**
10.  点击`Play`图标，在 iOS 设备上测试构建。
11.  点击`Play`图标停止播放。

你会注意到游戏在纵向模式下看起来有点倾斜，如果你把你的 iOS 设备倾斜到横向模式，你会看到游戏纠正了它的缩放。

## 可在 iOS 上播放

1.  在 Unity 菜单栏中点击`File` > `Build Settings…`。
2.  选择`iOS`平台。
3.  点击`Player Settings`按钮。
    1.  设置您的公司名称。
    2.  设置图标(从资源中选择)。
    3.  将`Default Orientation`设置为`Landscape Right`或`Landscape Left`。
4.  点击`Build`按钮。
5.  选择目标文件夹并点击`Save`。这需要一段时间
6.  在`Finder`中，转到目标文件夹并打开`Unity-iPhone.xcodeproj`，这将启动 Xcode。
7.  在选择器的`Project navigator`(最左边的图标【文件夹】)中，选择`Unity-iPhone`

[![Xcode Project Navigator](../Images/79004e7bccb2a8f13bbe9693ed9371c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TzBdXEIJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/ready-001-xcode-project-navigator.png)

1.  在`Identity`部分，更新`Bundle Identifier`字段。重要提示:这必须是唯一的。
2.  在`Signing`部分
    1.  检查`Automatically manage signing`
    2.  从下拉列表中选择您的`Team`
3.  单击此图标修复任何警告

[![Xcode warnings icon](../Images/78472213227cb3b88fa9d69be460ddb3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oEK3zce6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/ready-002-warnings.png)

1.  确保您的设备已连接并已从下拉列表中选择

[![Xcode device selector](../Images/32ad3d9f099b48de5f374ff8ec5f03d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JDGXpa6Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/ready-003-device.png)

1.  解锁你的 iOS 设备，点击`Play`图标。这将需要一段时间，在编译过程中您也可能会看到警告。

漫威在你的新 iOS 游戏！

[https://www.youtube.com/embed/9STnboHARQA](https://www.youtube.com/embed/9STnboHARQA)

如果您在将应用程序部署到 iOS 设备时看到此弹出窗口，您可能需要信任您的个人证书(每六天更新一次)。

在 iOS 11 中这个设置可以在`Settings` > `General` > `Profile & Device Management`中找到。选择`DEVELOPER APP`配置文件并信任证书。

[![Xcode trust certificate on device dialogue](../Images/578bdeb46bee208556ac1ca1d0e8bd2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--THzC-Z3i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/ready-004-trust.png)

## 参考文献

*   [太空鸡-额外奖励:将游戏导出到桌面](https://unity3d.com/learn/tutorials/projects/space-chicken/extra-credit-exporting-game-desktop?playlist=51293)
*   [Unity——将您的 Unity 游戏构建到 iOS 设备上进行测试](https://unity3d.com/learn/tutorials/topics/mobile-touch/building-your-unity-game-ios-device-testing)