# published: false 的报道供奉( 2018/05 版)

> 原文：<https://dev.to/jgs/published-false--201805--1k06>

忙得团团转，只写了开头而没有写完的报道一直开在 Chrome 的标签上，但是过了很久不想写了，所以要统一放流供奉。

* * *

# 从 Slack 开始说谷歌主页

3 月初买了 Google Home Mini。 在智能音箱开始流行的时候，大家都想要那么会说话的音箱吗？ 虽然很怀疑，但是试着买了一台。 在我家，因为担任会说话的收音机等职务很方便，所以增加了一台，组成了起居室和卧室两台。

话说回来，说来也太没用了，我妻子有一个习惯，就是用社交网络联系我，听到通知声，也只是想着“你在说什么吧——”而不确认内容。 本人也有这种意识，因为有了“买了谷歌主页什么的想表达的时候，只要谷歌主页说话就没什么可问的(意译)”的订单，所以决定努力做星期天的木匠。

我查了很多，就像[Google Home 的说话方式](https://scrapbox.io/smart-home/Google_Home%E3%81%AE%E5%96%8B%E3%82%89%E3%81%9B%E6%96%B9)中写的那样，和 Google Home 在同一个网络中如果没有什么司令塔的话好像就说不出来。

在那里，我买了很久没买到的 Raspberry Pi。 我也粗略地查了一下，买了开关科学的启动工具包([https://www.switch-science.com/catalog/3559 /](https://www.switch-science.com/catalog/3559/))。 只要好好组装扎一下，Linux 就会站起来有点感动。 只是，我家在放置 HDMI 显示器的位置没有可以使用的 LAN 电缆，运用的时候想放在别的位置用有线，所以用无线做了最初的设置，一直做到可以用 ssh 登录的程度，然后顺势用有线切换，有点诡计多端 一边祈祷一边连接 ssh 的时候也有点感动。

赶紧安装 Node.js，在前文报道中介绍的[https://github.com/noel Portugal/Google-home-notifier](https://github.com/noelportugal/google-home-notifier)中探索情况 试着说一下

```
Please fix your application to use the native API of Avahi! 
```

Enter fullscreen mode Exit fullscreen mode

这样的 WARN，调查后发现[https://github.com/thkl/homebridge-home matic/issues/41](https://github.com/thkl/homebridge-homematic/issues/41)好像很辛苦。

暂且不谈，因为`google-home-notifier`有不说日语的问题，读了代码后没做那么大的事，所以决定自己重新安装。

[https://github.com/e-jigsaw/google-home-notifier](https://github.com/e-jigsaw/google-home-notifier)

* * *

写到这里我已经筋疲力尽了。 现在我家从 Slack 转移到了 Telegram，所以 Slack 的系统停止了。

* * *

# 根据 Electron 的 APP 在 TouchBar 上制作 UI

全新调制了 MacbookPro，心不在焉地想着搭载了 TouchBar 能不能做点什么，结果得知搭载了可以在 Electron 上操作 TouchBar 的 API，所以试着碰一下。 用 Chrome Extension 在特定页面上在 TouchBar 上发布东西变得方便的话会很有趣。

[https://github . com/electron/electron/blob/master/docs/API/touch-bar . MD](https://github.com/electron/electron/blob/master/docs/api/touch-bar.md)

一边读着这一带的 Doc。

```
// ...
const touchBar = new TouchBar([
  new TouchBarLabel({
    label: 'test'
  })
])
// ...
mainWindow.setTouchBar(touchBar)
// ... 
```

Enter fullscreen mode Exit fullscreen mode

只要用这样的方式写就能显示出标签。

```
 new TouchBarButton({
    label: '➡️',
    click: () => {
      console.log('yo')
    }
  }) 
```

Enter fullscreen mode Exit fullscreen mode

这样就做成了可以做`yo yo`的按钮。

```
 new TouchBarScrubber({
    items: [{
      label: 'foo'
    }, {
      label: 'bar'
    }],
    select: index => console.log(index),
    showArrowButtons: true,
    selectedStyle: 'background'
  }) 
```

Enter fullscreen mode Exit fullscreen mode

选择器是这样的。

* * *

这个也在这里用尽了。 本来 TouchBar 周围的文档就很少，必须马上挖源代码，或者说还没有制作出期望的 UI，或者说系统。

Electron 生产的 APP 并行开发了 2 个左右，但都没有占用太多时间，没有进展...

* * *

我觉得有更多的草稿，在 dev.to 上写的是这两条。
想更加持续地配送输出...