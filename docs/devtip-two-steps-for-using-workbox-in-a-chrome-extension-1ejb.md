# 在 Chrome 扩展中使用 Workbox 的两个步骤

> 原文：<https://dev.to/idoshamun/devtip-two-steps-for-using-workbox-in-a-chrome-extension-1ejb>

最近，Chrome 团队在 Chrome 扩展中启用了 service worker，这是个好消息！现在，我们可以拦截扩展请求，这对缓存和性能尤其有利。
一个在服务工作者缓存方面大放异彩的库是[谷歌的工具箱](https://developers.google.com/web/tools/workbox/)，但是在 Chrome 扩展中使用它并不那么明显，所以这里是我的提示:

液体错误:内部

如果您正在使用 workbox 的 webpack 插件，只需添加:
`exclude: [/.*/],`作为您配置的一部分

在您的分机`manifest.json`中设置如下:
`"content_security_policy": "script-src 'self' https://storage.googleapis.com; object-src 'self'",`

让我知道你过得怎么样:)