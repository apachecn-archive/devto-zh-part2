# 代码审查

> 原文：<https://dev.to/funkysi1701/code-reviews-6fj>

审查代码是一个很好的习惯。代码审查有助于在团队成员之间共享知识，并有助于在 bug 进入生产之前将其捕获。但是如何养成复习的习惯，避免我们没时间做这种心态呢？

Visual Studio Team Services (VSTS)有一些很棒的选项，可以帮助将代码评审变成第二天性。

#### 拉取请求

许多源代码控制系统都有拉请求的概念。这是您请求其他人审查您的代码的地方，通常是在一个分支中，如果他们批准了，就将它合并到一个主分支中。

要在 VSTS 创建拉请求，请转到代码部分并选择拉请求。通常，VSTS 会建议向哪个分支机构发出拉取请求，如果您没有看到，请单击“新建拉取请求”按钮。

选择要合并的分支和要合并的分支(通常从特征分支合并到主分支)。给你的拉请求一个标题和描述，并选择谁应该审查你的代码，这可以是一个人或一组人。您还可以审阅所有将要审阅的更改，以便在审阅之前进行最后的修改。

现在，如果您和我一样，您希望在创建拉请求后立即合并您的代码，没有什么可以阻止您检查自己的代码，并在自己的拉请求上单击批准和合并。然而[分支政策](https://docs.microsoft.com/en-us/vsts/git/branch-policies?view=vsts)是解决这个问题的方法。

#### ![Branch Policy](img/b955032720936c7c458d84c6cc756c52.png)分公司政策

分支策略允许您指定如何合并代码。

转到 VSTS 分行列表，选择分行政策，你会看到一大堆选项来定制合并过程。如果您在 master 分支上这样做，那么如果不通过 pull 请求，您将无法向 master 提交任何更改。

第一个选项使您能够选择您的代码需要多少评审者。如果没有其他人参与你的项目，最好不要设置这个，但是对于其他人来说，设置至少一个人来审查你的代码是一个很好的实践。

接下来，您可以确保您的拉请求链接到一个工作项，这有助于确保您实际上是在修复问题，而不仅仅是为了修改而修改。

“检查注释解析”是一个很好的启用设置。这确保了如果您的审阅者评论了您需要在此处更改这一行，它会确保您这样做。

强制合并策略允许您在快进合并或挤压合并之间进行选择。

通过生成验证，可以使用您配置的生成定义来生成代码。这是在合并之前检查代码构建或测试是否通过的好方法。

最后两个选项允许您指定代码审查者和第三方外部服务。