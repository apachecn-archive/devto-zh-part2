# iOS 中的音量 HUD 怎么调？

> 原文：<https://dev.to/itscoderslife/how-to-tweak-the-volume-hud-in-ios-57ja>

iOS 音量变化 HUD 是一个巨大的不可交互的圆角矩形弹出窗口。这阻碍了用户界面 2-3 秒。这激怒了我。因此，如果有任何要开发的媒体应用程序，请使用此技术来定制音量变化反馈 UI。

现在，如果你正在开发一个应用程序，需要非常少的恼怒用户或沮丧用户，那么永远不要在屏幕中央使用反馈 hud，也不要超过 2-3 秒。

现在一个默认的 HUD 是音量 HUD，它覆盖了所有的应用程序。但好的一面是你可以定制它。

您只需要在视图控制器中添加 3 样东西:

1.  苹果的`MediaPlayer`框架
2.  作为视图的子视图添加的虚拟实例`MPVolumeView`
3.  `AVSystemController_AudioVolumeNotificationParameter`的观察员

以下链接的 youtube 视频将指导您如何在项目中实现这一点:

视频中提到的示例的源代码可以在 Github 上找到:

[https://github.com/itsdamslife/iOS-Custom-Volume-UI](https://github.com/itsdamslife/iOS-Custom-Volume-UI)

在这里或 youtube 视频下方分享您的反馈。

快乐编码 [![😉](img/b26450942c7c42752fe0b02f126abb48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fumfYCPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f609.png)

 [![Damodar Shenoy's DEV Profile](img/864c8cf24c0550953b8fad29c1ebfb59.png)

T6】](https://dev.to/itscoderslife)

广告

```
 __ATA.cmd.push(function() {
            __ATA.initSlot('atatags-26942-5adb57912d977', {
                collapseEmpty: 'before',
                sectionId: '26942',
                width: 300,
                height: 250
            });
        });

        __ATA.cmd.push(function() {
            __ATA.initSlot('atatags-114160-5adb57912d97a', {
                collapseEmpty: 'before',
                sectionId: '114160',
                width: 300,
                height: 250
            });
        }); 
```

Enter fullscreen mode Exit fullscreen mode