# 使用 Pub/Sub 而不是通过 HTTP (Google Cloud Scheduler)直接触发有什么优势？

> 原文：<https://dev.to/itsarnavb/what-are-the-advantages-of-using-pubsub-instead-of-directly-triggering-through-http-google-cloud-scheduler-2anm>

我猜:安全。

Cloud Scheduler 似乎没有提供任何方法来验证请求源。如果发现端点，用户可以在计划外触发端点。

使用发布/订阅还有其他好处吗？

谷歌云调度:[https://cloud.google.com/scheduler/](https://cloud.google.com/scheduler/)

谷歌云发布/订阅:[https://cloud.google.com/pubsub/](https://cloud.google.com/pubsub/)