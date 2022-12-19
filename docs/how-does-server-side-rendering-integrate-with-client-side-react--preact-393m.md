# 服务器端渲染如何与客户端反应/预反应集成？

> 原文：<https://dev.to/mfbx9da4/how-does-server-side-rendering-integrate-with-client-side-react--preact-393m>

我想建立一个服务器端渲染 HTML(理想情况下在后端使用 preact)的 web 应用程序，然后让 preact 以一种懒惰的方式逐步接管接口，以控制`onClick`绑定和其他事件。

重点是我不希望 javascript 库的加载对初始渲染速度有任何影响。

在这方面有哪些最佳实践和例子？

我知道 [http://dev.to](http://dev.to) 用 rails 做到了这一点，但不确定 preact 和 HTML 之间的集成程度如何。

谢谢🙏