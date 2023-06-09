# 用纯 C#制作 Unity 游戏

> 原文：<https://dev.to/onmyway133/making-unity-games-in-pure-c-7jb>

作为一名 iOS 工程师，我放弃了故事板以避免所有可能的麻烦，并用纯 Swift 代码编写 UI 组件。我前段时间在 Visual Studio for Windows Phone 应用中做过 XAML，在 Android Studio for Android 应用中做过 XML，体验很好。然而，由于我习惯于用代码写东西，我喜欢为 Unity 游戏做同样的事情，尽管 Unity 编辑器和模拟器相当不错。除了能舒服地用代码声明外，我发现代码很容易区分和组织。我也学到了更多关于组件如何工作的知识，因为在 Unity editor 中，大多数重要的组件和属性已经为我们连接和设置好了，所以有一些我们不知道的非常明显的事情。

我们可以在编辑器中做的所有事情，我们都可以用代码来表达。像设置组件、变换锚点、动画、声音等东西在代码中都是可行的，你只需要使用正确的名称空间。在[有很多信息，欢迎来到 Unity 脚本参考](https://docs.unity3d.com/ScriptReference/)。Unity 2018 拥有 Visual Studio 社区，因此编写代码非常方便。

## Javascript 和 C

如果 Unity 支持 Kotlin 或 Swift，我会非常高兴，但目前只支持 [Javascript 和 C#](https://unity3d.com/learn/tutorials/topics/scripting/c-vs-js-syntax) 。我也是纯 Javascript 的忠实粉丝，但我选择 C#是因为类型安全，也因为 C#是我在大学为 XNA 和 ASP.NET 项目学习的语言。

[![](img/aff25f0ab528030f328eb9018cea7088.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qvBWsFFT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A_c-Ytv_IVI2Q7xJtfw0GIQ.png)

## 缩进

如果你像我一样喜欢 2 空格缩进，你可以在 Visual Studio 中的首选项中更改编辑器设置

[![](img/0ac19b4b4cf5f9981cdf79ccf912d91f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mnk3y9Mv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2854/1%2Ar3ihnAstxkZnDFG6qNrtvg.png)

## 游戏对象和画布

在 Unity 编辑器中，尝试拖动任意 UI 元素到屏幕上，您可以看到 Canvas 和 EventSystem 也被创建。这些是游戏对象，有一堆预定义组件。

[![](img/ab6e3cef7130a100a6858bcd1a88833f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tsTZl05---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2894/1%2AQojuK8XV5FfWqs7bt-79uw.png)

如果我们要用代码来写，我们只需要按照屏幕上的内容来做。一开始，我们必须使用编辑器来学习对象和属性，但后来如果我们熟悉了 Unity 和它的许多 GameObject，我们就可以直接编码了。请原谅，一开始编写代码可能会很麻烦，但你肯定会学到很多东西。

我通常将可重复使用的代码组织成文件，让我们创建一个 C#文件并命名为 Sugar。对于 EventSystemand 和 UI，我们需要 UnityEngine。事件系统和使用 UnityEngine。用户界面命名空间。

下面是如何创建 EventSystem

```
public class Sugar {
    public GameObject makeEventSystem() {
        GameObject systemEventObject = new GameObject("EventSystem");

        EventSystem system = systemEventObject.AddComponent<EventSystem>();
        system.sendNavigationEvents = true;

        StandaloneInputModule module = systemEventObject.AddComponent<StandaloneInputModule>();
        module.horizontalAxis = "Horizontal";
        module.verticalAxis = "Vertical";
        module.submitButton = "Submit";
        module.cancelButton = "Cancel";
        module.inputActionsPerSecond = 10;
        module.repeatDelay = 0.5f;
        module.forceModuleActive = false;

        return systemEventObject;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

和画布

```
public GameObject makeCanvas() {
    GameObject canvasObject= new GameObject("Canvas");

    Canvas canvas = canvasObject.AddComponent<Canvas>();
    canvas.renderMode = RenderMode.WorldSpace;

    CanvasScaler scaler = canvasObject.AddComponent<CanvasScaler>();
    scaler.scaleFactor = 10.0f;
    scaler.dynamicPixelsPerUnit = 10.0f;

    GraphicRaycaster graphic = canvasObject.AddComponent<GraphicRaycaster>();

    canvasObject.GetComponent<RectTransform>().SetSizeWithCurrentAnchors(RectTransform.Axis.Horizontal, 3.0f);
    canvasObject.GetComponent<RectTransform>().SetSizeWithCurrentAnchors(RectTransform.Axis.Vertical, 3.0f);

    return canvasObject;

} 
```

Enter fullscreen mode Exit fullscreen mode

以及如何制作一些常见的 UI 元素

```
public GameObject makeBackground(GameObject canvasObject) {
    GameObject backgroundObject = new GameObject("Background");
    backgroundObject.AddComponent<CanvasRenderer>();

    Image image = backgroundObject.AddComponent<Image>();
    image.color = Color.green;

    backgroundObject.transform.SetParent(canvasObject.transform, false);

    return backgroundObject;
}

public GameObject makeLogo(GameObject parentObject) {
    GameObject logoObject = new GameObject("Logo");
    logoObject.AddComponent<CanvasRenderer>();

    Image image = logoObject.AddComponent<Image>();
    image.color = Color.red;

    logoObject.transform.SetParent(parentObject.transform, false);

    return logoObject;
}

public GameObject makeButton(String title, GameObject parentObject) {
    GameObject buttonObject = new GameObject(title + " Button");
    buttonObject.AddComponent<CanvasRenderer>();

    GameObject textObject = new GameObject("Text");
    textObject.AddComponent<CanvasRenderer>();

    Text text = textObject.AddComponent<Text>();
    text.text = title;

    textObject.transform.SetParent(buttonObject.transform, false);
    buttonObject.transform.SetParent(parentObject.transform, false);

    return buttonObject;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在在你的场景中创建一个空的游戏对象，并添加一个脚本组件到这个游戏对象中。您可以毫无问题地在 Sugar.cs 中引用代码

## 何去何从

*   从脚本中创建 UI 元素:另一种方法是使用 C#中的预置和实例化函数来轻松引用和构造元素

*   [2D 游戏创作](https://unity3d.com/learn/tutorials/s/2d-game-creation):制作 2D 游戏的官方 Unity 教程。

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️