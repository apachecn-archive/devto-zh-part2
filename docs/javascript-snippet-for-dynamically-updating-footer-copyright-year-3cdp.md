# 动态更新页脚版权年的 JavaScript 代码片段。

> 原文：<https://dev.to/vivekanandpadala/javascript-snippet-for-dynamically-updating-footer-copyright-year-3cdp>

嗨，今天我要编写一个 js 代码片段来动态更新页脚的版权年。

```
document.getElementById('copyrightYear').innerHTML = new Date().getFullYear(); 
```

Enter fullscreen mode Exit fullscreen mode

将 id (copyrightYear)放在页脚部分的一个 *span* 标记中，在符号之后和公司名称之前。

在关闭 body 标签之前，将上述代码放在底部的*脚本*标签中。

你会得到当前的年份，没有任何麻烦，每年定期更新。

如果你喜欢这篇文章，分享给你的朋友。

干杯！