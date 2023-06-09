# 将 pwa 作为 Chrome 扩展进行运输

> 原文：<https://dev.to/chromiumdev/shipping-pwas-as-chrome-extensions-3l5c>

渐进式网络应用程序，或 PWAs，基本上是一种离线工作的网站，可能包括类似于原生应用程序的功能。但实际上，PWA [的绰号](https://google.com/search?q=define%3Amoniker)是你告诉你的老板、你的投资者，无论是谁，这样他们就可以概念化你正在建立的这个网络。

所以你已经建立了一个 PWA，创建了你的服务人员，并跟随[所有](https://developers.google.com/web/progressive-web-apps/) [向导](https://developers.google.com/web/fundamentals/codelabs/your-first-pwapp/)。对我来说，那就是[表情输入器](https://emojityper.com):一个简单的 PWA，你可以在那里输入单词，并接收表情符号。这非常适合桌面和在不支持表情符号的编辑器中输入表情符号。⌨️🔜🤣

[![Emojityper Chrome Extension showing its browser action](img/2180131a72f819b797848505ab321e95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oBQodEco--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mgv1tc3rt3bgciuatgrm.png)

但是一旦你建立了这种伟大的体验，你就不仅仅局限于在“网络”上传播它了。在这篇文章中，我将详细介绍我如何将 [Emojityper](https://emojityper.com) 作为 [Chrome 扩展](https://chrome.google.com/webstore/detail/emojityper/cbkooiohnnlhpelnllknmenlhiglnkap)，通过[浏览器动作](https://developer.chrome.com/extensions/browserAction)访问。

* * *

**为您的 PWA 创建一个扩展非常简单。**为什么？因为如果你的 PWA 已经离线工作，你可以利用 Chrome 已经为你的用户缓存它的事实。我们实际上只是在构建一个小型嵌入器，将您的站点包含在一个`iframe`中。开始吧！🏁

## 扩展名文件

基本上有两个文件组成了我们的 Chrome 扩展。首先是`manifest.json`文件，描述一些元数据:

```
{  "name":  "Emojityper",  "version":  "0.2",  "description":  "Type emoji on your keyboard!",  "browser_action":  {  "default_popup":  "embedder.html",  "default_title":  "Emojityper",  "default_icon":  {"128":  "i128.png",  "32":  "i32.png",  "16":  "i16.png"}  },  "icons":  {"128":  "i128.png",  "32":  "i32.png",  "16":  "i16.png"}  "manifest_version":  2  } 
```

Enter fullscreen mode Exit fullscreen mode

这个清单也可以请求对 Chrome 扩展可用的特性的额外许可。然而，由于我们只是嵌入了一个已经利用了 web 平台特性的 PWA 您应该尽量避免它。

接下来是`embedder.html`文件，它将你的 PWA 嵌入到一个`iframe` :
中

```
<!DOCTYPE html>
<html>
<head>
<style>
  body {
    background: #2c3e50;
    margin: 0;
    width: 380px;
    height: 200px;
  }
  iframe {
    width: 100%;
    height: 100%;
    border: 0;
  }
</style>
</head>
<body>
  <iframe id="iframe" src="https://emojityper.com/?utm_source=chrome_ext"></iframe>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

保存这两个文件💾⬇️📂在一个单独的文件夹中(连同一些图标— `i16.png`、`i32.png`、`i128.png`，或者如果你能逃脱的话可能更少)，然后前往`chrome://extensions`，启用开发者模式，并选择“加载未打包的扩展”。

[![Extension development](img/d35e44dad714bf503fb49fe9f0d4c6a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r37zQiOp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sduvftzakknj1odyjuxt.png)

太好了！现在，您将看到 Emojityper 应用程序出现在您的扩展栏中，单击它将调出我们之前看到的显示。

* * *

# 挑战

您的 PWA 可能会开始正常工作。您已经嵌入了一个方便的弹出窗口，希望对您的用户有所帮助。但是有两个细微差别。

## 响应式设计

我们的扩展位于 380 像素宽 200 像素高的区域。当然，你可以把它放大，但重点是提供一个方便的小 UI。虽然浏览器标签也可以缩小到这个尺寸——在我的 Mac 上，我能做的最小的 Chrome 窗口是 400 宽 159 高——但你的用户可能会希望在他们的桌面上使用你的 PWA [作为一个小音乐控制器或其他“小工具”。🎶🔁](https://developers.google.com/web/updates/2018/05/dpwa)

这基本上意味着你可能需要在你的响应式设计中加入一个“微”模式。Emojityper 通过为 400 像素或更低的高度添加 CSS，从混乱变得简单:

[![Clutter to simple](img/55a98f19184137d4be0664a3a75948ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vKCGAg2B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wvbh4g2xgwg6jcqt3ypo.png)

## 延期津贴

我们嵌入的 PWA 能够正常工作，因为您利用了网络平台。但是总有一些事情是扩展可以做的，而你的 PWA 不能。

在 Emojityper 的例子中，点击“复制”按钮实际上应该关闭扩展窗口。这是通过让 PWA 寻找它的“父页面”:容器/嵌入页面，并让父页面知道复制完成的时间来实现的。在 Emojityper 内部，我做了这样的事情:

```
function notifyParentCopy() {
  // notify parent (for ext)
  if (window.parent) {
    window.parent.postMessage('copy', '*');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在扩展中，我添加了一个包含的 JS 文件，它监听消息并采取行动:

```
window.addEventListener('message', (ev) => {
  switch (ev.data) {
  case 'copy':
    window.close();
    break;
  default:
    console.warn('got unhandled message', ev.data);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

(因为我们的扩展实际上不是托管在一个域上，它可以与嵌入式的`iframe`进行通信，没有任何安全挑战。)

是否值得检测“扩展模式”并提供附加功能？这取决于你。这篇文章主要是关于把你的 PWA 带到你的用户所在的地方，所以我认为只有最少的附加特性可能是有意义的(就像你可能不需要请求任何权限一样)。

* * *

感谢阅读！我希望你已经了解了更多关于 [Emojityper](https://emojityper.com) 和它的 [Chrome 扩展](https://chrome.google.com/webstore/detail/emojityper/cbkooiohnnlhpelnllknmenlhiglnkap)的知识。😂

你可以采取类似的方法，通过网络视图将你的 PWA 包含在 Android、iOS 或桌面原生应用中。你的 PWA 可以扩展它的分布，去你的用户想去的地方。🔜👥👏👩‍💻👨‍💻