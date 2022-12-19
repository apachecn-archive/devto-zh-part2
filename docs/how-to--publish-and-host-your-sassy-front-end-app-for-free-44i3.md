# [如何]:免费发布和托管您的时髦前端应用程序

> 原文：<https://dev.to/ritikesh/how-to--publish-and-host-your-sassy-front-end-app-for-free-44i3>

<figure>[![](../Images/79a420631622b9501fa4b0436bee8831.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B3rTEd14--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/400/1%2AzyNSb0UXhP8TfxYbj-GNWg.png) 

<figcaption>来源:[https://vuejs.org](https://vuejs.org)</figcaption>

</figure>

就像开发社区中的其他人一样，我想学习一些“现代前端 Javascript 库”,以便在不断变化的 Web 技术世界中保持相关性。

经过几个月的反复阅读，这篇文章帮助我决定继续开发 Vue.js。对于那些不知情的人来说，vuejs 是当今世界三大前端框架之一(React、Angular 和 Vue ),并且已经连续两年成为最受欢迎的[之一。因为有很多帖子涉及 vuejs 和其他 Javascript 趋势，所以我将在这篇帖子中尝试涉及一个不太熟悉的话题。](https://medium.com/javascript-scene/top-javascript-libraries-tech-to-learn-in-2018-c38028e028e6)

我开始摆弄 [vue.js](https://vuejs.org) 有一段时间了，在 [youtube](https://www.youtube.com/watch?v=Fa4cRMaTDUI&list=PLWKjhJtqVAbnadueQ-C5keMQQiQau_i0D) 上看了几个视频，有了一个构建演示应用的想法(你猜对了，是一个待办事项)。虽然官方文档在其示例部分包含了一个[简单的待办事项列表](https://vuejs.org/v2/examples/todomvc.html)，但我想尝试一些更复杂的东西，一些我一直想使用的东西——一个可重复的清单——用我的话说，一个面向对象的待办事项列表。经过几天的黑客攻击，我能够完成任务，并想向世界展示它，因为耶，我学到了一个新的前端库！。

作为一个主要的后端/运营人员，我的第一个显而易见的选择是“托管”应用程序。所以我去了我的 Heroku 控制台，安装了一个新的 node.js 应用程序。简单的谷歌搜索让我找到了这篇简单易懂的[文章](https://medium.com/netscape/deploying-a-vue-js-2-x-app-to-heroku-in-5-steps-tutorial-a69845ace489)，讲述了如何在 heroku 上部署一个静态的、只有前端的 vuejs 应用。我按照文章中的步骤，成功地启动了这个网站。哇呜。但是等等，有个小问题。Heroku 的自由层有一个限制。如果应用程序停止接收请求，分配的 dynos(计算实例)将进入睡眠状态。当然，有一些方法可以避免这些，但为了避免让我的应用程序一直保持清醒、为微小的变化构建和部署代码的不必要的复杂性，我决定走一条更容易成功的道路。

我早就听说过 github pages，并在上面托管了我的个人网站有一段时间了。由于这将是一个静态的网站，我决定将它放在 github 页面上。对于那些不知道的人，github 让你为你的开源项目创建网站并免费托管它们。你可以在这里了解更多[。](https://pages.github.com/)

首先，您需要创建一个名为“. github.io”的 repo。然后，此回购上的一个 index.html 文件将被用作 URL“[https://your _ github _ username . github . io](https://your_github_username.github.io)的根。然后，可以通过在这些存储库上创建一个“gh-pages”分支来创建单独的项目网页。然后可以在“[https://github _ username . github . io/repo _ name](https://github_username.github.io/repo_name)访问这些文件。

在我的例子中，gh-pages 分支是主分支本身的子集。我首先删除了不需要的内容(package.json、vue 特定的 src 文件、webpack.config 和其他配置文件)。然后，我从 dist/中复制了 build.js 文件，并将它放在我的公共文件夹中，并修改了 index.html 文件，以便在我的公共文件夹而不是 dist 中查找 build.js 文件。在提交更改并将分支同步到远程存储库之后，我能够在几秒钟内访问我的 todolist。通过这些最少的更改/工作，我能够以零成本托管应用程序，并且没有维护问题。

对于那些感兴趣的人来说，这个应用程序位于 https://ritikesh.github.io/todoer 的，回购的链接是:【https://github.com/ritikesh/todoer 的。

感谢您的阅读。