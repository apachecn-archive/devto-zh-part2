# 在 React 外部创建和管理组件

> 原文：<https://dev.to/aravindballa/creating--managing-components-outside-react-aie>

你可能不会在前端使用 React 来启动一个新项目。您只想构建一些组件，利用您的前端已经使用的 API，并在您现有的应用程序中使用它们。

厉害！你可以一直这么做。让我在这里概述一下工作流程。

获取构建的输出 js 文件，并将其包含在您的 HTML(应用程序)中。您需要将 React 和 ReactDOM 添加到 HTML 中，以便将组件装载到页面上。