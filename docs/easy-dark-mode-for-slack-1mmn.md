# 轻松的黑暗松弛模式

> 原文：<https://dev.to/changoman/easy-dark-mode-for-slack-1mmn>

**更新:*从 Slack 4.0 开始，以下方法不再有效。我已经成功地使用了这个:[https://github.com/LanikSJ/slack-dark-mode](https://github.com/LanikSJ/slack-dark-mode)T5】*

我最近从一个同事那里得到了这个，我想我应该分享一下。如果你想在 Slack 中实现一个快速简单的黑暗主题，请执行以下操作:

在 Mac 上，导航到此目录:

```
cd /Applications/Slack.app/Contents/Resources/app.asar.unpacked/src/static 
```

Enter fullscreen mode Exit fullscreen mode

用文本编辑器编辑文件`ssb-interop.js`。

将此代码添加到最底部(*感谢 amerritt14 对代码的支持而编辑):

```
document.addEventListener('DOMContentLoaded', function() {
 $.ajax({
   url: 'https://cdn.jsdelivr.net/gh/laCour/slack-night-mode/css/raw/black.css',
   success: function(css) {
     let overrides = `
     code { background-color: #535353; color: #85c5ff; } /* Change color: to whatever font color you want */
     .c-mrkdwn__pre, .c-mrkdwn__quote { background: #535353 !important; background-color: #535353 !important; }
     `
     $("<style></style>").appendTo('head').html(css + overrides);
   }
 });
}); 
```

Enter fullscreen mode Exit fullscreen mode

保存文件重启 Slack，你应该会有一个黑暗主题！