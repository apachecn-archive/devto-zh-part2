# 如何为 Angular 6+定制服务人员？我正在使用 Firebase，它需要一名服务人员。

> 原文：<https://dev.to/itsarnavb/how-do-you-customize-service-workers-for-angular-6-im-making-use-of-firebase-and-it-needs-a-service-worker-4ol0>

我在用 Ionic 4/Angular 6 搭配 Firebase。

我用这个命令启用了 Angular

`ng add @angular/pwa@v6-lts --project app`

它生成了 ngsw-config.json，这将在生产中生成一个服务工作者。但是我如何给服务人员添加代码呢？

我正在添加 Firebase，以利用 Firebase 云消息。