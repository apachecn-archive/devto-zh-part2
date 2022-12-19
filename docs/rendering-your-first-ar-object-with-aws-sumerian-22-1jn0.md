# 用 AWS Sumerian 渲染你的第一个 AR 对象(2/2)

> 原文：<https://dev.to/wandererstudio/rendering-your-first-ar-object-with-aws-sumerian-22-1jn0>

如果你已经按照本教程的前一篇文章学习了，你现在应该有一个工作项目，其中有两个场景。

我们现在将介绍如何创建和发布场景，以及如何在项目的其他场景中使用这些场景。

#### 创建三维实体

##### 添加实体

现在我们准备继续，点击你想进入的场景并进入编辑器屏幕

因为我们只想渲染一个简单的 3d 对象，这就是我们要做的:

1.  点击编辑器屏幕顶部的**创建实体**。
2.  选择提示屏幕左上角的**框**实体。
3.  (可选)单击该框，并在屏幕右侧更改它的`name`属性，以便将来可以更容易地识别该实体。

[![](../Images/7e62d2e438534a6b339f8c07cf7f90a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gcx6nrei--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lnb3zwbxgg6yo82ayreg.png)

随着 AWS Sumerian 自动保存所有更改，我们差不多完成了创建一个基本的 3D 形状，它将呈现在屏幕上(尽管你可以随意地让它变得复杂)。

##### 创建资产包

在编辑器屏幕的左下角，您会注意到一个 **Assets** 部分，其中有各种图标和一个树形层次菜单。

在这个部分中，您将能够创建资产包，然后可以在 AWS 项目中的所有场景之间共享这些资产包。要在其中添加我们自己定制的包，请遵循以下步骤:

1.  点击**创建包**按钮
2.  选择新创建的`New Pack`
3.  更改屏幕右侧的`name`属性，因为这是您将在其他场景中引用的名称，您希望能够识别您的资产
4.  将屏幕右上方的框实体(在“实体”面板中)拖动到新资产包中

如果一切顺利，我们现在有一个自定义包，里面有一个 3D 实体，现在这一步剩下的就是让它可以被其他场景访问。

##### 导出资产包

要导出您创建的任何包，您只需将鼠标悬停在所需的包上，然后单击左侧的第二个图标。

这应该会提示一个模块，询问您将要出口哪种包装，在本例中，我们出口的是属于`Entities`类别的包装。

#### 处理你的 AR 场景

既然我们手头上有现成的 3D 资产，我们只需进入我们的 AR 场景并利用它！

##### 场景元素

回到我们的 AR 场景的编辑器屏幕。您会注意到，在屏幕左侧，我们有以下实体:

*   AR 相机:场景渲染的初始点，坐标`(0, 0, 0)`在`(X, Y, Z)`轴上。
*   AR 锚:锚是一个坐标，你可以用它将实体附加到你的场景中，AR 模板附带了一个自动放置在`(0, 0, 0)`坐标的锚。

##### 导入资产

我们要做的第一步是将我们的资产包导入到场景中。

只需点击顶部的**导入资产**，选择您想要的包(请注意，这里也有许多预先制作的选项可供选择！)，并点击菜单底部的**添加**。这应该会在您的`Assets`菜单上添加一个新包。

##### 添加脚本

此时，我们*可以*将我们的 3D 资产拖入场景中，并在应用程序中进行渲染。

但是我发现，大多数真正的应用程序更感兴趣的是通过编程将对象添加到它们的场景中，而不是将它们“硬编码”在那里。为了实现这一点，我们将不得不使用 AWS 苏美尔人的脚本功能，这将需要你知道一点 Javascript。

我们要做的第一件事是向`ARAnchor`实体添加一个脚本:

1.  在左上树形层次菜单中选择`ARAnchor`实体
2.  向下滚动屏幕右侧的属性窗格，找到`Scripts`部分
3.  点击`+`按钮添加新脚本，并选择`Custom`选项

这将创建一个脚本模板，其中包含一些在不同时间触发的生命周期功能。

在这个例子中，我们将只使用`setup`函数，并在文件底部添加我们的自定义函数`parameters`。

##### 实例化一个对象

我们需要将我们定制的 3D 资产加载到这个脚本中，以便它可以被渲染，为此我们需要为我们的函数添加一个`parameter`来接收所述资产:

```
var parameters = [
    {type: 'entity', key: 'exampleEntity', description: 'Example Entity'},
]; 
```

Enter fullscreen mode Exit fullscreen mode

注意:您可以随意调用`key`和`description`值，但是建议将它保持为与您将加载到该参数中的资产包相关的值。

此时，您可以保存您的脚本文件，您会注意到一个带有`Entity`属性的新槽出现在右边的属性菜单上(在脚本部分内)。

[![](../Images/27b1d3bc306fac85236edf4b5dede6a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1GlfsPvE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kax50n5vue9y78fuclgq.png)

此时，您可以将您的资产包拖动到空槽中，现在应该可以在该空槽中看到您的包的名称，为了完成该过程，添加将实例化您的资产并将其放置在 AR 世界场景中的代码:

```
function setup(args, ctx) {
    const arSystem = ctx.world.getSystem('ArSystem');

    // Creates an Entity and adds it to the world
    var x = 0;
    var y = 0;
    var z = -1.5;
    var translation = new sumerian.Matrix4(
        1, 0, 0, 0,
        0, 1, 0, 0,
        0, 0, 1, 0,
        x, y, z, 1,
    );

    var clonedEntity = sumerian.EntityUtils.clone(ctx.world, args.exampleEntity);
    clonedEntity.addToWorld();
    clonedEntity.setComponent(new sumerian.ArAnchorComponent());
    arSystem.registerAnchor(translation, function(anchorId) {
        clonedEntity.getComponent('ArAnchorComponent').anchorId = anchorId;
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

为了解释上面看到的代码:
首先我们实例化`ArSystem`，这是苏美尔语中的一个内置功能，允许用户执行一些 AR 固有的功能。在这里查看文件。

之后，我们设置我们希望 3D 实体渲染的`x`、`y`和`z`值，并从它们创建一个变换值以备后用。

然后，我们使用 [EntityUtils](https://content.sumerian.amazonaws.com/engine/latest/docs/EntityUtils.html) 来生成我们的`exampleEntity`的克隆，我们可以从`args`参数接收它，并将它添加到世界中。

由于 AR 功能围绕着将锚点绑定到虚拟对象，我们现在需要采取额外的步骤将锚点组件附加到我们的`clonedEntity`，然后从我们之前设置的`tranlation`值注册一个新的锚点。

最后，我们设置我们的`clonedEntity`的`anchorId`值来匹配我们刚刚创建的那个，这将导致你的对象在你的`translation`值上设置的位置被查看。

#### 在 iOS 应用中加载场景

现在，我们已经设置了实例化节点和锚点背后的所有逻辑，以及在真实空间中呈现 3D 实体。我们现在必须用一个真实的设备来测试。

你要做的第一件事是**使用编辑器屏幕顶部的菜单发布你的场景**，或者点击`Shift + Ctrl + P`。然后，您可以按照以下步骤
在您的设备上运行它:

##### 运行在 iOS 上

1.  克隆 [SumerianARKitStarter](https://github.com/aws-samples/amazon-sumerian-arkit-starter-app) 存储库。
2.  打开 XCode，在“文件”菜单上选取“打开”，然后导航到克隆存储库的顶层目录中的 SumerianArKitStarter.xcodeproj 文件。
3.  在应用程序设置的“常规”选项卡上设置您的代码签名身份和配置文件。如果你不熟悉这些概念，请参阅苹果的文档。
4.  在 XCode 中，打开`ViewController.swift`文件。将`sceneURL`变量改为指向上一步中发布的场景的 URL**。将`/?arMode=true`附加到网址上。该参数使场景以更适合 AR 体验的方式进行渲染(具体来说，它在 WebGL 画布上启用 alpha 通道并移除 UI 元素)。**
5.  连接您的 iOS 设备。构建并运行应用程序。

##### 运行在 Android 上:

1.  克隆 [SumerianARCoreStarter](https://github.com/aws-samples/amazon-sumerian-arcore-starter-app) 存储库。
2.  打开 Android Studio，从菜单中选择 Open a existing Android Studio project，然后导航到克隆存储库的顶层目录中的 SumerianARCoreStarter 目录。
3.  在 Android Studio 中，打开`MainActivity.java`文件。将`SCENE_URL`变量更改为指向上一步发布的场景的 URL。将`/?arMode=true`附加到网址上。该参数使场景以更适合 AR 体验的方式进行渲染(具体来说，它在 WebGL 画布上启用 alpha 通道并移除 UI 元素)。
4.  连接您的 Android 设备。构建并运行应用程序。

#### 包装完毕

希望通过遵循所有这些，你现在已经能够建立自己的 AWS 苏美尔人项目，并且你已经掌握了如何导入你自己的场景元素的基本知识，并使它们出现在你的 iOS 和 Android 设备的屏幕上。

虽然仍处于起步阶段，但在网络上托管 AR 场景并使其适用于所有类型的移动设备的可能性令我非常兴奋，尽管这只是对您可以使用 AWS Sumerian 做什么的一个非常粗浅的看法，但我邀请大家加入 [AWS Sumerian Slack page](https://amazonsumerian.slack.com) 并成为这一令人兴奋的计划的一部分。

如果这篇教程写得太乱，我很抱歉，我对写这样的文章还不是很有经验，但我希望我可以通过练习来提高:)

参考资料:

*   [用 ARCore 运行 AWS 苏美尔教程](https://docs.sumerian.amazonaws.com/tutorials/create/intermediate/augmented-reality-using-sumerian-arcore/)
*   [用 ARKit 运行 AWS 苏美尔教程](https://docs.sumerian.amazonaws.com/tutorials/create/intermediate/augmented-reality-using-sumerian-arkit/)
*   [脚本 API](https://content.sumerian.amazonaws.com/engine/latest/docs/index.html)