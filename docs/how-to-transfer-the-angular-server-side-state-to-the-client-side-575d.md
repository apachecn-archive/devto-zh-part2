# 如何将角度服务器端状态转移到客户端

> 原文：<https://dev.to/teroauralinna/how-to-transfer-the-angular-server-side-state-to-the-client-side-575d>

默认情况下，Angular Universal 应用程序在应用程序引导时会再次发出 HTTP 请求，即使应用程序已经在服务器端呈现。虽然有一个非常简单的解决方案，不需要修改任何代码就可以实现。起初，当我试图找到如何阻止请求时，我只找到了如何在组件级别实现这一点的教程。在谷歌搜索了一段时间后，我幸运地找到了一个更简单的方法来实现这一点。

解决方案在 [TransferHttpCacheModule](https://github.com/angular/universal/tree/master/modules/common) 中。

> TransferHttpCacheModule 为在服务器端呈现应用程序时已经发出的请求安装一个 Http 拦截器，以避免在客户端出现重复的 HttpClient 请求。

由于这是一个 HTTP 拦截器，它会自动影响所有请求，并在幕后做所有的艰苦工作。

我在这里不重复安装步骤，因为最新的说明可以从[Angular Universal Common Module 自述文件](https://github.com/angular/universal/tree/master/modules/common)中找到。我只是想指出这一点，以防有人也很难找到适当的解决方案。