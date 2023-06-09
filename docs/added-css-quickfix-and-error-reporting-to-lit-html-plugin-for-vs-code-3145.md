# 为 VS 代码的 lit-html-plugin 添加了 CSS 快速修复和错误报告

> 原文：<https://dev.to/justinribeiro/added-css-quickfix-and-error-reporting-to-lit-html-plugin-for-vs-code-3145>

花了一点功夫才把它做好，但是我已经能够给 VS 代码的[typescript-lit-html-plugin](https://github.com/Microsoft/typescript-lit-html-plugin)语言插件添加 CSS quickfix 和错误报告了。在下面的视频中，我们可以看到 css quickfix 和错误报告在没有任何花哨的用户设置的情况下为`<style>`块工作(带有火焰表情符号的 albiet 没有进入 pull 请求:-)。它还足够聪明，能够理解您的标记模板中可能存在的各种其他代码片段之间的差异。

<video loop=""><source src="https://storage.googleapis.com/jdr-public-imgs/blog/sc-2018-07-06-css-quickfix-lit-html-vscode.webm" type="video/webm"> <source src="https://storage.googleapis.com/jdr-public-imgs/blog/sc-2018-07-06-css-quickfix-lit-html-vscode.mp4" type="video/mp4"></video> 

再次非常感谢微软的 Matt，他忍受了我的问题，并花时间审查和合并了这个请求。

你现在可以通过抓取插件 [lit-html](https://marketplace.visualstudio.com/items?itemName=bierner.lit-html) 或者为你选择的编辑器配置[语言服务](https://github.com/Microsoft/typescript-lit-html-plugin)来开始使用它(详见项目自述文件)。