# 零模式#性能

> 原文：<https://dev.to/gokatz/nil-pattern-perfmatters-3jjh>

<small>*封面图片鸣谢:[马克-奥利维耶·乔多因](https://unsplash.com/@marcojodoin)来自【unsplash.com】T4*</small>

在通过互联网向我们的用户交付内容时，性能是需要检查的关键复选框之一。尽管我们的内容很棒，但如果我们的网站/应用表现不佳，我们可能会面临对我们产品的批评。我们需要尽快开始！保持速度！！抓住并留住我们的用户群。

有很多很棒很聪明的技术可以提高我们网站的性能，包括减少初始负载、适当的资产缓存、预取关键资源、将繁重的计算任务卸载给工作人员等等。,

在这篇文章中，我们将看到另一个这样的技术，我从 Pinterest 工程博客中获得灵感，关于他们的[一年 PWA 成功故事](https://medium.com/@Pinterest_Engineering/a-one-year-pwa-retrospective-f4a2f4129e05)，作者[扎克·阿盖尔](https://twitter.com/ZackArgyle)。整篇文章非常简洁地描述了他们的旅程。一定要尝试了解更多有趣的加载模式。

这篇文章的观点是:

如果您浏览 Pin 的提要，我们会提供每个 Pin 的信息。当你点击一个，它会带你到一个详细的视图。因为 Pin 数据是标准化的，所以我们可以很容易地从 feed 视图中显示有限的细节，直到从服务器获取完整的细节

在我们的应用程序中可能会有这样的情况，我们有一个从列表中选择的事物列表，在单击一个项目时，我们应该加载该项目的全部细节。这就是 NIL 模式*(展开待披露😜)*将进场。当用户单击特定项目时，获取列表视图中所有可用的数据，并将其放在详细视图上，当用户消化显示的数据时，仅获取要在详细视图上显示的剩余详细信息。

将上述模式命名为**近瞬时加载(NIL)** 模式😉！

通过实现这一点，我们甚至可以完全消除每次用户点击项目时都会飞溅的加载微调器。有了这一点感知性能的提高，用户可能会觉得页面呈现是即时的💪

下面是 NIL 模式在 Ember.js 应用程序中的实现，可以被任何框架继承。

我们的示例应用程序是一个博客站点，它有一个博客帖子列表。对于普通的实现，该站点类似于👇

[![usual loading pattern](img/83a3bddaeafaa8e33f9ef20e50e80f4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CuhmFp6R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q6rzlkdcbeh1m47hp252.gif)

每当我们点击任何帖子，等待数据从服务器加载时，就会出现加载飞溅。这个加载时间取决于各种因素。要实现 NIL 模式，首先，我们需要消除中间加载子状态(点击 post 后的加载屏幕)，然后将数据从列表页面传递到细节页面。加载子状态是由于网络请求获取来自*模型*钩子的整个 post 细节。如果我们抑制模型挂钩，那么加载屏幕将不会被拉进来。太好了！但是，怎么做呢？

在转换到细节视图时，如果我们传递模型数据，细节页面中的模型钩子将不会被调用，相反，从列表页面发送的数据将被用作模型。

```
// routes/posts.js - Before

this.transitionTo('post.details', post.id); 
```

Enter fullscreen mode Exit fullscreen mode

```
// routes/posts.js - After

this.transitionTo('post.details', post); 
```

Enter fullscreen mode Exit fullscreen mode

这里，`post`将是来自列表页面的有限数据。这里需要注意的一点是*数据需要和我们在详情页维护的模型一样进行归一化*，以避免破损。

例如，如果您将详细信息页面中的文章数据组织到一个带有关键字`details`的对象中，那么您必须正确地对其进行规范化。

```
// routes/posts.js

let postDetails = {
    details: post // from the list page
}
this.transitionTo('post.details', postDetails); 
```

Enter fullscreen mode Exit fullscreen mode

如果你做到了这一步，那你就太棒了！现在，您的详细信息页面应该立即加载，没有任何加载子状态。高保真音响🙌

但是你也可以注意到我们的页面将是半生不熟的。别担心！我们会解决的。如前所述，当用户消化可用数据时，我们必须获取剩余的数据。为此，我们可以在`setupController`钩子内触发一个数据获取来取出剩余的数据。

```
// routes/posts/list.js

setupController(controller, model) {
    this._super(...arguments);
    let postId = model.details.id; // model is the passed data from the list
    controller.get('fetchPostMeta').perform({ postId });
    controller.get('fetchComment').perform({ postId });
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我使用了`ember-concurrency`任务来获取剩余的文章细节和评论。在任务完成和数据加载到 UI 之前，我们可以在适当的位置使用加载符号。同样，我们必须正确地规范化延迟加载的数据。再次提高感知性能的一个额外技巧是尽可能使用 skeleton loader。Ember.js 中还有另一篇关于[骨骼装载的伟大文章，作者是](https://emberway.io/skeleton-screen-loading-in-ember-js-2f7ac2384d63)[劳伦](https://twitter.com/sugarpirate_)。查看详细的实施细节😃

在实现了 **NIL 模式**之后，我们的示例应用程序将如下所示:

[![NIL Pattern implement](img/30ba676e7fa8ce9c169cacde2e9d5c85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yX4QH2eG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y16ayq8e561lklzud1gc.gif)

您可以看到博客的作者以及评论已经被延迟加载。这可能比以前的版本好多了，在以前的版本中，用户必须等待一段时间才能在屏幕上看到一些内容。

希望这能帮你节省用户的时间🙂这是一个巨大的胜利！😉完成的旋转可以在这里找到