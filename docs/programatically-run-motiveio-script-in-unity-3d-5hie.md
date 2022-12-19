# 在 Unity 3D 中以编程方式运行 Motive.io 脚本

> 原文：<https://dev.to/netcell/programatically-run-motiveio-script-in-unity-3d-5hie>

> [Motive.io](https://www.motive.io) 是一个专业平台，允许您将前沿的 AR 技术集成到引人注目的位置 AR 游戏和体验中。

[Motive.io](https://www.motive.io) 就像是 Unity 3D 地理定位游戏的 **Wordpress** ，它拥有一套工具，允许**在其基于网络的界面上创作脚本**，并在客户端激活它们。

事情是这样的， [Motive.io](https://www.motive.io) 只负责**托管**脚本，所有的逻辑实际上都是基于授权工具上设置的条件在客户端上离线运行的。这对于简单的单人游戏来说很好，但对于更复杂的功能，如多人游戏，你需要能够以编程方式控制你的脚本**，意识到它们的**事件**，并从你自己的服务器给它们**参数**。**

 **不幸的是，这项服务目前还处于测试阶段，*“将于 3 月 21 日向公众开放该平台。”，*手册缺少很多内容，例子只是皮毛，还有**没有 SDK 文档**。幸运的是，他们的支持真的很快，很有帮助。

> 在本系列中，我将解释如何用动态变量以编程方式启动脚本。

* * *

# 解剖动机脚本

Motive 脚本在 [Motive.io](https://www.motive.io) 创作工具上创建。

通常，您将使用一个`Main`脚本来启动其他脚本，在示例项目中，有一个脚本面板，您可以在其中手动激活它们。然而，如果您以编程方式启动一个脚本，您将需要它的**唯一标识符**。每个脚本都有一个可以在地址栏的`scriptId`参数中找到的脚本。

我认为术语*【启动脚本】*有点误导。事实上，您将创建该脚本的实例，每个实例都有您指定的唯一的`runId`。每个实例都有自己的内存。例如:如果您创建了一个`Location Task`的实例，那么每个实例都有自己的位置、完成状态等。

* * *

# 对动机脚本的操作

在一个脚本实例上你可以做 3 个操作:**启动、停止和重置。**

*   **启动:**创建一个新的脚本实例，或者恢复已创建的实例
*   **复位:**暂停脚本，复位脚本的原始状态。
*   **Stop:** 暂停脚本，隐藏任何位置标记，但不重置任何内容。

为了理解**复位**和**停止**之间的区别，考虑这个例子:

*   包含两个定位任务的脚本实例被启动。
*   玩家已经在那个实例中完成了一个任务。
*   如果你**停止**然后**再次启动**实例，你只会看到**没有完成的任务。**
*   如果你**重置**，然后**再次启动**实例，你会看到**两个任务。**

* * *

# ScriptManager Singleton

脚本管理和操作由一个具有两个主要方法`LaunchScript`和`StopRunningScript`的`ScriptManager`单例处理。他们最基本的签名如下:

```
public void LaunchScript(
  string scriptId, string runId, Action<bool> onComplete
);

public void StopRunningScript(
  string scriptId, string runId, bool resetState, Action onComplete
); 
```

它们都接收用于标识脚本的`scriptId`、用于标识脚本实例的`runId`和用于完成时回调的`onComplete`动作。

*   对于以`runId`作为唯一标识符的脚本的**启动一个新实例**，或者**恢复一个具有该 id 的实例**，我们使用:

```
ScriptManager.Instance.LaunchScript(scriptId, runId, onComplete); 
```

*   为了**停止 id 为`runId`的实例**，我们用`resetState = false`调用`StopRunningScript`:

```
ScriptManager.Instance.StopRunningScript(scriptId, runId, false, onComplete); 
```

*   为了用 id `runId`重置实例，我们用`resetState = true`调用`StopRunningScript`:

```
ScriptManager.Instance.StopRunningScript(scriptId, runId, true, onComplete); 
```

* * *

# 试举一例

从由 [Motive.io](https://www.motive.io) 提供的 GhostHunter Unity 模板开始，我们将编辑脚本启动器，以便**在每次**启动新实例。

默认情况下，模板的脚本启动器只使用`runId="test"`为每个脚本启动和管理一个实例。

```
// TestScriptsPanel.cs
ScriptManager.Instance.LaunchScript(e.Script.Id, "test", null); 
```

如果你运行这个项目，每次你启动一个脚本，`Launch`按钮就会变成一个红色的`Stop`按钮。你可以通过像这样改变`TestScriptItem.cs`的逻辑来消除这种行为:

```
// TestScriptItem.cs
public void UpdateState()
{
  var isRunning = false; // Previously = ScriptManager.Instance.IsScriptRunning(m_script, "test");

  LaunchButton.gameObject.SetActive(!isRunning);
  StopButton.gameObject.SetActive(isRunning);
} 
```

然而，这只保留了`Launch`按钮，但是这个按钮不会创建新的脚本实例，并且**不会添加新的位置标记**。相反，您可能希望用一个**随机标识符**来替换`"test"`，如下所示:

```
// TestScriptsPanel.cs
var runId = System.Guid.NewGuid().ToString();

ScriptManager.Instance.LaunchScript(e.Script.Id, runId, null); 
```

现在，您可以运行项目并启动任何脚本，它的新实例将被启动。管理所有这些实例需要更多的编码，因为`TestScriptItem.cs`只跟踪`runId = test`实例。我们不会在这里讨论，相反，让我们探索如何**添加动态变量**到我们的脚本。

* * *

# 接下来是什么？

在本系列的下一篇文章中，我将解释如何在 [Motive.io](https://motive.io) 创作工具中创建动态变量，并在 Unity 3D 中启动脚本实例时将值传递给这些变量。**