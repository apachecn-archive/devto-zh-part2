# 受 safari 12 ITP 影响的只是我们吗

> 原文：<https://dev.to/roshan092/is-it-just-us-that-is-affected-by-safari-12-itp-297m>

随着最近 safari 12 的发布，苹果公司启用了 ITP，这本质上是一个机器学习系统，旨在阻止所有跟踪 cookies。我们使用第三方身份提供商来处理我们的注册和认证。这个提供者使用 openid connect，它本质上依赖于第三方 cookies 来管理多个域之间的 SSO。如果认证域被 ITP 列入黑名单，将中断整个 SSO 流程。

我想知道这里的开发人员是否受到了影响，如果是，有什么解决方法可以缓解这个问题。