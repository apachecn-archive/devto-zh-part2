# 编写 React 组件时要记住的性能提示

> 原文：<https://dev.to/psamim/performance-tips-to-have-in-mind-while-writing-react-components-4eo>

当我微调我正在工作的 React 组件时，我测试并尝试了不同的方式来重写代码。我正在汇编我学到的所有技巧，在我写代码时要记住的性能技巧。

*   注意你作为道具发送到组件中的内容。当道具或状态改变时，React 组件触发重新渲染。送变化少的道具。例如，请参见下面的代码: