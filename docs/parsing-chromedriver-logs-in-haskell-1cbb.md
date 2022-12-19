# 解析 Haskell 中的 chromedriver 日志

> 原文：<https://dev.to/chrismwendt/parsing-chromedriver-logs-in-haskell-1cbb>

chromedriver(用于与 Chrome 进行编程交互的工具)没有可分析的输出格式，所以我编写了一个 Haskell 程序将其转换为 JSON [chromedriver-logs](https://github.com/chrismwendt/chromedriver-logs) ，它:

*   输出 JSON
*   跟踪命令及其响应时间
*   流日志行
*   忽略垃圾行