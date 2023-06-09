# 在谷歌助手上讲述一个打字故事

> 原文：<https://dev.to/michaelsolati/telling-a-typeform-story-on-the-google-assistant-3eaf>

我成长于 90 年代，阅读的是臭名昭著的 R. L. Stine 写的扣人心弦的故事。我最喜欢的小说是那些建议读者小心选择自己恐惧的小说。这些都是你自己挑选的冒险书籍，读者从一页跳到另一页，就像他们在奇怪、怪异、通常令人毛骨悚然的世界中导航。

当时，很容易看到和理解这些书是如何工作的。多年后，作为一名软件开发人员，我认为它们是简单的决策树，是人工智能的初级形式。即使不打算成为人工智能的一种形式，我们作为开发人员也经常在我们的`if`语句和`switch` cases 中修改这些概念，其中函数可能基于 case、情境或标志链接到其他函数。

然而，代码并不总是讲述一个令人信服的故事。我的侄子和侄女可能对编写类或函数没有兴趣。我可能会启发他们写自己的恐怖故事。这就是 [Typeform](https://www.typeform.com/) 的用武之地。

如果你以前没有听说过 Typeform，你可能在网上见过他们的产品。Typeform 是一个简单而强大的工具，用于构建以对话方式收集和共享信息的表单。它是基于网络的，所以你可以创建从调查到应用的任何东西，而不需要写一行代码。您还可以使用 Typeform 的 API 以动态和编程的方式开发表单。因为这些是会话形式，所以 Typeform 被用来制作类似这样的东西:

*   [求职申请表](https://www.typeform.com/forms/job-application-form-template/)
*   [网上订单](https://www.typeform.com/order-forms/delivery-order-form-template/)
*   [小测验&测试](https://www.typeform.com/quizzes/online-trivia-quiz-template/)
*   [竞赛](https://www.typeform.com/examples/polls/straw-poll-template/)
*   [登记表](https://www.typeform.com/forms/registration-form-template/)
*   [互动评书](https://www.typeform.com/templates/t/interactive-fiction/)

一个有趣的用途是，平台让制作互动故事变得超级容易！实际上，你把每一个领域都变成了一个选择题块，在这里你把你的故事的一部分作为一个章节来讲述，并向读者呈现选项。你甚至可以使用“感谢屏幕”(一个免费使用的专业功能)作为故事分支的结尾。

[![Building a form](img/ec305e51ad69e95668c8b126dc2b3dcc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sb9ZMzWY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xlfilhz1un16kk0dkno9.png)

一旦你的章节和结尾都创建好了，你就需要把它们连接起来，这也是 Typeform 让它变得超级容易的地方！Typeform 的逻辑跳转(另一个 PRO 特性)允许用户根据他们的选择只查看表单的一部分并与之交互。

[![Jump to page 24](img/d9307e32959f905ab8bd30c33138990a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hCgeJzXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ru7r483hto88qa3mhztl.gif)

继续这个过程，你可以为任何场景建立案例，最终建立一个像这样的故事…

[![A full story](img/954238289c21633c0dc468c6c510b568.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LIG0zQpW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/as4wod87qht3pfbu2vbr.png)

呀，那是一个激烈的故事！我必须承认，这不是我创造的。其实就是 Typeform 提供的[互动故事模板](https://www.typeform.com/templates/t/interactive-fiction/)。它提供了一个很好的例子，说明如何使用字体来制作一个交互式的故事。

在我的侄女和侄子写完他们自己的恐怖故事后，比如受人尊敬的*当心紫色花生酱*，我内心的开发者问:“我们在这里还能做什么？”虽然这可能很酷，但如果能够讲述整个故事，那就更酷了。毕竟是一种会话形式！

为了能够讲述一个故事，我们可以让它在支持 Google Assistant 的设备上播放，比如 Google Home(我侄子喜欢用它说话)。有四种工具可以做到这一点:Dialogflow、Firebase、Google Assistant 和 Typeform！我们使用 Dialogflow 来处理来自用户的意图或输入，但我们只使用默认的欢迎意图来介绍我们的故事，并为每个未来的交互使用后退意图。我们通过 webhook 调用可以托管在 Firebase 上的云函数来实现这些意图。网上有很多文章解释了如何为 Google Assistant 构建一个基本的应用程序。我建议你看看其中的一些，或者看一看谷歌在这里提供的 codelabs。

我们交互式的、语音驱动的故事中真正的肉(或豆腐)和土豆来自于 Typeform SDK。Typeform 提供 API 和 SDK 来构建和管理表单、获取提交等等。我们真正感兴趣的是他们的 Create API，我们可以用它来检索表单。通过 NPM
安装客户端即可

```
npm i @typeform/api-client 
```

Enter fullscreen mode Exit fullscreen mode

从那里，在您的`index.js`文件中，我们将添加 Typeform 客户机，实例化它，然后获得表单。

```
import { createClient } from '@typeform/api-client';
import { dialogflow } from 'actions-on-google';
import * as functions from 'firebase-functions';

// Available at https://admin.typeform.com/account#/section/tokens
const typeform = createClient({ token: 'YOURTYPEFORMTOKEN'});

// The 6 character code in the URL when editing a form
const typeformForm = typeform.forms.get({ uid: 'FORMID' }); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了表单作为承诺，我们将获得表单数据并将其设置为一个局部变量，这样我们就不需要在每次需要查看它时从 Typeform 发出另一个 API 请求。

```
let resolvedForm;
typeformForm.then(f => resolvedForm = f); 
```

Enter fullscreen mode Exit fullscreen mode

最后，现在我们有了表单数据，我们可以为 Dialogflow webhooks 创建处理程序，我们将从默认意图开始。

```
const app = dialogflow();
app.intent('Default Welcome Intent', (conv) => {
    const slide = new Slide(conv, resolvedForm);
    slide.run();
});
export const aog = functions.https.onRequest(app); 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到，我将对话和表单注入到 Slide 类的一个新实例中。Slide 类是我为了处理来自 Dialogflow 的用户输入以及来自表单的细节而创建的。

当用户第一次与应用程序对话时，我们获取第一个表单字段的详细信息，并将其发送给用户，同时提供他们可以做什么的选项。

```
conv.add(`Welcome to ${tf.title}. ${tf.welcome_screens[0].title}`);
conv.add(`${tf.fields[0].title}... What would you like to do? ${choices()}`);
conv.data = data(); 
```

Enter fullscreen mode Exit fullscreen mode

向用户背诵。但是同样被发送回来的，仍然是隐藏的，是关于他们所拥有的选项的细节，以及这些选项将用户带到哪个字段。这就是将`conv.data`赋给调用`data`函数的结果的地方。

```
function data() {
    const choices = field.properties.choices.map((c) => { return { label: c.label, ref: c.ref }; });
    const actions = tf.logic.find((v) => v.ref === field.ref).actions;
    const options = [];
    choices.forEach((c) => {
        const action = actions.find(a => ((a.condition.vars.length === 2) && (a.condition.vars[1].value === c.ref)));
        options.push({ label: c.label, value: action.details.to.value });
    });
    return { ref: field.ref, options };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们循环选择当前显示给用户的字段，然后搜索逻辑(通过 SDK 返回的字段)。我们这样做是为了看到一个选择会把用户带到下一个字段(也就是跳到第 24 页)。最后，我们返回用户当前所在字段的数据、他们的选项以及他们的选项所指向的字段。

当用户做出下一个选择时，使用分配给`conv.data`的信息，我们可以确定他们的选择是否有效，以及下一个字段应该是什么。然后，当用户通过扣人心弦的故事时，它被冲洗，循环，重复。

如果你想看看那是什么样的，只要找到一个谷歌主页(或者使用你的安卓手机)并说，“好的谷歌，和*谈谈一个可怕的开始*”

这种体验完全建立在字体上。你可以在这里找到所有可用的代码。你也可以在你的助手驱动的设备上试用，你可以从这里把它发送到你的任何设备[。](https://assistant.google.com/services/a/uid/0000001b7df4bf67?hl=en)

我希望很快听到你的恐怖故事！