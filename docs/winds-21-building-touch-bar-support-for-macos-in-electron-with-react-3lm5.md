# Winds 2.1:用 React 为 macOS 构建 Touch Bar 支持

> 原文：<https://dev.to/nickparsons/winds-21-building-touch-bar-support-for-macos-in-electron-with-react-3lm5>

[![Winds 2.1](../Images/1af8eac1b341e14ac98a9490884026be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nrvW9789--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mg33pmd30pyrzcpuxoxs.png)

最近，科技/硬件领域最新、最酷的创新之一是苹果去年在 MacBook Pro 上发布的 touch bar。随着这些机器变得越来越受欢迎，越来越多的应用程序正在利用 touch bar 以一种新的方式与用户进行交互。随着我们观察这一趋势的发展，我们认为这似乎是一个有趣的挑战，因为我们过去几个月的宠物项目 Winds 2.0 在本月早些时候首次亮相。

[![Winds](../Images/46afd0f7d1ec1c9690a2cafa789152eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zqs1IE5T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/JzDcF64.gif)

随着我们根据社区的反馈继续对 Winds 进行迭代，现在似乎是在 Winds 2.1 中推出对 macOS 触摸条控制支持的最佳时机。走进去，似乎是小菜一碟。然而，我们大错特错了。macOS 和 electronic 之间的 API 通信还远远没有完成。希望在不久的将来，我们能看到 macOS 和 electronic 之间更好的支持。

目前，我们已经想出了一个不错的解决方案，允许我们在 Electron 和 macOS touch bar 之间进行双向通信。为此，我们非常依赖三个主要的电子元件:

*   由电子公司提供的(有限的)触摸条 API
*   ipcMain 模块，处理从渲染器进程(网页)发送的异步和同步消息
*   ipcRenderer 模块，它提供了一些方法，允许您从呈现器进程(网页)向主进程(ipcMain)发送同步和异步消息。

在本帖中，我们将深入探究我们是如何完成这项任务的。让我们开始吧。

# IPC main 模块

ipcMain 模块是 EventEmitter 类的一个实例。当在主进程中使用时，它处理从呈现器进程(网页)发送的异步和同步消息。从渲染器发送的消息被发送到该模块，由事件处理程序拾取，然后传递给函数进行进一步处理。

## 发送&接收来自电子

在 [/app/public/electron.js](https://github.com/GetStream/Winds/blob/master/app/public/electron.js) 中，一旦窗口准备好显示，我们就初始化下面的代码:

```
ipcMain.on('media-controls', (event, args) => {
    mediaControls(event, args);
}); 
```

Enter fullscreen mode Exit fullscreen mode

事件属性指定发生了什么，而参数可以是单个值或键值对的对象。对于 Winds，我们选择使用一个对象，这样我们就可以传递额外的元数据(来自前端)，比如当前的剧集标题和播客名称。

# IPC renderer 模块

ipcRenderer 模块是 EventEmitter 类的一个实例。它提供了一些方法，允许您从呈现器进程(web 页面)向主进程(electronic)发送同步和异步消息。

理解通信如何工作是我们获得媒体控制支持的第一步。为了更好地理解它的工作原理，我们来看几个简短的代码示例:

## 发送&接收来自反应

在[/app/src/components/player . js](https://github.com/GetStream/Winds/blob/master/app/src/components/Player.js)中，我们使用 window.ipcRenderer，因为 ipcRenderer 不是直接可用的，因此需要我们将其从窗口对象:

```
window.ipcRenderer.send('media-controls', {
    type: 'play',
    title: `${episode.title} - ${episode.podcast.title}`,
}); 
```

Enter fullscreen mode Exit fullscreen mode

**和**

```
window.ipcRenderer.send('media-controls', {
    type: 'pause',
}); 
```

Enter fullscreen mode Exit fullscreen mode

因此，说了这么多，做了这么多，我们可以使用播放器上下文来区分正在播放的剧集和暂停的剧集。大概是这样的:

```
if (isElectron()) {
    if (context.playing) {
        window.ipcRenderer.send('media-controls', {
            type: 'play',
            title: `${episode.title} - ${episode.podcast.title}`,
        });
    } else {
        window.ipcRenderer.send('media-controls', {
            type: 'pause',
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 对生命周期事件做出反应

在`componentDidMount()`上，我们使用下面的处理程序来确保我们的输入事件被拾取。

> 注意:我们通过 is-electron 节点模块将我们的代码包装在电子支票中，以确保我们只在电子环境中执行它——这很重要，因为我们有应用程序的 web 和本地版本。

```
componentDidMount() {
    if (isElectron()) {
        window.ipcRenderer.on('media-controls', this.incomingMediaControls);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`componentWillUnmount()`上，我们使用下面的处理程序来确保所有的监听器都被销毁:

```
componentWillUnmount() {
    if (isElectron()) {
        window.ipcRenderer.removeAllListeners('media-controls', this.incomingMediaControls);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 电子触摸条 API

正如本文前面部分所指出的，我们在我们的 electron.js 文件中初始化 ipcMain。但是等等，还有更多...除了切换触摸栏图像和处理触摸栏事件之外，我们还有一部分代码专门用于处理传入(和传出)消息:

```
mediaControls = (event, args) => {
    let next = new TouchBarButton({
        icon: `${__dirname}/static/next.png`,
        click: () => {
            event.sender.send('media-controls', 'next');
        },
    });

    let previous = new TouchBarButton({
        icon: `${__dirname}/static/previous.png`,
        click: () => {
            event.sender.send('media-controls', 'previous');
        },
    });

    let playPause = new TouchBarButton({
        icon: `${__dirname}/static/pause.png`,
        click: () => {
            event.sender.send('media-controls', 'togglePlayPause');
        },
    });

    let info = new TouchBarLabel({
        label:
            args.title && args.title.length > 40
                ? `${args.title.substr(0, 40) + '...'}`
                : args.title,
        textColor: '#FFFFFF',
    });

    if (args.type === 'play') {
        playPause.icon = `${__dirname}/static/pause.png`;
        info.label = args.title;
    } else {
        playPause.icon = `${__dirname}/static/play.png`;
    }

    let touchBar = new TouchBar([
        previous,
        playPause,
        next,
        new TouchBarSpacer({ size: 'flexible' }),
        info,
        new TouchBarSpacer({ size: 'flexible' }),
    ]);

    mainWindow.setTouchBar(touchBar);
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个函数应该放在 main.js 文件中，或者在我们的例子中，放在 [electron.js](https://github.com/GetStream/Winds/blob/master/app/public/electron.js) 文件中。

# 最终产品

所有这些都为 Winds 2.1 提供了一个非常棒的 touch bar 功能，允许我们的用户在 Winds 中暂停和播放播客剧集，查看当前正在播放的播客，并向前和向后搜索。

[![Touch Bar](../Images/7b413af63c30a1f77131defee240ca0d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XLRWyq8p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/KlvfuVP.jpg)

随着我们继续开发应用程序和[收到来自我们令人敬畏的社区的反馈](https://github.com/GetStream/Winds/issues)，我们希望继续为用户添加与 touch bar 互动的新方式，并让人们对他们与 Winds 的每次互动感到惊喜。

如果你认为我错过了什么，请在下面的评论中留言，或者在 Twitter 上找到我-[@ nick Parsons](https://twitter.com/nickparsons)。