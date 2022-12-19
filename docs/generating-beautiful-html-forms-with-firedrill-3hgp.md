# 用 FireDrill 生成漂亮的 HTML 表单

> 原文：<https://dev.to/scottlepp/generating-beautiful-html-forms-with-firedrill-3hgp>

在之前的[帖子](https://dev.to/scottlepp/introducing-firedrill---power-tools-for-firebase-g9f)中，我向你展示了如何使用 [FireDrill](https://firedrill.co) 在你的 Firebase 数据库中轻松找到数据。现在，我将向您展示如何从您的数据中轻松生成基于 html 表单的漂亮材料设计。

我们开始吧！前往[消防演习](https://firedrill.co)。

如何连接见[此处](https://dev.to/scottlepp/introducing-firedrill---power-tools-for-firebase-g9f)。

连接后，点击**表单**。输入您想要生成表单的收藏，然后点击**生成表单**。您将看到下面生成的表单。请注意，它将根据您的数据检测数据类型，并生成适当的输入。

[![gen](../Images/ac36119416eb5137da1583545d5b3854.png "Generate")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lxHI5Cgh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2h8cxq3gk2p990fv3pb9.png)

因此，我们看到它根据数据生成了适当的输入:复选框、文本输入和日期选择器都已生成。现在我们想移动我们的田地。将鼠标悬停在字段上以查看其拖动指示器。

[![drag](../Images/6269009fe0a323111dcdcff26def1c3e.png "Drag")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SuipIum9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hahtof5v3mrtbr9526s7.png)

所以我将可用的拖到了底部，并将创建的和**也向下移动了一点。**

[![drop](../Images/edc76a9949212409a99e56579814d719.png "Drop")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aoFdK3Wx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5g55amho6160a9831n90.png)

太好了，这就是我想要的形式。现在让我们**复制**它，这样我们就可以将它添加到我们正在开发的应用程序中。

[![copy](../Images/6ad7ebecc7361182ae06e4022a1d4145.png "Copy")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PtWryPgT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q9dxgu97i9l3jpni72lm.png)

就是这样！现在 HTML 表单被复制到你的剪贴板！让我们将它粘贴到我们最喜欢的 HTML 编辑器(对我来说是 VS 代码)中，看看它是什么样子的。

[![paste](../Images/c9b104a954a18b3d466cc306ab6af8ff.png "Paste")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mTqKmUoz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/leh1vlf5nux0sg5thcpx.png)

厉害！几秒钟之内，我们就生成了一个完整的 HTML 表单。**注意**表单元素使用来自[角形材料](https://material.angular.io/)的组件，因此您需要将其包含在您的项目中。

如果你喜欢 [FireDrill](https://firedrill.co) 就拿出点爱心，在 [GitHub](https://github.com/scottlepp/fire-drill) 上给我一颗星吧。感谢阅读！