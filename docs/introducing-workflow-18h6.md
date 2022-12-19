# 工作流简介

> 原文：<https://dev.to/havardh/introducing-workflow-18h6>

*第一贴[https://spectrum.chat/workflow?thread = 7679137d-130 c-4b8a-8eb 2-09 ACF 0d 2 b 27 c](https://spectrum.chat/workflow?thread=7679137d-130c-4b8a-8eb2-09acf0d2b27c)*。*更新了代码样本，支持 [workflow@2.x](https://github.com/havardh/workflow/commit/ca77860)* 。

`workflow`是一个节点命令行工具，可以将声明性布局转化为屏幕上运行的应用程序。

[![ReactExample.js](../Images/5e14c69ea00cd3f03af59132cb68c6a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y0oAWxr9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/chodmke88uapwkwsflhm.gif)

布局可以声明为 javascript 对象或 React 应用程序。布局包含应用程序及其在屏幕上的位置，以及传递给应用程序的参数。下面的代码示例是上面 gif 中使用的布局，声明为 React 应用程序。React 应用程序由`render`函数中的自定义协调器评估为 javascript 对象。

```
import React from 'react';
import { render, Workspace, requireComponent } from 'workflow-react';

const { SplitH, SplitV } = requireComponent('workflow-layout-tiled');
const { TextEditor, Browser, Terminal } = requireComponent('workflow-apps-defaults');

export const flow = render(
  <Workspace name={'workflow-react-example'}>
    <SplitV percent={1.0}>
      <SplitH percent={0.8}>
        <TextEditor percent={0.5} file={__filename} />
        <Browser percent={0.5} url={'https://github.com/havardh/workflow'} />
      </SplitH>
      <Terminal percent={0.2} cwd={__dirname} />
    </SplitV>
  </Workspace>
); 
```

## 支持

使用 i3 和 OSX 支持 Linux。它对使用 wmctrl (Ubuntu 等)的 Windows 和 Linux 有实验性的支持。每个平台至少支持一个终端、浏览器和文本编辑器。即将发布的帖子将描述如何实现/改进 windows 管理器支持，以及添加对更多应用程序的支持。

## 入门指南

```
$ npm install --global workflow
$ workflow ReactExample.js 
```

当您第一次运行`workflow`时，它会要求您设置您的工作流主目录。这是您将添加所有布局和支持库的地方。工作流主目录的默认位置是`~/.workflow`。

这篇文章是关于如何使用和扩展`workflow`来满足你的需求的系列文章的第一篇。下一篇[文章](https://dev.to/havardh/controlling-your-desktop-layout-with-react-1g0b)描述了如何编写你自己的布局声明。工作流仍然是实验性的和不完整的，但是基础正在逐渐形成。来和我一起在[https://spectrum.chat/workflow](https://spectrum.chat/workflow)建设社区，在[https://github.com/havardh/workflow](https://github.com/havardh/workflow)发展。