# 安全可靠的浏览器嗅探

> 原文：<https://dev.to/mahmoudelmahdi/safe-reliable-browser-sniffing-39bp>

我们最近为我们的 Web 应用程序构建了一个 WebExtension，作为要销售的附加和额外功能💰💸(b/c 为什么不可以)。我们决定提供 4 种不同的浏览器类型: **Chrome** 、 **Safari** 、 **Opera** 和 **Firefox** 。因此，我们必须弄清楚哪个浏览器正在呈现我们的页面，以便向用户显示特定于浏览器的指令。

* * *

## 你在骗我。你不是吗？

**使用用户代理的浏览器检测**简直太烂了！！因为恶搞这个**值**是小事一桩。例如下面的片段:

```
navigator.userAgent.indexOf('Chrome') !== -1 
```

Enter fullscreen mode Exit fullscreen mode

为**谷歌 Chrome** 和 **Opera** ( *因为 Opera 用 Chrome*使用的 Blink + V8 替换了它的引擎)返回`true`，因为它的 UA 字符串看起来像 Chrome。那不是我要找的。如果我们试图检测一个特定的浏览器，那么功能检查就失去了意义。

> 查看 [MDN web docs:使用用户代理](https://developer.mozilla.org/en-US/docs/Web/HTTP/Browser_detection_using_the_user_agent)进行浏览器检测，了解更多详情。

## 顶级对象 FWT

`window`是**浏览器对象模型** ( **BOM** )层次中的顶层对象。每一个浏览器都有自己的属性，比如 Safari 中的 *ApplePayError* ，此外还有标准属性(比如`window.location`、`window.console`，...等等)。

## 解

```
/*
 * Browser detection
 * @return {String} 
 */
const browserDetection = () => {
  const browsers = {
    firefox: !!window.InstallTrigger,
    safari: !!window.ApplePaySession,
    opera: window.opr && !!window.opr.addons,
    chrome: window.chrome && !!window.chrome.webstore
  };

  return Object.keys(browsers).find(key => browsers[key] === true);
};

console.log(browserDetection()) // browser name expected 
```

Enter fullscreen mode Exit fullscreen mode

*   **Firefox**:`InstallTrigger`界面是应用程序 API 中一个有趣的异常点。
*   **Safari** : `ApplePaySession`属于 Apple Pay JS API。用于管理网上支付过程的会话对象。
*   **戏曲** : `opr`自明。`.opr.addons`表示附加组件 API 中的接口
*   **Chrome** : ~~`chrome.webstore` API 从网页~~中“内联”启动应用和扩展安装。注意:由于[麦迪逊·迪克森](https://dev.to/mix3d)，这将[弃用](https://dev.to/_elmahdim/safe-reliable-browser-sniffing-39bp#comment-node-118287)。欢迎任何推荐！

## 在以下“桌面浏览器”中测试:

Firefox Quantum 版本 60

谷歌浏览器第 67 版

歌剧版本~53

Safari 版本~11

**请记住**，那个解决方案在我的情况下刚刚好，而<u>可能不适合你的</u>。

## [在 Codepen 上演示](https://codepen.io/elmahdim/full/bKjmbb/)

* * *

**欢迎反馈**。如果你有任何建议或更正，请不要犹豫给我留言/评论。