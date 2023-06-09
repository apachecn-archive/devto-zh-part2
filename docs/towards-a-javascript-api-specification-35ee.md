# 迈向 JavaScript API 规范

> 原文：<https://dev.to/miralemd/towards-a-javascript-api-specification-35ee>

在过去的几年里，OpenAPI (以前的 Swagger)已经成为描述 REST API 的事实上的标准，而非 REST API 的格式仍有待定义。

这在像 JavaScript 这样的动态类型语言中尤其具有挑战性，尽管它很流行，但仍然没有一致的方式来为消费者描述 API。

尽管确实存在各种格式，但每个项目/供应商似乎都有自己描述接口的方式:

*   来自 [JSDoc3](https://github.com/jsdoc3/jsdoc) 项目的 JSON 输出有很长的路要走，但是它的主要焦点是生成文档，而不是描述带注释的 API。
*   [documentationjs](https://github.com/documentationjs/documentation) 也提供了一个非常好的 JSON 结构，但是这个项目还是更侧重于生成文档。
*   esdoc 有一个非常好的插件架构，输出一个结构化的 JSON 应该是可能的。
*   NodeJS 的每个模块都有一个 JSON 表示，例如[net.html](https://nodejs.org/api/net.html)和 [net.json](https://nodejs.org/api/net.json)

## 目标

我感兴趣的是定义一个描述 JavaScript APIs 的标准。通过定义可消费 API 的机器可读格式，可以基于提供的规范创建附加工具:

*   生成 API 参考文档
*   生成类型(打字稿、流程等。)
*   可视化 API 以提供概述
*   通过比较版本和检测添加/弃用/删除的端点来协助 API 治理

与开放 API 规范非常相似，我已经从这种格式的[草稿开始，补充了一个 JSON-schema 和一个能够将 JSDoc 注释代码](https://github.com/miralemd/js-api-spec)转换为所述规范的[工具。](https://github.com/miralemd/jsdoc2spec)

你认为 JavaScript APIs 规范有用吗？没必要？你对目前的草案有什么反馈吗？让我知道你的想法。