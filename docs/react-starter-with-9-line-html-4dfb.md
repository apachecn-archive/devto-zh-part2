# React starter 行 html

> 原文：<https://dev.to/mizchi/react-starter-with-9-line-html-4dfb>

将此代码另存为 index.html，并通过您的(现代)浏览器打开。

```
<div id=root />
<script type=module>
import React from 'https://dev.jspm.io/react@16'
import ReactDOM from 'https://dev.jspm.io/react-dom@16'
ReactDOM.render(
  React.createElement('h1', null, 'hello'),
  document.querySelector('#root')
)
</script> 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！

## 工作原理

*   这是一个没有`<html>`、`<body>`等的速记 html 文件。仅仅是`<div id=root>`的存在。
*   `<script type=module>`表示该代码范围可以使用原生 ES 模块。
*   从`jspm.io`导入 react 和 react-dom。jspm.io 是 ES 模块的 age CDN，仅用于开发(还没有)。
*   `ReactDOM.render`渲染`React.createElement('h1', null, 'hello')`，用 jsx 表示`<h1>hello</h1>`，到`<div id=root />`

我喜欢 hyperscript 语法，所以我经常使用`const $ = React.createElement`