# XML 语言服务器简介！！！

> 原文：<https://dev.to/jpinkney/introducing-the-xml-languageserver-1oea>

在过去的 6 个月左右的时间里，我在 Red Hat 的一些同事一直在开发 XML 语言服务器。这是一个[被大量请求的语言服务器](https://github.com/Microsoft/vscode/issues/1440),尝试过几次，但都只有基本的功能。作为一个对语言服务器非常着迷的人，看到又一个新的语言服务器出现真是太棒了，尤其是我从一开始就看到它在成长。一两周前它刚刚在 [VSCode marketplace](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml) 上发布，看到它对编写 XML 的影响真的很令人兴奋。

目前的特点是:

*   语法错误报告
*   通用代码完成
*   自动关闭标签
*   符号高亮显示
*   文档折叠
*   文档符号和大纲
*   重命名支持
*   文档格式
*   XSD 验证
*   总部位于 XSD 的 hover
*   基于 XSD 的代码完成
*   代码操作

[![Features gif](../Images/9151c1e093e90bdfb4968abc6eccbb51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KTuPLGAY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/148698/45977901-df208a80-c018-11e8-85ec-71c70ba8a5ca.gif)

它仍在积极开发中，如果你想查看，这里有一个链接:[https://github.com/angelozerr/lsp4xml](https://github.com/angelozerr/lsp4xml)