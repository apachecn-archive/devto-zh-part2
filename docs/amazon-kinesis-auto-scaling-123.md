# 亚马逊 Kinesis 自动缩放

> 原文：<https://dev.to/workdharmendra/amazon-kinesis-auto-scaling-123>

这是 npm 模块，根据当前的流量需求扩展亚马逊 kinesis。该模块持续监控 kinesis 流中流量，并根据需要分割和合并碎片。亚马逊提供 Kinesis 来处理流数据。每个 kinesis 流可以有多个碎片，每个碎片每秒可以有高达 1000 条记录的流量。例如，如果您的应用程序需要处理 5000 req/秒，那么您需要 5 个碎片。因为你的应用程序的流量变化很大，碎片的数量也是如此。如果您的应用程序在高峰时间需要处理 20000 请求/秒，那么您需要 20 个碎片，而在其他时间您可能只需要 5 个碎片。每个碎片都要花钱。重要的是要有足够的碎片来服务当前的流量，但如果没有必要省钱，我们应该关闭额外的碎片。目前亚马逊不提供任何 kinesis 流的自动缩放。这个 npm 模块填补了缺失环节，以便您的应用程序总是有足够的碎片来处理。

## 你可以在[[https://www . npm js . com/package/@ xaxis-open-source/Amazon-kinesis-scaling](https://www.npmjs.com/package/@xaxis-open-source/amazon-kinesis-scaling)找到这个 NPM 模块。