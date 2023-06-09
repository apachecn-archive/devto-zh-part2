# 使用 Apecs 在 Haskell 中开发游戏的介绍

> 原文：<https://dev.to/ashe/an-introduction-to-developing-games-in-haskell-with-apecs-19ee>

为了在 Haskell 中开发游戏，已经发布了如此多的框架、示例和原型，然而我们仍然没有太多(如果有的话)商业版本可以从中寻找灵感。我想帮助放宽 Haskell 游戏开发的入口点，并向人们展示用 Haskell 编写游戏是多么有趣。我曾经为视频游戏制作 YouTube 指南，通常我得到了积极的回应，所以我想我可以在我的博客帖子甚至我的 YouTube 频道上做类似的事情。

在游戏行业与 Haskell 一起工作是一项有点开创性的工作，但我坚信我们的技术已经足够先进，我们应该能够牺牲一点点性能来使开发更容易。我的第一次经历并不令人惊奇，但是在做了一段时间其他事情和思考之后，我决定再试一次。这一次，我决定尝试一个别人做的框架，而不是什么都自己做。

本指南是我自己写的，我处于 Haskell 的初级和高级阶段之间——我已经读了 Haskell 的书,但是我仍然在学习，因为我在实践中应用了我的知识。我希望我的初步观点能帮助像我一样不太喜欢学术的人更容易消化东西，我只是想为那些紧随其后的人创造捷径！

> 请注意，虽然我不打算假设 Haskell 精通，但我希望至少有一点 Haskell 或函数式编程知识。我不打算介绍绝对的基础知识，但是我将介绍我正在做的事情，以便新的 Haskellers 理解正在发生的事情。我也不会谈论如何用 Stack 建立一个项目，我将纯粹专注于 Haskell。

## Haskell 为什么要做游戏？

你可以用几乎任何一种编程语言来制作游戏，你是否应该完全取决于你自己。然而，有许多障碍阻碍了 Haskell 在游戏中的使用。我已经为我的 Haskell 游戏工作了大约一个月，在我开始宣扬这是可能的之前，我想真正对这种语言感到自信。不管你选择什么样的框架或者你是否有自己的框架，语言本身就是你游戏的基础，它必须适合你的任务。

人们选择避开 Haskell 的一个原因仅仅是语言本身的难度。游戏开发有多难就有多难，有许多不同的引擎、框架和语言可供选择。虽然我同意对大多数人来说理解 Haskell 可能很困难，但是一旦你有了基础，随着时间的推移，添加额外机制的过程会变得更容易。

我想花点时间来称赞 Apecs 在使用函数式语言时缓解了多少问题。我确信其他框架也会让 Haskell 变得舒适，这绝不是使用 Apecs 的理由，这只是它可用性的一个证明。在不深入 Haskell 和 FP 语言的细节的情况下，Haskell 与 Apecs 的结合确实使开发变得更加容易，我真的可以想象未来人们可能会选择像 Haskell 这样的语言来简化开发。

*   9/10 次，如果编译成功，你的游戏就会运行。也许不如预期，但至少不会崩溃！
*   使用 [Stack](https://docs.haskellstack.org/en/stable/README/) 和 [Stackage](https://www.stackage.org/) ，你的游戏的所有依赖项都可以通过简单地列出来自动下载，这使得构建你的项目变得容易。
*   如果你想做一个网页游戏或者类似的东西，GHCJS 允许你把你的 Haskell 代码编译成 Javascript。
*   Haskell 的类型系统确保你知道纯函数和不纯函数的区别。通过以这种方式组织代码，你简化了游戏的结构，使得*非常容易*找到游戏中的逻辑错误。
*   做事情的*的重要函数可以包装在单子中，并利用`do`语法，这意味着你可以执行多种副作用。*
*   函数是一等公民，它们与值受到完全相同的对待，可以很容易地存储(拼写万岁！).
*   人们说学习和使用 Haskell 会对你有所帮助，即使是在命令式语言中。我喜欢 Haskell，因为它让我在解决问题时感觉很好，所以如果你喜欢 Haskell，为什么不用你喜欢的语言做一个游戏呢？

事不宜迟，我们开始做游戏吧！

## 入门

### 初始化

对于这个项目，我们将从 [Apecs](https://hackage.haskell.org/package/apecs) 和 [SDL2](https://hackage.haskell.org/package/sdl2) 开始。选择任何适合你的，但我会展示相当多的 Apecs，我会用 SDL2 开始这个项目，所以你必须研究如何独立地开始你的游戏。我也将使用 [SDL-ttf](https://hackage.haskell.org/package/SDL-ttf) 和 [SDL-image](https://hackage.haskell.org/package/SDL-image) ，记住，如果你也使用绑定到 C 的包，你也需要将它们安装到你的系统上，而不仅仅是 Haskell 绑定。现在，让我们展示一些代码。

> 注意，我是用`qualified`导入 SDL 的，所以每个 SDL 函数前面都有 SDL。我主要在主文件中这样做，因为有很多事情要做。如你所愿。此外，由于我们还没有实现`initialise`函数，所以这还不能编译。

```
import Apecs
import qualified SDL
import qualified SDL.Image(quit)
import qualified SDL.Font

-- Uses templateHaskell to create the data 'World'
-- also creates initWorld
makeWorld "World" []

main :: IO ()
main = do
  -- Initialise Apecs world
  world <- initWorld

  -- Initialise SDL
  SDL.initialize [SDL.InitVideo]
  SDL.Font.initialize

  -- Create a window and renderer
  window <- SDL.createWindow "App" SDL.defaultWindow
  renderer <-
      SDL.createRenderer window (-1)
        SDL.RendererConfig
          { SDL.rendererType = SDL.AcceleratedRenderer
          , SDL.rendererTargetTexture = False
          }

  -- Do any initialisation here!
  runSystem initialise world

  -- Display the game
  SDL.showWindow window

  -- Insert loop code here!

  -- Delay shutdown if you like?
  SDL.delay 1000

  SDL.destroyRenderer renderer
  SDL.destroyWindow window
  SDL.Image.quit
  SDL.Font.quit
  SDL.quit
  putStrLn "Goodbye!"
  exitSuccess 
```

Enter fullscreen mode Exit fullscreen mode

好，这里我们有一个基本的主函数，它创建一个世界，然后关闭它。没什么特别的。我甚至加入了 [SDL。延迟](https://hackage.haskell.org/package/sdl2-2.4.1.0/docs/SDL-Time.html#v:delay)以便你能在窗口关闭前看到它。注意`SDL.Image`在使用时被初始化，不像核心和字体变化，这就是为什么这里只导入退出。这段代码没什么特别的，所以我应该停止输入，转而描述一个简单的游戏循环。但首先，让我们看看 [Apecs 的`makeWorld`功能](https://hackage.haskell.org/package/apecs-0.5.0.0/docs/Apecs.html#v:makeWorld)。

如果你真的点击了上面的链接，你会注意到它在某个时候会说“变成”。这个函数使用了模板 Haskell，简单的给你写了很多东西。你永远找不到`World`类型的原因是，它实际上是在所描述的`makeWorld`函数中创建的，因此函数`initWorld`专用于在`makeWorld`中创建的类型。函数`makeWorld`接受一个字符串，该字符串将成为类型(在我们的例子中，“世界”成为`World`)以及一个我们尚未制作的组件列表。综上所述，`makeWorld`只是一个为你创建世界类型和一堆你的整个游戏将围绕其运行的函数的模板。

### 循环功能

现在为循环功能:

```
let loop prevTicks secondTick fpsAcc prevFps = do
      ticks <- SDL.ticks
      payload <- map SDL.eventPayload <$> SDL.pollEvents
      let quit = SDL.QuitEvent `elem` payload
          dt = ticks - prevTicks
          calcFps = secondTick + dt > 1000
          newFps = if calcFps then fpsAcc + 1 else prevFps
          newFpsAcc = if calcFps then 1 else fpsAcc + 1
          newSecondTick = if calcFps then mod (secondTick + dt) 1000 else secondTick + dt

      -- React to input
      runSystem (handlePayload payload) world

      -- Update the world
      runSystem (step $ fromIntegral dt) world

      -- Set the background colour and clear the screen
      SDL.rendererDrawColor renderer $= V4 0 0 0 0
      SDL.clear renderer

      -- Render the world
      join $ runSystem (draw renderer newFps) world

      SDL.present renderer
      unless quit $ loop ticks newSecondTick newFpsAcc newFps

-- Begin looping
loop 0 0 0 0 
```

Enter fullscreen mode Exit fullscreen mode

您可以将它放在`main`函数内的 let 块中，也可以让它成为自己的东西。由你决定。重要的是你要明白，我们几乎无限地递归循环，直到用户关闭窗口(即，给 SDL`QuitEvent`)。如果有很多东西你不明白，不要担心，花点时间研究一下呈现给你的功能。

我本可以更好地命名变量，但是在本教程中，我实际上做了一个简单的 FPS 计数器。是的，它可能会更好，是的，可能有像这样的东西为你做这件事。我只是想说明滴答和时间是如何工作的。

### 打勾

一秒钟有 1000 个滴答(1 个滴答代表 1 毫秒)，而`ticks`函数允许我们查看自 SDL 初始化以来已经过了多少个滴答。我们可以用这个算出从前一帧开始的时间(`dt`或[*δ时间*](https://en.wikipedia.org/wiki/Delta_timing) )。这里，我用`secondTick`来计时秒，所以当一秒过去后`calcFPS`就是`True`。`newFPS`值将显示前一秒(`prevFps`)的 fps，或者显示存储在`fpsAcc`中的当前累计值+ 1。最后，跟踪秒数的计时器被重置。这有点难看，但这是早期跟踪 FPS 的一种非常简单和原始的方式，我们还没有渲染任何文本。让我们开始了解 Apecs 的基础知识。

## Apecs 组件

### 记忆类型

好的，有几行还没有编译，因为我们还没有实现它们。让我花点时间来解释一下[【运行系统】](https://hackage.haskell.org/package/apecs-0.5.0.0/docs/Apecs.html#v:runSystem)的作用。在 Apecs 中，`System`是一个单子，大部分代码都会在某个时候产生这个单子。我不打算谈论单子到底是什么，因为这是一个热门话题，但我可以展示他们能做什么。简单来说，`runSystem` ***做*** 的事情。它可以或者*会*执行副作用，可以一次产生多个副作用。`System`单子是你将如何操纵你的游戏。

这里回顾一下在 Haskell 中创建类型的[。如果我们想和哈斯克尔有所进展，我们需要这些。](https://wiki.haskell.org/Constructor)

*   **`type` :** 一个*类型的同义词*。你可以用你的类型所代表的内容来替换它，除此之外它什么也不做。除了类型所代表的内容之外，函数还可以接受类型，这只是一个别名。示例:

```
type String = [Char]
type LongThing = (Int, Int, Int, Int, Int) 
```

Enter fullscreen mode Exit fullscreen mode

*   **`data` :** 某*型建造师*。您可以从其他类型中创建自己的类型(想想其他语言中的类或结构)。接受或返回你的类型的函数只接受你的类型，你必须解构它来访问内容。示例:

```
data Bool = True | False
data Maybe a = Just a | Nothing
data Action = MoveTo (V2 Int) | Attack Entity | Cast Spell | Wait Int 
```

Enter fullscreen mode Exit fullscreen mode

*   **`newtype` :** 一个*类型构造器*，充当单个类型的包装器。您的类型将像您使用`data`时一样工作，但是更有效。如果你只是简单地把你的字体包在什么东西上，用这个。示例:

```
newtype PositionComponent = PositionComponent (V2 Double) 
```

Enter fullscreen mode Exit fullscreen mode

### 创建组件

现在，让我们创建一些简单的组件来操作。除了修改我们的`makeWorld`调用之外，检查下面的代码。注意，`V2`包含在`SDL.Vect`中，它会重新导出包 [`Linear`](https://hackage.haskell.org/package/linear) (也就是说，如果你使用的是 SDL，你不需要安装它自带的`Linear`。`Linear`有很多好看的类型，比如`V2`、`V3`和`V4`，去看看吧。)

```
-- Global component, exists outside of entities
-- Used for managing the passage of time
newtype Time = Time Double deriving Show
instance Semigroup Time where (<>) = mappend
instance Monoid Time where mempty = Time 0
instance Component Time where type Storage Time = Global Time

-- Unique component, either one or none exists
data Player = Player deriving Show
instance Component Player where type Storage Player = Unique Player

-- Position of game entities
newtype Position = Position (V2 Double) deriving Show
instance Component Position where type Storage Position = Map Position

-- Name of game entities
newtype Name = Name String deriving Show
instance Component Name where type Storage Name = Map Name

-- Uses template Haskell to create the data 'World'
-- also creates initWorld
makeWorld "World" [''Time, ''Player, ''Position, ''Name] 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有三个组件。第一个是`Time`，是一个`Global`组件。这意味着在任何时候都只有一个 ***，它不属于任何实体*** 。这是恰当的，因为时间是全球性的。虽然，它确实秘密地属于一个实体 Apecs(和许多其他实现)中的实体只是一个引用组件的整数。全局组件存储在实体 *-2* 上，不会干扰 apecs 的正常使用。

时间是一种新的类型——我们本想只使用一个`Double`,但是因为我们需要提供自己的 typeclasses 实例，所以我们需要`newtype`它，因为类型同义词没有自己的实现。我们实现了`Component`类型类，以便 apecs 知道它是哪种组件。你可以在这里看到不同种类的实体。它还需要实现`Semigroup`的实例(通常你可以只指定`mappend`，到目前为止它已经为我工作了！)，以及`Monoid`(指定“空”组件的样子，因为这是该组件从游戏开始时就存在的初始状态)。

第二，我们有`Player`组件。这种类型只有一个数据构造函数`Player`。这个组件的目的是简单的标记哪个实体是玩家- ***一次只能有一个唯一的实体，把它交给一个新的实体就把它从之前的*** 中去掉了。现在没什么要说的，但是以后会有作用的。

最后，我们还有`Position`。同样，这是一个新类型，这样我们就可以创建`Component` - *的实例，Apecs 中的每个组件都需要这个实例，所以尽量提供这些实例以防止[成为孤儿实例](https://wiki.haskell.org/Orphan_instance)。*尽管`V2`的实现如下..

```
data V2 a = V2 a a 
```

Enter fullscreen mode Exit fullscreen mode

..它仍然是单个数据，就像`(a,b)`也是单个数据一样(一个元组包含两个可能不同类型的字段)。

最后一点，注意`makeWorld`现在是如何包含组件的。这个模板 Haskell 将继承我们所做的，并允许我们的组件类型在 Apecs `World`中使用。这意味着在[发现的一切 Apecs。系统](https://hackage.haskell.org/package/apecs-0.5.0.0/docs/Apecs-System.html)现在将与你的类型兼容。

好吧，也许这是一个艰难的部分，也许不是。也许你正在学习一些东西，或者也许你对我以一种幼稚的方式做了一些事情感到不安。不管怎样，这段代码应该接近于编译，并且应该足以演示 Apecs 的一些基础知识。

## 系统

### 副作用在单子上

在像 Apecs 这样的[实体组件系统](https://en.wikipedia.org/wiki/Entity%E2%80%93component%E2%80%93system)中，*系统*是让世界运转的东西。通常在 C++或 C#中，我会将我的 ECS 与引擎中的一些东西分开，但使用 Apecs，我不得不将许多东西想象成一个全局组件。游戏的地图，纹理缓存和其他资产很可能会在这里显示为一个全局组件，如果你还没有以这种方式实现的话。

到本教程的这一步，我已经给出了没有给出太多灵活性空间的代码示例，我假设您已经理解了我的意思。不过，对于这个领域，我将稍微偏离主题，只是尝试展示 apecs 可以做些什么。我将提供可用的相关代码，但显然您选择做什么取决于您的游戏。这一节将会很长，并且有很多代码，但是如果你遵循并且理解了这个方法，我相信你可以将我给出的应用到你自己的代码中。

首先，一些用法。Apecs 有一个我已经提到过的功能，叫做[‘运行系统’](https://hackage.haskell.org/package/apecs-0.5.0.0/docs/Apecs.html#v:runSystem)。这个函数将执行单子中包含的副作用，因此去掉了`System`部分。这里有一个 IO 单子的例子:

```
main :: IO ()
main = do
  -- The following will be executed just like how
  -- runSystem would execute one of your systems
  putStrLn "Here's a basic print function!"

  -- The following will NOT be run..
  let funcThatProducesIOIO = pure $ putStrLn "Here's an IO(IO())!"
  foo <- funcThatProducesIOIO

  -- ..until it is called like so
  foo

  -- That was because 'foo' has type IO (), but this
  -- is simply the storage of the monad, not execution

  -- pure wraps the argument inside the monad, leading to
  -- IO (IO ()), which when bound to foo, creates type IO ()
  -- This means that one layer of IO has been executed but
  -- not the second.

  -- This pattern is dumb, binding something just to call it.
  -- This will work instead:

  join funcThatProducesIOIO 
```

Enter fullscreen mode Exit fullscreen mode

希望上面的代码有一些意义。因为主线程将执行 IO 以便实际上使程序发生一些事情，任何具有类型`IO ()`的东西都将被自动执行。最后一个例子是我想提到的，因为一些人问我一些关于单子的混淆。当我们使用`<-`绑定某个东西时，右边的单子被执行来产生里面的东西。例如，`IO (String)`会产生一个`String`(这可能是从键盘读入的？).这意味着，如果生成的东西是另一个单子，比如 IO()，左边的东西不会被执行，而`join`是一个很好的快捷方式，可以省去上面演示的绑定和执行。

我为什么要告诉你这些？因为不管你是否使用 SDL，你都会想要执行 IO 来使事物出现在屏幕上。如果你有一个产生 IO 的系统，比如一个渲染系统，那么你将需要使用`join`来执行包含在你获取的 monad 中的副作用。

### 易系统类型同义词

接下来，简单类型同义词。以下内容将使使用 Apecs 变得更加容易:

```
-- Easy type synonym for systems
type System' a = System World a 
```

Enter fullscreen mode Exit fullscreen mode

请记住，这是一个类型同义词，它实际上不会以任何方式影响您的代码。它只是让你不用打字，并且更容易阅读类型签名。所有的`System World a`的意思是，假设你的`makeWorld`函数中的世界被命名为`"World"`，这个系统在你的世界中运行，并能以某种方式做出改变。

### 编写初始化系统

如果我们向上滚动到我们的初始化代码，您会看到我们:

```
-- Do any initialisation here!
runSystem initialise world 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候编写我们的`initialise`系统了。这个系统只是作为我们游戏的启动，增加我们的玩家，做一些事情。我相信你能猜到你的游戏需要做些什么来设置。我需要提到的最后一件事是， [a 系统有以下类型](https://hackage.haskell.org/package/apecs-0.5.0.0/docs/Apecs-Core.html#t:SystemT) : `newtype SystemT w m a`。起初这可能没有任何意义，但是文档清楚地说明了系统中使用的单子`m`实际上与 IO 兼容。与 IO 有关的任何事情都可以作为系统的一部分来执行，这可能有助于调试。

我们走吧！这里有一个初始化系统的例子:

```
-- The type of this function is System' (), so when we open up
-- a 'do' block, we are allowing us to bind the contents of System' monads
-- as well as executing side effects of any other System' () easily.
initialise :: System' ()
initialise = do

  -- Make sure you use liftIO to get the following to compile,
  -- we are returning System' (), not IO (), hence the lift.
  -- Lift is used to lift a function producing a
  -- monad of one type (IO) into another (System'()).
  liftIO $ putStrLn "Let's do some IO just to prove we can!"

  -- Let's initialise the time monad, even though we don't
  -- really have to as Apecs handles that for us.

  -- 'set' produces a System' (), which means this line
  -- will be executed as part of this system.
  set global $ Time 0

  -- Another two System' ()s, this time we're creating entities
  newEntity (Player, Name "Lola", Position $ V2 0 0)
  newEntity (Name "Harold", Position $ V2 0 0) 
```

Enter fullscreen mode Exit fullscreen mode

请记住，我们正在查看 Apecs Hackage 文档上的函数[,以获取关于使用什么函数的所有灵感。我保证，当你掌握它的窍门时，它是相当不言自明的。确保您看到的是所有文档的最新版本。](https://hackage.haskell.org/package/apecs-0.5.0.0/docs/Apecs-System.html)

老实说，如果你了解这些是如何工作的，我们如何组成单子来执行副作用，以及 Apecs 如何允许我们从主线程运行这些系统来玩我们的游戏，事情会变得容易得多。如果没有，不要害怕，你会成功的。希望更多的例子会有所帮助。

### 编写步骤系统

回想一下，我们在主游戏循环中调用了以下代码:

```
-- Update the world
runSystem (step $ fromIntegral dt) world 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的第二个系统。这将比前一个更重要，因为这是你需要执行每一帧的每个系统的集合。注意我们如何调用这个`runSystem`——我们传递给它的系统内部有一些额外的参数。当参数被求值时，`step`将接受`dt`并求值为`System' ()`，正如你对任何编程语言的期望一样，传递`(1 + 1)`将等同于一个`Int`，正如函数:

```
(+) :: Num a => a -> a -> a 
```

Enter fullscreen mode Exit fullscreen mode

已经全面评估过了。

这意味着我们的新系统看起来如下:

```
-- Okay, so we have access to delta time now.
-- This allows us to animate or simulate stuff.
-- Notice how we can execute other systems inside
-- this system with no problems.
step :: Double -> System' ()
step dT = do
  moveCharacters dT
  rootPlayer

-- The function cmap below finds every entity with a Position
-- and rewrites it with new values - adding dT to the x and y values.
-- 'cmap' is another function producing System' ()
moveCharacters :: Double -> System' ()
moveCharacters dT =
  cmap (\(Position (V2 x y)) -> Position $ V2 (x + dT) (y + dT))

-- Apecs has a lot of magic involved, look at this:
rootPlayer :: Double -> System' ()
  cmap (\(Player, Position _) -> Position $ V2 0 0)

-- This iterates on all entities with both a Player component and
-- Position component, however, Player is unique, so this is only
-- applied to the player.

-- This is actually a bit dumb, as we're operating on the player twice,
-- so let's rewrite that first call:

-- Not is an Apecs Data Constructor used for the exclusion of a component.
-- We have to specify the type of Not as GHC cannot infer this on its own.
-- This will operate on entities without a Player component.
moveNonPlayerCharacters :: Double -> System' ()
moveNonPlayerCharacters dT = do
  cmap (\(Position (V2 x y), Not :: Not Player) -> Position $ V2 (x + dT) (y + dT)) 
```

Enter fullscreen mode Exit fullscreen mode

### Apecs 魔法配合系统

在上面的例子中，函数`cmap`允许我们读取组件并覆盖它们。还有很多工作要做，一旦你习惯了不同的方法，你将不得不称赞 Apecs 所做的大量工作。

Apecs 文档中任何接受或写入`Components`(或`c`，因为文档使用类型和魔法使事物多态)的函数都可以采取各种不同的形式。正如你在上面看到的，你可以像我们使用`(Player, Position _)`一样读取多个组件(注意，在这种情况下我们使用`_`来忽略参数，因为我们正在覆盖位置)。您实际上也可以写入多个组件！只要组件被生成为一个元组，就像刚才给出的例子一样，那么您也可以返回多个组件。这并不是说忽略任何输入组件就可以不管它们——我们不需要再次编写`Player`来在实体上保存它。

我现在就给你们举几个我能想到的例子。我会在每个片段之后谈论他们做什么。我将调用函数并使用我们还没有定义的组件，想法是这些只是为了演示使用 Apecs 的不同方法。

```
everyoneChasePlayer :: System' ()
everyoneChasePlayer = do
  [(Player, Position p)] <- getAll
  cmap (\(Position p', Not :: Not Player) ->
    Position $ moveTowards p' p) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，使用`do`语法，我们可以非常容易地将组件附加到`player`上。当列表中只有一个元素时，我们可以像元组一样对其进行模式匹配，但是如果`Player`不存在，或者如果您没有获取一个唯一的组件并因此拥有多个实体，这可能会使您的程序崩溃。通过获取玩家的位置，我们可以将其传递给一个名为`moveTowards`的函数，该函数负责操纵第一个参数，使其更接近第二个参数。这个函数是纯函数，因为`cmap`是产生`System' ()`单子的函数，所以你可以忘记 Apecs，在写它的时候专注于你的逻辑。

```
debugCharacters :: System' ()
debugCharacters = cmapM_ (\(Name n, Entity e) ->
  liftIO $ putStrLn ("Entity " ++ show e ++ " is named: " ++ n)) 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个简单的模板，可以用于调试。如前所述，`System' ()`支持`MonadIO`，允许你在里面执行`IO`。`cmapM`允许我们读取组件并逐个迭代(基本上我们可以将每次迭代视为另一次`System' ()`)，这允许我们执行副作用，而不是像前面的例子一样被锁定在一个纯环境中。如果我们在这里使用`do`，我们甚至可以调用另一个系统！

另外，注意我们是如何在这里找到`Entity`的——无论何时我们使用`getAll`或`cmap`或任何获得组件的东西(除了`get`，我们都可以为这些组件所属的`Entity`分配绑定。`Entity`只是`Int`的一个新类型，所以在这种情况下`e :: Int`，因此我们称之为`show`。`n`只是一个不需要使用`show`的字符串。如果我们想实际使用`Entity`，一定要将`Int`放回`Entity`中，或者简单地将实体抓取为`e :: Entity`，而不是像我们在这里做的那样进行模式匹配。

最后，我们使用的`cmapM_`函数(注意`_`)和`cmapM`一样，但是它不写任何东西。它的工作是读取并提供一种单值迭代的方法。如果我们想改变实体的名称，使用`cmapM`将允许我们从一元迭代中返回一些东西来编写实体。这里有一个用`cmapM` :
编写组件的例子

```
applyGravity :: Double -> System' ()]
applyGravity dT = cmapM (\(Position (V2 x y), e :: Entity) -> do
  let gY = y + (dT * 0.5)
      y' = max gY 0
  liftIO $ putStrLn ("Entity " ++ show e ++ " moved from " ++ show y ++ " to " ++ show y')
  pure $ Position (V2 x y')) 
```

Enter fullscreen mode Exit fullscreen mode

所以这里我们有一个使用`let`使计算更容易的例子，还有一些`IO`，得到`Entity`，最后覆盖`Position`组件来应用重力(在 SDL 和许多其他框架中，左上角是`(0,0)`，所以正的`y`向下移动)。

```
fireWeapon :: V2 Double -> Double -> System' ()
fireWeapon (V2 i j) -> speed = do
  [(Player, p :: Position, Weapon ammo, e :: Entity)] <- getAll
  when (ammo > 0) $ void $ do
    let ammoLeft = max 0 $ ammo - 1
    set e (Weapon ammoLeft)
    newEntity (Velocity (i * speed) (j * speed), p) 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个在制作`System' ()`的同时制作新实体的例子。当这个系统的副作用被执行时，我们开始用`Player`组件和一些其他组件抓住`Entity`。请记住，我们需要事先确保玩家拥有这些组件，否则模式匹配将会失败，我们只能在这里进行模式匹配，因为我们知道我们在这个列表中只有一个元素。

然后，我们将播放器上的`Weapon`组件设置为一个新值。无论实体上是否已经存在函数`set`都可以使用，因此它可能是比`cmap`更好的选择。此外，`set`要求您提供您正在修改的实体，这意味着如果我们不在一个唯一的实体上操作，那么`cmap`就不会这么好- `cmap`遍历每个具有匹配组件的*实体。你可以用`set`进行更多的控制，只要确保在你可以使用`cmap`或任何其他 Apecs 函数时，你没有浪费时间。*

 *最后，注意我们对`when`和`void`的使用。函数`when`由`Control.Monad`导出，相当于下面的:

```
when' :: Bool -> m () -> m ()
when bool func =
  if bool
    then func
    else pure () 
```

Enter fullscreen mode Exit fullscreen mode

基本上，`when`逻辑为假的时候就会做 ***无*** 。还有 when 的反义词，叫做`unless`。另一个函数，`void`将执行传递给它的单子的副作用，但是丢弃返回值。当我们使用`newEntity`创建一个新的实体时，创建的实体作为`System' (Entity)`返回。因为我们希望这个函数返回一个空白的`System' ()`，所以我们只需要忽略返回的内容(不要担心，实体不会被删除)，这样就可以对整个类型进行检查。

### 编写系统事件处理程序

好的，这部分将开始结合 SDL 电码和 Apecs。有很多关于如何使用 SDL 的指南，所以我将先展示代码，然后再提及任何重要的东西。请记住，SDL 会给你一份自上次投票以来发生的所有事件的列表，你需要单独处理它们。我还没有实现任何代码来检查像`shift`或`ctrl`这样的修饰键。这将使用前一节中介绍的许多技术，因此希望您可以轻松地跟上。

```
-- Global component used for changing gamestates
data GameMode = Standard | Look deriving (Show, Eq)
data GameState = Game GameMode | Interface deriving (Show, Eq)
instance Semigroup GameState where (<>) = mappend
instance Monoid GameState where mempty = Game Standard
instance Component GameState where type Storage GameState = Global GameState

-- Handle the entire event payload
handlePayload :: [EventPayload] -> System' ()
handlePayload = mapM_ handleEvent

-- The main event handler function for dealing with keypresses
handleEvent :: EventPayload -> System' ()
handleEvent (KeyboardEvent ev) = handleKeyEvent ev
handleEvent _ = pure ()

-- For the handling keyboard events only
handleKeyEvent :: KeyboardEventData -> System' ()
handleKeyEvent ev = do
  (state :: GameState) <- get global
  let code = keysymKeycode $ keyboardEventKeysym ev
  case keyboardEventKeyMotion ev of
    Pressed ->
      case state of
        Game mode -> gameAction mode code
        Interface -> postMessage "Interface state not implemented yet"
    Released -> pure ()

-- For keyboard events that  take place in the game
gameAction :: GameMode -> Keycode -> System' ()
gameAction mode k =
  let intents = lookup k defaultGameIntents in
    case mode of
      Standard ->
        case intents of
          Just (Navigate dir) -> navigate dir
          Just ToggleLook -> toggleLook mode
          Just Wait -> do
            postMessage "You wait.."
            playerActionStep 100
          _ -> pure ()
      Look ->
        case intents of
          Just (Navigate dir) -> moveReticule dir
          Just ToggleLook -> toggleLook mode
          _ -> pure ()

-- Initial bindings for intents
defaultGameIntents :: [(Keycode, GameIntent)]
defaultGameIntents =
  [ (KeycodeUp , Navigate C.Up)
  , (KeycodeLeft , Navigate C.Left)
  , (KeycodeDown , Navigate C.Down)
  , (KeycodeRight , Navigate C.Right)
  , (KeycodeSemicolon, ToggleLook)
  , (KeycodeW, Wait)
  ] 
```

Enter fullscreen mode Exit fullscreen mode

我首先创建了一个名为`GameState`的组件，也许这不是最好的名字。这个`Global`组件基本上是要帮助我们计算出屏幕上内容的上下文。例如，我们在箭头键上的输入可能会移动游戏中的玩家，但在菜单上它会做别的事情。我还没有做任何界面的东西，但我有两个不同的实际游戏模式。我在做一个类似 Roguelike 的 RPG，一个要求是玩家可以不动不动的看着东西，不与任何东西互动。这个组件允许我首先看到我们是否在游戏中，其次，如果我们在游戏中，我们在做什么。这些东西很可能会改变以适应你正在制作的游戏，但是我希望本教程的目的是让你明白我的意思。

`handlePayload`函数本身从获取整个有效载荷并对其执行`mapM_`开始- `mapM_`不是 Apecs 函数，而是`Control.Monad`中的函数。如果您遵循了前面的`cmapM_`函数，这可能已经很清楚了。这个函数非常类似，除了它不是遍历 Apecs 组件，而是遍历您传递给它的任何一个`Traversable`类，在本例中是一个 list。我们使用的是 map 的`mapM_`版本，因为普通的`map`函数不能给我们干净地完成这项工作所需的一元上下文，而`mapM_`允许我们为列表中的每个元素生成一个`System' ()`，并且`_`简单地丢弃任何不是副作用的数据，因为我们没有使用任何东西。这将我们带入`handleEvent`函数，它现在只需要担心一个`SDL.Event`。

我写的`handleEvent`系统对给定的事件类型进行简单的模式匹配。如果事件是一个`KeyboardEvent`，它被打开以暴露事件本身，然后被传递给`handleKeyEvent`。我还没有考虑其他的`SDL.Event`，因为我们的主循环已经处理了对`QuitEvent`的检查，这意味着所有我认为重要的事情都已经被处理了。也许你想在调整屏幕大小时或者点击鼠标时发生一些事情，你应该去查看一下[文档](https://hackage.haskell.org/package/sdl2-2.4.1.0/docs/SDL-Event.html#t:EventPayload)以找到你可能需要的事件类型，以及这些事件包含的数据。

我们的工作是引导程序流向下一步需要去的地方。现在，不同的游戏会做不同的事情。我的游戏对任何关键版本都不感兴趣，所以如果`keyboardEventKeyMotion ev`的类型与`Released`匹配，我什么也不做。否则，我调用自己的函数，`gameAction`以及当前模式和键码。请记住，在这些函数中的任何地方，您都可以调用任意数量的`System' ()`函数来组成单子。Apecs 允许我们保持代码的整洁和健康，同时也给了我们空间去做我们想做的任何事情，只要我们把`System' ()`构造成输出，正如我们所知，当你知道如何做时，这真的很容易。

### 书写抽签制度

回想一下，draw 系统与`IO`单子交互。我已经展示了如何使用`liftIO`从`System' ()`中执行`IO`，但是我也展示了当使用`join`函数(在`Control.Monad`中找到)时，如何使用返回的`System (IO ())`进行绘制。你应该有足够的信息来启动一个基本的绘图系统。

你将在早期面临的最困难的挑战之一是管理你的纹理、字体和其他资产。创建一个`Texture`组件并赋予每个`Entity`它将要呈现的纹理是很容易的，然而，这意味着显示同一事物的五个实体将有相同的纹理被加载五次。

我已经为`Textures`和`Fonts`创建了`Global`组件，它们实现了`HashMap`(在`Data.HashMap`中找到)。一个`HashMap`就像一个普通的地图，但是它运行起来要快得多，因为它不是有序的。然后，我的系统将这些组件中的资产传递给任何需要它们的功能。当然，你可以在主循环中使用`IO`并将所有内容传递给`draw`系统，以避免多次加载，但这意味着要一次加载所有资产。最终，我计划扫描我所有的`Sprite`组件，并根据所需的纹理加载/卸载散列表中的资产。下面是当前代码:

```
-- Turns a list of key value pairs into a hashmap for a resource component
createResourceMap :: [(String, a)] -> HM.Map String a
createResourceMap = foldl (\m (k, v) -> insert k v m) empty

-- Types for creating textures
type TexResource = (String, Texture)
type TextureMap = HM.Map String Texture

-- Create a TextureMap with initial filepaths
loadTextures :: Renderer -> [FilePath] -> IO [TexResource]
loadTextures r = traverse getTex
  where getTex p = do
          tex <- loadTexture r p
          pure (p, tex)

-- Types for creating fonts
type FontResource = (String, Font)
type FontMap = HM.Map String Font

-- Create a FontMap using initial filepaths
loadFonts :: [(FilePath, PointSize)] -> IO [FontResource]
loadFonts = traverse getFont
  where getFont (p, s) = do
          font <- load p s
          pure (p, font)

-- Global store of all textures
newtype Textures = Textures TextureMap
instance Component Textures where type Storage Textures = Global Textures
instance Semigroup Textures where (<>) = mappend
instance Monoid Textures where mempty = Textures HM.empty

-- Global store of all fonts
newtype Fonts = Fonts FontMap
instance Component Fonts where type Storage Fonts = Global Fonts
instance Semigroup Fonts where (<>) = mappend
instance Monoid Fonts where mempty = Fonts HM.empty

-- Create System' (IO ()) for everything depending on item drawn
draw :: SDL.Renderer -> Int -> System' (IO ())
draw renderer fps = do
  Textures texs <- get global
  Fonts fonts <- get global
  let uiFont = HM.lookup "Assets/Roboto-Regular.ttf" fonts
  sequence_ <$> sequence
    [ drawComponents $ renderSprite renderer texs
    , drawComponents $ renderReticule renderer
    , displayFps renderer fps uiFont
    ]

-- Produce a system used for drawing
drawComponents :: Get World c => (c -> Position -> IO ()) -> System' (IO ())
drawComponents f = cfold (\img (p, comp) -> img <> f comp p) mempty

-- Render textures
renderSprite :: SDL.Renderer -> TextureMap -> Sprite -> Position -> IO ()
renderSprite r ts (Sprite fp rect) (Position p) =
  case HM.lookup fp ts of
    Just tex -> SDL.copyEx r tex (Just $ toCIntRect rect) (Just (SDL.Rectangle (P $ toCIntV2 p) tileSize')) 0 Nothing (V2 False False)
    _ -> pure ()

-- Render the target reticule
renderReticule :: SDL.Renderer -> Reticule -> Position -> IO ()
renderReticule r (Reticule on) (Position p)
  | not on = pure ()
  | on = do
    rendererDrawColor r $= V4 255 255 255 20
    fillRect r $ Just $ Rectangle (P $ toCIntV2 p) tileSize'

-- Display FPS
displayFps :: SDL.Renderer -> Int -> Maybe SDL.Font.Font -> System' (IO ())
displayFps r fps Nothing = pure $ pure ()
displayFps r fps (Just f) =
  pure $ renderSolidText r f (V4 255 255 255 255) ("FPS: " ++ show fps) (V2 0 0) False

-- Render solid text
renderSolidText :: SDL.Renderer -> SDL.Font.Font -> SDL.Font.Color -> String -> V2 Double -> Bool -> IO ()
renderSolidText r fo c s p = renderText r fo (SDL.Font.solid fo) c s (toCIntV2 p)

-- Render text to the screen easily
-- renderSolidText calls this
renderText :: SDL.Renderer -> SDL.Font.Font -> (SDL.Font.Color -> Data.Text.Text -> IO SDL.Surface) ->
           SDL.Font.Color -> String -> V2 CInt -> Bool -> IO ()
renderText r fo fu c t (V2 x y) center = do
  let text = Data.Text.pack t
  surface <- fu c text
  texture <- SDL.createTextureFromSurface r surface
  SDL.freeSurface surface
  fontSize <- SDL.Font.size fo text
  let (w, h) = (fromIntegral *** fromIntegral) fontSize
  unless center $
    SDL.copy r texture Nothing (Just (Rectangle (P $ V2 x y) (V2 w h)))
  when center $ do
    let x' = x - fromIntegral (fst fontSize `div` 2)
    SDL.copy r texture Nothing (Just (Rectangle (P $ V2 x' y) (V2 w h)))
  SDL.destroyTexture texture 
```

Enter fullscreen mode Exit fullscreen mode

> 这段代码只是一个粗略的草案，以便我可以看到屏幕上的东西。虽然我建议初学者使用它，这样他们就可以开始享受 Haskell 带来的乐趣，但我 ***强烈鼓励*** 你尽可能地进行优化，让一切都适合你的游戏。函数的效率非常低，所以在创建纹理之前，我会试着把所有的文本都放到一个表面上。这个码是 ***不好*** 。

天哪，这么多代码。我很抱歉只是倾倒了这么多，但同时没有更好的方式来说明我如何尝试做事。对于阅读本文的初学者来说，这可能非常复杂和难以理解，但我能说的是，您应该从查看每个代码块的函数类型开始，并理解这些函数如何相互使用。最重要的是，看看我们如何从生产`renderText`和`renderSolidText`中的`IO ()`到捆绑`displayFPS`或`drawComponents`生产`System' (IO ())`。`drawComponents`函数简单地使用`cfold`将所有的`IO ()`单子组合成一个，然后像所有其他 Apecs 函数一样将`System' ()`包装起来。

## 结论

我为大量的代码粘贴感到抱歉，但希望这篇博客帖子中至少有一些东西可以教人一些东西。我真正的意图是鼓励使用 Haskell 来创建小游戏，我们不再像过去那样需要高性能。希望这能激励更多的程序员和更多的框架使用 Haskell，这样用函数式语言编写就不那么陌生了。我希望看到独立开发者开始拥抱高级语言的力量，让他们本已困难的生活变得更容易。如果人们可以用 JavaScript 做出[酷的游戏，那么 Haskell 为什么不行？](https://store.steampowered.com/app/497800/Golden_Krone_Hotel/)*