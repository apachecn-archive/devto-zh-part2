# 像 full story 或 session stack 这样的软件是如何工作的？

> 原文：<https://dev.to/divyenduz/how-does-a-software-like-full-story-or-session-stack-work-3gjd>

他们[捕捉网页上的交互](https://www.fullstory.com/),并在随后的深度分析式会话中“回放”它们。控制台也是可用的。

对我来说，这看起来像是软件将整个文档存储为状态(包括鼠标位置),并使用一些 API 使控制台可用。

存储的数据是时间序列格式。

稍后，这些存储的数据可用于:-

为管理员可以播放的视频创建帧。在浏览器中播放文档+交互(一些方法)。
我想知道是否有人已经意识到这是如何实现的？