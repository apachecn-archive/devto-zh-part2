# re-jok——一个用样式化组件构建的小型 react UI 组件库

> 原文：<https://dev.to/tranbathanhtung/re-jok---a-small-react-ui-component-library-built-with-styled-components-3bl1>

大家好！！！我刚刚为 react 构建了一个小的库 ui...几个月前刚开始在网上学习 react，实习了三个月...当我看到光谱时，我有了这个想法..我从光谱中学到了很多新东西，我决定通过构建 re-jok 来练习...所以也许我的代码看起来很愚蠢或者有很多错误，但是我希望每个人都喜欢它.....我会努力改进它

这是我的回购协议:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[重约](https://github.com/tranbathanhtung/re-jok)

### 用样式化组件构建的 React UI 组件库

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/6efbc4e2b45467ccb90b5b5c816f6dc2.png)T2】](https://camo.githubusercontent.com/82d195b4b9407c2cea51493672210e710185e51db3f32bd7609d7abe54a8ab6e/687474703a2f2f7265732e636c6f7564696e6172792e636f6d2f6c6967687465722f696d6167652f75706c6f61642f76313533323631343739362f6c6f676f2d707265766965772d34383531616362332d313235312d346363342d616638372d3336343666303430373839345f686475627a762e6a7067)

# Re-jok

Re-jok 是用 [styled-components](https://github.com/styled-components/styled-components) 构建的 React UI 组件库。这是一个组件的集合，可以帮助你快速、轻松地设计出漂亮的网站。现在，它非常年轻，很少的组件或某处看起来很愚蠢，但我们总是试图在最短的时间内改进和添加更多的组件。

## 为什么要重新开玩笑

*   非常轻便...大约 38KB min+gzip
*   易于定制的组件
*   易于定制的主题

## 安装

```
npm i --save re-jok
```

Enter fullscreen mode Exit fullscreen mode

## 入门指南

```
import React from 'react'
import { globalConfig, JokTheme, Button } from 're-jok'

globalConfig();

const App = props => (
  <JokTheme>
      <Button>Hello World</Button>
  </JokTheme>
)
```

Enter fullscreen mode Exit fullscreen mode

## 证明文件

*   [文档](https://re-jok.herokuapp.com/)

</article>

[View on GitHub](https://github.com/tranbathanhtung/re-jok)

谢谢大家！！！