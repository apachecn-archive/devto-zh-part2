# Spotify on Workflow

> 原文：<https://dev.to/havardh/spotify-on-workflow-4fo6>

应用程序模块是[工作流程](https://github.com/havardh/workflow)的核心。它们是工作流内部和平台上运行的实际应用程序之间的重要粘合剂。如果你是第一次接触`workflow`，那么[介绍帖](https://dev.to/havardh/introducing-workflow-18h6)是一个很好的起点。

*更新了代码样本以支持[工作流@ 2 . x](https://github.com/havardh/workflow/commit/ca77860)T3。*

[![spotify code sample](img/a696d3357610a01c69c41854ab4adfd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x3luI2Iv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lbgcph0q626fo28jh388.png)

这篇文章将通过一个支持 Spotify 的扩展工作流的实际例子来指导你。工作流是跨平台的，不同平台的实现是不同的。如果你需要让一个 app 在某个特定的平台上运行，其他平台可以跳过。这篇文章将展示如何为`osx`和`i3`编写应用程序。

### 运行示例

作为本帖中的一个运行示例，我们将为 Spotify 创建一个新的应用程序组件。Spotify 不属于之前定义的`workflow`支持的应用类别，即终端、浏览器和文本编辑器。因此，让我们为 Spotify 应用程序的初始版本挑选一个简单的用例。Spotify 定义了一个可以用于自动化的`uri`规范。让我们使用`uri`打开带有给定播放列表的 Spotify。

### 初始化应用程序

为了开始编写应用程序，有一个名为`create-workflow-app`的`npm`初始化器。让我们用`npx`命令行界面运行它。

```
npx create-workflow-app workflow-app-spotify 
```

这将生成一个在终端打开`emacs`的示例应用程序。三个值得注意的文件是`flows/Example.js`、`cli.js`和`src/index.js`。`cli.js`实现了一个简单的工作流配置，让我们测试我们的应用程序，默认情况下，它将使用`workflow-wm-terminal`。设置`yarn example`命令来运行`flows/Example.js`。

### 将我们的示例编写成代码。

让我们通过实现示例来开始定义我们的接口。`spotify` cli 带有一个`uri`参数，这个参数的一个变体允许我们指定一个播放列表。`uri`的格式为`spotify:user:<username>:playlist:<playlist-id>`。所以让我们定义一个使用 jsx 规范的例子。

```
<Spotify minimized play>
  <Playlist user={'<username>'} id={'<playlist id>'} />
</Spotify> 
```

我们已经给了我们的顶级组件`Spotify`一个名为`minimized`的参数，这将导致 Spotify 应用程序最小化启动。我们给了它一个孩子来指定要打开的播放列表。该组件具有`username`和`playlist id`属性。`Spotify`组件上的`play`道具会触发自动播放。

### 应用程序支架

在`src/index.js`文件中，我们有为`workflow`制作任何应用程序的支架。以下属性对于任何应用程序都是必需的。

```
const Spotify = {
  type: 'app',
  name: 'Spotify',
  params: ['minimized', 'play'],
  open: ({minimized, play}, context, children) => {
    // code for the app
  }
}; 
```

`workflow`使用`type`属性来区分 app 节点与`layout`和`workspace`节点。`name`属性用于调试信息，并向`wm`适配器层公开。`params`用于验证传递给`open`函数中节点的参数。`open`功能负责打开应用程序，并确保它被放置在屏幕上的预期位置。`open`函数的参数是流中节点的*参数*，特定于底层平台和 windows 管理器的*上下文变量*，以及传递给节点的任何*子节点*。应用程序本身可以自由定义允许的子对象和参数的规范。

### 支持工作流-wm-i3

让我们从添加对 i3 窗口管理器的支持开始。i3 窗口管理器由`context = {platform: "linux", wm: "i3"}`标识。它要求应用程序定义一个名为`xClass` [1]的附加属性。对于 Spotify 来说，这简直就是`Spotify`。open 函数应该返回一个 shell 命令，执行该命令可以打开应用程序，这是针对 i3 的。`workflow-wm-i3`将基于`xClass`生成一个`layout tree`，它将在打开时匹配各种应用程序[2]。

```
const Spotify = {
  xClass: 'Spotify',
  open: ({ minimized, play }, context, children) => {
    if (children.length !== 1) {
      throw new Error('Spotify does not support more or less than one child node');
    }

    const [child] = children;
    const uri = child.open(child, context, child.children);

    return `spotify --uri='${uri}' &`;
  }
}; 
```

我们还需要定义`Platform`子节点[3]。在平台节点内部，我们构建了一个 uri，`spotify`节点将把它返回给`workflow-wm-i3`。这种设计让我们可以轻松地添加新类型的子节点，spotify 节点将调用这些子节点。

```
const Platform = {
  type: "app",
  name: "Platform",
  params: ["user", "id"],
  open: ({user, id}, context, children) => {
    return `spotify:user:${user}:playlist:${id}`;
  }
}; 
```

这就是添加对在`workflow-wm-i3`下运行的 spotify 的支持所需要的一切。

**注意**上面的例子实际上并没有触发 linux 上的自动播放。如果你弄清楚如何激活它，请看一下[这期](https://github.com/havardh/workflow/issues/155)。

### 支持`workflow-wm-osx`

OSX 的整合遵循了一种更加标准化的为`workflow`编写应用程序的方法。`workflow-wm-osx`将调用每个应用程序的 open 函数，并将参数传递给应用程序和屏幕上的绝对位置。应用程序负责打开应用程序，并将其定位在给定的位置。这通常是用 JXA [4]来完成的。为了方便起见，`workflow-wm-osx`将通过`context`参数传递一个名为`run`的函数，该函数可用于执行`JXA`代码。下面给出了 psudo 实现的基本框架。

```
const Spotify = {
  open: async (app, context, children) => {
    const uri = getUri(children, context);

    await context.run(({ minimized, play, position }, uri) => {
      const spotify = Application("Spotify");

      spotify.activate();

      const window = spotify.windows[0];
      window.bounds = app.position;

      spotify.playTrack(uri);
    }, app, uri);
  }
}; 
```

现在，关于上面的代码，最值得注意的是对`run`函数的调用。这将调用 [`@jxa/run`](https://github.com/JXA-userland/JXA/tree/master/packages/%40jxa/run) ，它用`osascript`执行函数参数并返回一个承诺。这意味着传递的函数不能是闭包，必须只引用它的参数和由`osascript`环境提供的上下文。代码打开`spotify`并将`window`的位置从`app`设置到`position`。Spotify 特定功能`playTrack`用于启动播放列表。

Spotify 应用程序上可用的 api 可以在 OSX 的`Script Editor`应用程序中找到。可以为 api 生成类型脚本定义，查看[这个](https://github.com/JXA-userland/JXA/pull/8#issuecomment-419169843)开始。

现在，让它在 OSX 上正常工作的实际代码有点复杂。检查工作版本的[源代码](https://github.com/havardh/workflow/blob/master/packages/workflow-app-spotify/src/index.js#L47)。

## 脚注

[1]:这是由`xprop`实用程序找到的 X11 `WM_CLASS`。

[2]:这种简单化的实现导致了[这个](https://github.com/havardh/workflow/issues/156) bug。

[3]:现在这些节点将使用类型`layout`或`app`。其中，`layout`节点用作定位其他节点的支持节点，`app`节点表示屏幕上可见的东西。

[4]: JXA，或用于自动化的 Javascript，使用 Javascript 编写自动化脚本的 OSX 方式