# [Idea]使用电子和推送器的屏幕共享应用程序

> 原文：<https://dev.to/akshatbhargava123/idea-screen-sharing-application-using-electronjs-and-pusher-5c9n>

# 我的想法？

创建一个像 Teamviewer / Chrome remote desktop 一样的实时屏幕共享应用程序，但没有任何实时控制系统。

# 技术堆栈

1.  ElectronJS(获得本地操作系统 API 支持，特别是[桌面捕获](https://electronjs.org/docs/api/desktop-capturer)
2.  NodeJS(为共享会话和可能的上行/下行数据流生成 userId 的路由)
3.  推送通道 API(这些神需要的电源提供者)

## 我知道我将要面对的挑战？

[MediaRecorder](https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder) ， [FileReader](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) ， [Buffer](https://medium.freecodecamp.org/do-you-want-a-better-understanding-of-buffer-in-node-js-check-this-out-2e29de2968e8) ， [ElectronJS](https://electronjs.org/) (所有这些都将是我的第一次尝试)但我知道这对我和我的学习来说将是一次有趣而令人兴奋的旅程😁

#### 我对推手的疑问:

*   我如何开始从一个客户端到另一个客户端的流？例如，我向后端路由发送一个生成 id 的请求，我用这个 id 创建一个推送通道。现在，我如何从客户端通过 pusher api 在通道上以流形式发送视频数据？任何帮助都将不胜感激。

这是我的第一篇文章，所以请原谅我的错误标记，并感谢阅读。