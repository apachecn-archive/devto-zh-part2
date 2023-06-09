# MDX

> 原文：<https://dev.to/kahlil/mdx-4b50>

> MDX 是一款用于雄心勃勃项目的 Markdown 加载器、解析器和渲染器的 JSX。它结合了 Markdown 的可读性和 JSX 的表现力。两全其美。

如果为您的项目配置 MDX，您可以做一些疯狂的事情，比如将 React 组件导入 markdown 文件并在其中使用它们。

```
import Graph from './components/graph'
## Here's a graph
<Graph /> 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，您可以将 MDX 文件导入 React 组件，并将其用作常规 React 组件。

```
import React from 'react'
import Hello from '../hello.md'
export default () => <Hello /> 
```

Enter fullscreen mode Exit fullscreen mode

强大的东西。如果你是一个用 React 或 Next.js 构建东西的开发人员，你想给项目添加内容，这是一个非常好的方法。一旦你使用它，你会感觉很兴奋。

MDX 是 [CommonMark](http://commonmark.org/) 规范的超集，增加了嵌入式 JSX 和`import/export`语法。

CommonMark 是由建立话语的人创造的。他们在其平台上支持 Markdown，他们需要一个清晰的规范来处理某些边缘情况。John Gruber 的原始规范不够具体。起初他们想称之为[标准降价](https://blog.codinghorror.com/standard-flavored-markdown/)，但是 [Gruber 大发雷霆，写了一封愤怒的电子邮件](https://blog.codinghorror.com/standard-markdown-is-now-common-markdown/)，甚至在他的播客脱口秀上谈论这件事。

话语从未试图扰乱他或从他身上带走任何东西，所以他们把它重新命名为 CommonMark。

很高兴看到像 MDX 这样的东西可以非常安全和相对容易地构建，因为有一个 Markdown 规范。