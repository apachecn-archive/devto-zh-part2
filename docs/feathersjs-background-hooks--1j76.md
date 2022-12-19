# 羽毛背景挂钩

> 原文：<https://dev.to/mattchewone/feathersjs-background-hooks--1j76>

##### Feathersjs 是一个面向现代应用的开源 REST 和实时 API 层。

如果您需要在响应客户端之后运行钩子，例如，一个过程可能需要几秒钟才能完成，而钩子的响应并不是被调用的实际服务所需要的，那么您可以从钩子返回`context`并允许函数运行。