# MDX -降价中的 JSX

> 原文：<https://dev.to/kayis/mdx---jsx-in-markdown-48lg>

## 什么

这个街区有一种新的标记语言，叫做 [MDX](https://mdxjs.com/) ，本质上是 [Markdown](https://daringfireball.net/projects/markdown/) ，点缀着 [JSX 元素](https://reactjs.org/docs/introducing-jsx.html)和 JavaScript `import` s

## 为什么

如果你读了我的关于故事书的文章，你会知道能够独立运行组件，而不被嵌入到一个巨大的应用程序中是很好的。例如，当你需要检查所有东西是否仍然工作，或者你需要你的[文档](https://www.docz.site/)的例子。

此外，一些应用程序主要是文本，但需要一些更复杂的部分，如图表或动画，所以用 Markdown 编写它们会有点痛苦。例如[演示幻灯片](https://github.com/jxnblk/mdx-deck)。

## 如何

要快速启动并运行，只需使用这个命令:

```
npm init mdx 
```

Enter fullscreen mode Exit fullscreen mode

这将设置一个为 MDX 预先配置的 [Next.js](https://github.com/zeit/next.js) 项目。然后你就可以开始写你的 MDX 文档了。

这里举个简单的例子:

```
import Graph from './components/graph'

## My Heading

A nice paragraph that tells people about some data, followed by a `<Graph>` 
component that visualizes this data.

<Graph /> 
```

Enter fullscreen mode Exit fullscreen mode

*   JavaScript 中的`import`语句
*   Markdown 中的标题和段落
*   JSX 元素反应过来

好的一面是**一切都是组件**，所以你甚至可以导入其他 MDX 和**甚至 Markdown** 文件。

## 结论

MDX 是一种非常有趣的方法，可以让 Markdown 更加灵活。让我们看看将来会用它做什么。:)