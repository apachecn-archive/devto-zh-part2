# 如何评论 JSX 的反应

> 原文：<https://dev.to/skptricks/how-to-comment-in-react-jsx-21c6>

帖子链接:[https://www . skptricks . com/2018/08/how-to-comment-in-react-jsx . html](https://www.skptricks.com/2018/08/how-to-comment-in-react-jsx.html)

本教程解释了如何在 react js 应用程序中执行注释。React 使用 JSX，它是 ECMAScript 的类似 XML 的语法扩展，没有任何定义的语义。它不打算由引擎或浏览器来实现。

[如何评论反应过来的 JSX](https://www.skptricks.com/2018/08/how-to-comment-in-react-jsx.html)

在 react 应用程序中添加注释时要记住:

1.  可以使用常规/*块注释 */，但是需要用花括号括起来:
    {/* 一个 JSX 注释*/}

2.  对于多行注释，可以使用下面的语法:
    {/*
    多行
    行
    注释
    */}

3.  你不能只在 JSX 内部使用 HTML 注释，因为编译器会认为它们是真正的 DOM 节点。React 开发者声明他们不打算给 JSX 添加常规的 JS 评论。
    不好的做法。
    render() {
    返回(

    )
    }

[![](../Images/2fc38b75a2316c6e36cb60499336350f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WeL-WlOB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-9_KwL66-Ny0/W2btxCBM8ZI/AAAAAAAABvQ/CQ5SKDrQPtUfKRhS_nRXW-Zeq9445bypQCLcBGAs/s320/reactcomment.jpg)

[阅读更多...](https://www.skptricks.com/2018/08/how-to-comment-in-react-jsx.html)