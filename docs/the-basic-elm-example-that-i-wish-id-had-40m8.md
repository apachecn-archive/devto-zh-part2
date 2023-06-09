# 我希望我有一个基本的榆树例子

> 原文：<https://dev.to/nimmo/the-basic-elm-example-that-i-wish-id-had-40m8>

注意:你根本不需要太多的 Elm 知识来从中受益，但是同样地，这也不适合任何对 Elm 一无所知的人。它更适合那些刚刚开始接触这门语言的绝对基础，并试图理解如何实现“组件之间的通信”(或者更确切地说，为什么不这样想呢！)

*2018 年 10 月 9 日更新:针对 Elm 0.19 更新*

今天，我在 Elm 中重新创建了一个示例应用程序(最初的示例在 React+Redux 中)，并意识到我已经编写了 Elm 示例，我希望在我第一次开始探索这种语言时就拥有它，所以我认为它可能值得与更广泛的受众分享。

### 初始设置

如果你的机器上还没有安装 Elm，你可能想现在就看一下[安装说明](https://guide.elm-lang.org/install.html)。

安装了 Elm 之后，在您选择的位置的终端中，运行`elm init`来初始化您的新项目。这将为您创建一个`elm.json`文件，以及一个`src`目录。*请注意，这并不会为您创建应用程序目录，因此请确保您现在位于您实际希望您的应用程序所在的目录中。*

首先在你的`src`目录中创建一个`Main.elm`文件，然后在你喜欢的编辑器中打开它(尽管如果你想要一个推荐，Visual Studio 代码和 [Elm 语言插件](https://marketplace.visualstudio.com/items?itemName=sbrink.elm)一起使用是非常好的)，然后在顶部添加你的模块声明:

```
module Main exposing (..) 
```

Enter fullscreen mode Exit fullscreen mode

### 榆树架构的快速介绍

Elm 应用程序的关键是 Elm 体系结构，至少在一个完全不与外界交流的应用程序中是这样的:

[![](img/b64a4c7e32b31e12e52a8726d48dde92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oT29AJmn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pfdd18lau3fmjhv63bou.png)

这意味着我们需要一个`Model`、一个`View`和一个`Update`。但是这些是什么？

#### 型号

我们的`Model`是我们将要定义应用程序的`state`的地方。这个例子背后的整个思想是在考虑任何事情之前先考虑我们的应用程序的状态，尽管我们将在后面更详细地讨论这个问题。

#### 视图

我们的`view`将是一个接受`Model`的函数，并返回我们在 Elm 中的 [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) 的表示。Elm 包括一个虚拟 DOM 和一个 HTML 库，使您能够在利用 Elm 提供的保证的同时编写标记。

#### 更新

我们的`update`函数将负责处理对我们的`Model`的任何更新。值得注意的是，尽管顾名思义，对模型的更新实际上不会原地发生——相反，`update`将返回一个新的模型，该模型将被传递给我们的`view`函数，允许在我们的应用程序中呈现任何必要的更改。

### 我们要建造什么

好了，现在我们来看看我们的任务。我们将会建造一个高科技，最先进的虚拟房间。那个房间只有一扇门和一个报警系统。其规格为:

```
If the alarm is armed, then it should be triggered by the door opening.
If the alarm has been triggered, then it can be disarmed, but not armed.
If the door is open, the alarm's current state can not be altered manually.
If the door is open it can be closed.
If the door is closed it can be opened or locked.
If the door is locked it can be unlocked. 
```

Enter fullscreen mode Exit fullscreen mode

所以我们知道，我们需要有一个房间，里面有两个东西，每一个都可以用许多可能的状态来表示，我们知道什么样的状态组合可以被表示，我们知道可能的状态之间的转换是什么。在我看来，我们正在寻找:

```
-- POSSIBLE STATES:

Door: 
  Locked
  Closed
  Opened

Alarm:
  Armed
  Disarmed
  Triggered

Combined: 
  Locked + Armed
  Locked + Triggered
  Locked + Disarmed
  Unlocked + Armed
  Unlocked + Triggered
  Unlocked + Disarmed
  Opened + Triggered
  Opened + Disarmed

-- POSSIBLE TRANSITIONS: 

Door: 
  Closed <-> Locked
  Closed <-> Opened

Alarm: 
  Armed -> Triggered
  Triggered -> Disarmed
  Armed <-> Disarmed 
```

Enter fullscreen mode Exit fullscreen mode

但是整个应用程序呢？在一个完美的世界中，它似乎只能以一种状态存在:房间被显示的状态。但是为了更好地衡量，让我们考虑一下，我们可能会在某个时候引入一些逻辑错误，如果我们这样做了，我们可能想要得到一些关于发生了什么的提示。因此，让我们考虑这个应用程序有两种总体状态可能性:`DisplayingRoom`，它需要知道`Door`和`Alarm`，以及`Failure`，它需要某种消息来告诉我们它是如何到达那里的。

嗯，感觉就像我们刚刚描述了我们的`Model`，在我们可能的门和警报状态之上。现在让我们把所有这些都添加到我们的`Main.elm`文件中:

```
type Model 
  = DisplayingRoom DoorState AlarmState
  | Failure String

type DoorState
  = Opened
  | Closed
  | Locked

type AlarmState
  = Armed
  | Disarmed
  | Triggered 
```

Enter fullscreen mode Exit fullscreen mode

这就是在 Elm 中创建`custom type`的方法，以上都是定制类型的例子。

### 编码我们的`update`功能

在这个阶段，因为我们预先做了所有的计划，所以我们也有了编写`update`函数所需的所有信息。更新需要两个东西:一个`message`(它是需要发生的转换的描述)，和`model`(它是应用更新之前的模型*)，它将返回一个新的模型。*

我们的转换将采用`messages`的形式，再次感谢我们对问题的预先思考，我们已经知道它们将会是什么，所以现在让我们将它们添加到我们的`Main.elm`文件:

```
type Msg
    = Open
    | Close
    | Lock
    | Unlock
    | Arm
    | Disarm 
```

Enter fullscreen mode Exit fullscreen mode

(如果你想知道为什么我们没有一个`Trigger`消息，那是因为在我们的规范中没有任何方法来手动触发警报！)

奇怪的是我们还没有看到任何东西，是吧？嗯，一开始是这样。根据我的经验，在整理出事物的样子之前，先整理好你的数据是非常好的。

好吧，回去工作！我们的`update`函数将接受一个`message`和一个`model`，它将返回一个新的`model`。我们将从检查应用程序的状态(即`Model`的值)开始:

```
update msg model =
  case model of
    DisplayingRoom doorState alarmState ->
      model
    Failure errorMessage -> 
      model 
```

Enter fullscreen mode Exit fullscreen mode

如果我们处于失败状态，`update`函数没有办法恢复，所以它可以返回`model`，这就是我们在`update`函数中对`Failure`的处理。

那么，在展示我们的房间时，我们对可能的状态组合了解多少呢？我们知道当我们的门开着的时候，我们有最多的约束——事实上，我们只有一个可能的状态转换，因为我们不能与我们的闹钟互动，直到门关上。现在让我们把它加进去:

```
update : Msg -> Model -> Model
update msg model =
    case model of
        DisplayingRoom doorState alarmState ->
          case doorState of
            Opened ->
              case msg of
                Close ->
                  DisplayingRoom Closed alarmState

                _ ->
                  Failure "unexpected message received while door was in Opened state"

        Failure _ -> 
          model 
```

Enter fullscreen mode Exit fullscreen mode

现在，当门处于`Opened`状态时，我们正在处理我们的`Close`消息，我们说，在这种情况下，我们想要返回的模型是`DisplayingRoom Closed alarmState` - `alarmState`，它是变量的名称，该变量保存警报被传递到模型时所处的状态；这确保了当门从`Opened`到`Closed`时，它被传递到新模型而不被改变。

我们还说，对于门处于这种状态时收到的任何其他消息，我们希望将我们的应用程序置于我们的`Failure`状态，用一条消息说明它是如何到达那里的。我们将以这样一种方式对视图进行编码，在这里不应该有其他消息，但是至少现在如果我们犯了一个错误，我们将有一个简单的方法给我们自己那个信息。

接下来，让我们来处理我们的`Closed`门状态。在这种情况下，我们知道如果门是关着的，并且我们收到了一个`Open`消息，那么我们需要关心警报的状态，我们也知道当门是关着的时候，我们也可以手动改变警报的状态。所以让我们把这些都加入:

```
 Closed ->
                  case msg of
                    Open ->
                      case alarmState of
                        Armed ->
                          DisplayingRoom Opened Triggered

                        _ ->
                          DisplayingRoom Opened alarmState

                    Lock ->
                      DisplayingRoom Locked alarmState

                    Arm ->
                      DisplayingRoom Closed Armed

                    Disarm ->
                      DisplayingRoom Closed Disarmed

                    _ ->
                      Failure "unexpected message received while door was in Closed state" 
```

Enter fullscreen mode Exit fullscreen mode

好的，现在如果我们的门被打开，同时我们的警报被启动，它就会触发警报。我们现在也在处理与警报相关的消息。因此，剩下要添加的是当我们的门是`locked` :
时任何可能的消息

```
 Locked ->
                  case msg of
                    Unlock ->
                      DisplayingRoom Closed alarmState

                    Arm ->
                      DisplayingRoom Locked Armed

                    Disarm ->
                      DisplayingRoom Locked Disarmed

                    _ ->
                      Failure "unexpected message received while door was in Locked state" 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的`update`功能！因此，现在我们已经添加了描述我们的数据如何表示的所有内容，以及所有可以改变的方式，是时候添加一些`view`代码了，这样我们就可以看看它是否都工作了。

### 编码我们的`view`功能

正如我前面提到的，Elm 有一个`HTML`库，它允许我们在 Elm 代码中编写 HTML。现在通过在你的模块声明下添加

`import Html exposing (..)`

来导入它。

先说最简单的部分；我们的`failure`功能。我们知道在这种状态下我们从模型中得到一个消息，所以让我们创建一个函数来显示它:

```
failure message =
    div []
        [ p [] [ text message ] ] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们创建我们的`Door`。我们知道当它被关闭时，需要有两个可能的消息，但是如果它被打开或锁定，只有一个消息。基于传入的内容，创建一个表示所有这些不同状态的函数是很容易的，但是坦率地说，单独创建它们并不需要很多代码，而且谁知道随着我的高科技门和警报应用程序的扩展，它们会偏离多远呢——让我们不要过早地将自己优化到一个角落。在 Github 上看看我的门模块[，你会看到我们有所有我们需要的门功能，以满足我们的验收标准。](https://github.com/dnimmo/elm-state-machine-example/blob/master/src/View/Door.elm)

现在我们要为我们的`Alarm`做同样的事情，你也可以在 Github 上查看[。你会注意到，在这种情况下，我们说 alarm 不仅需要知道它有能力发送的`message`(记住我们的 Alarm 只能根据我们的应用程序所处的状态发送一条消息——它永远不会处于我们可以同时`arm`和`disarm`它的状态),而且它还需要知道是否允许任何动作。这给了我们禁止发送任何警报消息的能力，但关键是`Alarm`模块本身*不需要从任何地方接收任何消息来实现这一点*。当我们将`view`函数连接到`Main.elm`中时，我们会处理这个问题，我们现在就做。](https://github.com/dnimmo/elm-state-machine-example/blob/master/src/View/Alarm.elm)

记得把你的`Door`和`Alarm`模块也放进你的`Main.elm`里。如果你用和我一样的方法设置它们(即`View/Door.elm`和`View/Alarm.elm`，并且每个都根据门/警报状态显示单独的功能)，你可以如下导入它们:

```
import View.Alarm as Alarm exposing (armedAlarm, disarmedAlarm, triggeredAlarm)
import View.Door as Door exposing (closedDoor, lockedDoor, openDoor) 
```

Enter fullscreen mode Exit fullscreen mode

我们希望显示我们的`door`和`alarm`，并且我们希望根据应用程序的状态确保它们以正确的状态显示。我们现在知道了我们的应用程序开始时处于什么状态，以及之后它可能处于什么状态，并且我们已经构建了允许我们显示我们需要的内容的模块，所以我们只需要在中间添加位！我们在`Main.elm`中的`view`函数应该是这样的:

```
view : Model -> Html Msg
view model =
    case model of
        Failure message ->
            failure message

        DisplayingRoom doorState alarmState ->
            div
                []
                [ div
                    [ class "doorPanel" ]
                    [ case doorState of
                        Opened ->
                            openDoor Close

                        Closed ->
                            closedDoor Open Lock

                        Locked ->
                            lockedDoor Unlock
                    ]
                , div
                    [ class "alarmPanel " ]
                    [ case alarmState of
                        Armed ->
                            armedAlarm Disarm (doorState /= Opened)

                        Disarmed ->
                            disarmedAlarm Arm (doorState /= Opened)

                        Triggered ->
                            triggeredAlarm Disarm (doorState /= Opened)
                    ]
                ] 
```

Enter fullscreen mode Exit fullscreen mode

在这里，你可以看到我们正在检查来自我们的`model`的`doorState`，并根据门的状态调用适当的函数，以及适当的消息。我们对`alarmState`做了同样的事情，在我们的每个报警函数中，我们传递正确的消息以匹配我们在函数中期望的行为，我们也传递对`doorState /= Opened`的检查结果，因为我们告诉我们的`alarm`函数期待一个布尔值来声明是否允许任何动作。

### 接线运行时

我们需要 Elm 的核心`Browser`模块来让它在运行时做任何事情。将`import Browser`添加到`Main.elm`文件的顶部，模块声明的下面。(注意:通常你需要`install`新的包才能被导入，但是`Browser`是在你运行`elm init`时自动安装的)

`Browser`有几个函数，但是我们这里需要的是 [Browser.sandbox](https://package.elm-lang.org/packages/elm/browser/latest/Browser#sandbox) 。沙盒允许您创建一个使用 Elm 架构的应用程序，但它不与“外部世界”(即任何外部 API 或 JavaScript)对话。它需要一个有三个字段的`record`:`init`、`update`和`view`。我们已经有了`update`和`view`函数，但是`init`将描述应用程序的初始模型——假设我们将显示房间，门关闭，警报启动:

```
initialModel : Model
initialModel = DisplayingRoom Closed Armed

main : Program () Model Msg
main =
    Browser.sandbox
        { init = initialModel 
        , view = view
        , update = update
        } 
```

Enter fullscreen mode Exit fullscreen mode

### 运行我们的应用

好了，这就是我们需要做的一切——在您的终端中，运行`elm make src/Main.elm`。这将给你一个`index.html`文件，你可以在你选择的浏览器中打开它。

### 这只是...工作了？

感觉真的应该有更多的事要做，不是吗？如果你习惯于 web 开发是一个“看看它看起来如何->摆弄代码->看看它现在看起来如何”的循环，这一开始可能有点奇怪，但是我发现从`model`向下工作而不是从`view`向上工作是有意义的*很多*，并且保持事情漂亮和干净。

希望这对某人有用！