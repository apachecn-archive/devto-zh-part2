# 电子托盘应用:如何在气球点击事件的事件处理程序中引用目标气球？

> 原文：<https://dev.to/jochemstoel/electron-tray-application-how-to-reference-target-balloon-in-event-handler-of-balloon-clicked-event-4i9k>

电子*托盘*应用程序可以使用*托盘.显示气球*方法显示气球，该方法采用一个带有*标题*、*内容*和可选*图标*属性的对象。

据我所知，气球接口不包括 click 或 onClick 属性。*托盘*类确实有一个*气球点击*事件，当一个气球被点击时触发，还有一个*气球关闭*和*气球显示*事件。然而，传递给回调函数的*事件*对象似乎不包含对调度事件的气球的引用。(显示、点击或关闭的气球)

```
tray.on('balloon-click', console.log)
tray.on('balloon-show', console.log)
tray.on('balloon-closed', console.log) 
```

Enter fullscreen mode Exit fullscreen mode

日志:

```
 { preventDefault: [Function: preventDefault],
      sender:
       Tray {
         _events:
          { 'balloon-click': [Function],
            'balloon-show': [Function: consoleLog] },
         _eventsCount: 2 } } 
```

Enter fullscreen mode Exit fullscreen mode

我的托盘应用程序从不同的来源发送气球通知。当你点击气球时会发生什么(导航到哪里或者打开哪个窗口)取决于气球在说什么。

我需要一个被点击的气球的引用，而不仅仅是在某个时候某个地方有一个气球点击。它强烈期望这是存在的。

我可以想出许多方法来实现一个解决方案，从简单的包装函数和一个 activeBalloon 变量到整个 BalloonManager 类，但是如果我在[电子托盘 API 文档中忽略了这一点，我不想浪费时间。](https://electronjs.org/docs/api/tray)

> 2017 年 8 月在 discuss.atom.io in 上也问过这个问题

来源:[[https://discuse . atom . io/t/tray-balloon-click-how-to-know-the-balloon-is-clicked/46744][2](https://discuss.atom.io/t/tray-balloon-click-how-to-know-which-balloon-is-clicked/46744%5D%5B2)]