# 我对拉请求的首选合并策略

> 原文：<https://dev.to/igorsantos07/my-preferred-merge-strategy-for-pull-requests-1lo5>

在我以前工作过的每一个像样的团队项目中(有 PRs 的地方)，合并策略是将所有的提交压缩成一个，在主分支中完成。

开始时，感觉很奇怪，因为“我所有的努力工作和有组织的提交”会变成一个单一的、标准化的各种 blob。后来我习惯了这种流动，嗯，不管怎样，对吧？*起作用了*。

但是，到了该由我来决定一个团队要开始走的路的时候了。问题来了，为什么壁球会合并？为什么是 rebases？

正如人们所说的，首先你抗拒，然后你接受，然后你质疑正在发生的事情。

[![Squash diagram](img/39220e0fe3f827725bf912e36fa128a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M_QWfbWQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://help.github.com/asseimg/help/pull_requests/commit-squashing-diagram.png)

因此，为了帮助理解我为什么选择 Squash Merge 作为我们存储库的默认设置:

挤压合并通常更受欢迎，这不仅是因为“平坦的历史”(这通常更容易使用任何工具进行导航)，而且实际上，因为历史变得更加清晰和“直截了当”，没有开发期间常见的所有部分提交。
如果挤压正确完成，您仍将在挤压提交中维护所有提交的文本，因此您仍可以获得关于该功能开发的一些细节，而不会堵塞您的`master`历史。

人们还应该记住，历史不仅用于在时间线/树上查看代码进度，还用于*责备变更*。即使您可以使用工具或额外的选项来“忽略”部分提交，责备可能不会——或者至少会变得过于复杂。

我做了一些快速的研究来查看关于这个主题的讨论，我可以将你链接到两个参考文献，这两个参考文献表明了我的观点，并且我用来加强我上面的论点:

[在 Git 工作流的 CLI 工具上回答关于挤压偏好的问题](https://github.com/reenhanced/gitreflow/issues/52#issuecomment-28437323):

> *[...]久而久之，这才是我们真正关心的历史。谁做出了这种改变，为什么做出这种改变。挤压合并允许我们这样做，同时仍然给我们所有的开发人员以最适合他们的方式进行开发的个人自由。*

[关于公关合并策略的 GitHub 手册](https://help.github.com/articles/about-pull-request-merges/):

> 您可以使用 squash 和 merge 在您的存储库中创建一个更加简化的 Git 历史。在处理一个特性分支时，正在进行的工作提交是有帮助的，但是在 Git 历史中保留它们并不一定重要。如果您在合并到默认分支时将这些提交压缩成一个提交，那么您可以通过一个清晰的 Git 历史来保留最初的更改。

* * *

*[(这也贴到了我的介质上)](https://medium.com/@igorsantos07/my-preferred-merge-strategy-for-pull-requests-6a9db5b0acd5)*

最美好的祝愿无冲突合并！