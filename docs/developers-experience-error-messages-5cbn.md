# 开发人员体验:错误消息

> 原文：<https://dev.to/stereobooster/developers-experience-error-messages-5cbn>

让我们谈谈开发工具中的错误信息和警告，比如编译器、linters 类型检查器等。

## 清除错误信息

我猜测清除错误信息的趋势是由 Elm 开始的。至少我以前没见过那么明确的消息。榆树走势被 [JS](https://github.com/codehag/js-better-errors) 、 [ReasonML](https://github.com/reasonml-community/error-message-improvement) 、 [Ocaml](https://github.com/gasche/ocaml-better-errors) 、 [Rust](https://blog.rust-lang.org/2016/08/10/Shape-of-errors-to-come.html) 等接盘后。

清晰的错误信息使学习曲线变得更加容易，特别是如果有一个非常严格的类型系统，基本上，类型检查器变成了一个交互式教程。

如果错误信息不清楚，您需要在互联网上搜索错误。如果有一页解释了这些问题，例如[导入/第一个](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/first.md)，那你就很幸运了。但是在 GitHub 中可能会有很长的讨论，你需要浏览所有的评论来寻找答案，例如[警告:关键依赖](https://github.com/webpack/webpack/issues/196)。如果错误消息能够清楚地解释如何修复它，而不需要搜索它，也不需要切换上下文，那么将会节省很多时间。

我想知道为什么以前没有发生过。我想因为这是一个额外的任务，你需要写额外的代码来使错误信息清晰。例如，参见[构造人类级解析器](http://duriansoftware.com/joe/Constructing-human-grade-parsers.html)。

## 日志中的杂音

为了更好地进行 DX，需要限制警告、调试消息的输出，因为如果出现大量重复或不相关的信息，程序员将会对这些消息视而不见。我看到过几次开发人员努力解决问题，即使错误消息清楚地解释了该做什么——他们只是不读它，因为他们习惯了日志中的噪音。例如:

```
FAILED: src/ListItem.mlast
reason-react-hacker-news/node_modules/bs-platform/lib/bsc.exe -pp "reason-react-hacker-news/node_modules/bs-platform/lib/refmt.exe --print binary" -ppx 'reason-react-hacker-news/node_modules/bs-platform/lib/reactjs_jsx_ppx_2.exe'   -w -30-40+6+7+27+32..39+44+45+101 -nostdlib -I 'reason-react-hacker-news/node_modules/bs-platform/lib/ocaml' -bs-no-version-header -bs-super-errors -no-alias-deps -color always -c -o src/ListItem.mlast -bs-syntax-only -bs-binary-ast -impl reason-react-hacker-news/src/ListItem.re
File "reason-react-hacker-news/src/ListItem.re", line 30, characters 24-25:
Error: 3355: <UNKNOWN SYNTAX ERROR>

  We've found a bug for you!
  reason-react-hacker-news/src/ListItem.re

  There's been an error running Reason's refmt parser on a file.
  This was the command:

  reason-react-hacker-news/node_modules/bs-platform/lib/refmt.exe --print binary 'reason-react-hacker-news/src/ListItem.re' > /var/folders/sd/gvj7n1494zj86l861747vbd00000gn/T/ocamlppc688eb

  Please file an issue on github.com/facebook/reason. Thanks!

ninja: error: rebuilding 'build.ninja': subcommand failed 
```

而重要的部分是:

```
File "reason-react-hacker-news/src/ListItem.re", line 30, characters 24-25:
Error: 3355: <UNKNOWN SYNTAX ERROR> 
```

其他一切都无助于修复错误。

*马修·布罗德在 Unsplash 上拍摄的照片*