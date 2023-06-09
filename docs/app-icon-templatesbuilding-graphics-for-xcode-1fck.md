# 应用程序图标模板—为 Xcode 构建图形

> 原文：<https://dev.to/leogdion/app-icon-templatesbuilding-graphics-for-xcode-1fck>

资产目录是 Xcode 项目中的关键组件，用于存储颜色、
精灵、元数据，当然还有图形，管理这些图形可能是一个
挑战。今天我们来谈谈在素描
和 Photoshop 中使用应用图标模板。

# 使用 App 图标模板

应用程序图标模板提供了一种简单且可预测的方式来创建您的资产
，同时使用设计人员熟悉的图形应用程序。我们要
看看前两位的图形编辑:**素描**和 **Photoshop** 。

# 草图

[https://www.youtube.com/embed/4Um_zRh4ENY](https://www.youtube.com/embed/4Um_zRh4ENY)

在过去的几年里，Sketch 已经发展成为图形设计师使用的主要应用程序之一，尤其是在移动应用程序开发领域。幸运的是，他们已经提供了一个应用程序图标模板来使用。

1.  运行 Sketch 应用程序，启动对话框应该会出现，显示 Sketch 中内置的各种模板。
2.  选择 **iOS 应用图标**模板。现在，您应该会看到带有各种大小的应用程序图标的模板。
3.  继续编辑最大的图标。当您编辑大图标时，您的更改应该会反映在您的其他应用程序图标大小中。
4.  完成后，转到顶部菜单并选择**导出**。在“导出”对话框中，选择所有要导出的尺寸，然后选择要导出到的文件夹。

如果你去那个文件夹，你应该看到你所有的图标大小。从那里你可以
拖动文件到你的资产目录应用图标槽。

Sketch 提供了一种非常简单的导出图形集的方法。显然，每次需要进行编辑时，都需要访问 Sketch，并且需要在 repo 中更新多个文件。

# Photoshop

[https://www.youtube.com/embed/yr6KsuP5j5A](https://www.youtube.com/embed/yr6KsuP5j5A)

二十多年来，Photoshop 一直是公认的图形编辑器。
然而，它并不像 Sketch 那样针对应用程序开发，所以
它并不像 Sketch 那样针对应用程序开发。

苹果通过他们的[苹果设计资源
页面](https://developer.apple.com/design/resources/)为我们提供了 Photoshop 的用户界面模板和小工具(以及不断更新的草图
)。

1.  前往[苹果设计资源页面](https://developer.apple.com/design/resources/)并下载您将要构建的操作系统的模板。在这种情况下，我们将使用 iOS 12 版本。**请注意——这些下载文件非常大，所以请务必提前完成。**
2.  解压缩模板集合，转到*生产模板*文件夹。我们将使用标准的*模板-AppIcons-iOS.psd* 文件。打开 PSD 文件，打开 Photoshop。
3.  确保我们在保存时生成图像资产。转到文件…生成…图像资产，并确保它被选中。
4.  进入图层窗口，双击图层图标。这是您将直接编辑的图层，并将被应用到其他应用程序图标大小。
5.  转到包含您打开的 PSD 文件的文件夹。您应该看到一个同名的文件夹，但是包含后缀 *-assets* 。在那里，您应该可以看到从模板中导出的所有图标大小。

Photoshop 是更成熟的应用程序，但也不太专注于 UI 和
应用程序图标开发。模板可能相当大，甚至更麻烦
每次打开和编辑都可以。

理想情况下，我们希望简单地编辑一个文件，它可以包含在我们的 repo 和在构建中自动生成的
中。这就是构建一个 bash 脚本在构建上运行会有所帮助的地方。

*最初发布于[https://learning swift . bright digit . com/app-icon-templates-graphics-xcode/](https://learningswift.brightdigit.com/app-icon-templates-graphics-xcode/)T3】*