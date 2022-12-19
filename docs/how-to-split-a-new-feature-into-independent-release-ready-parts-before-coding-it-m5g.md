# 在编码之前，如何将新特性分割成独立的(发布就绪)部分

> 原文：<https://dev.to/geshan/how-to-split-a-new-feature-into-independent-release-ready-parts-before-coding-it-m5g>

作为软件工程师，我们总是面临一个问题，如何在编写代码之前将任务分解成独立的发布就绪部分。项目经理也有责任以最佳方式记下小而独立的任务/单。但是，这不是我们所得到的，特别是如果产品是新的，还没有发布生产。根据我的经验，拥有这种语言和框架不可知的技能将有助于你成为一名更好的高级软件工程师。

有时任务描述是一行程序，你可以根据自己的喜好进行解释。所以，这篇文章将帮助你解决这个问题，在给定一个任务的情况下，你如何计划将它分成**个独立发布就绪的**部分。不考虑语言和框架，软件设计和架构在你的软件生命中扮演着重要的角色，需要一个强大的基础来构建一个更持久的软件系统。

[![How to split a new feature into independent (release ready) parts before coding it](../Images/cdcc590a08fb33b2a11ef7e5fad964c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--phW1PDNF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2Ar3wid07Aub-BaBUA.jpg)

## 一些假设

这篇文章的假设如下:

1.  像 JIRA/雷德明/特雷罗这样的项目管理系统被用来将任务定义为票/卡。
2.  一个版本控制系统(VCS)正被用于你正在工作的项目。
3.  有一个合适的团队结构，如果你是软件工程师，在你之上有一个团队领导。
4.  可能你的团队遵循敏捷软件开发方法

## 如何分割特征

举个例子，我们以项目代码为 PX 的项目 X 作为你正在做的项目。你的任务是 PX-4，开发/编码用户系统(用户普遍存在于每一个软件系统中)，其内容如下:

```
Title: Develop a user system for the back-end
Description: Users should be able to create, view, edit and delete other users in the system. 
```

Enter fullscreen mode Exit fullscreen mode

现在，在你准备好完成这项任务后，你的下一步应该是什么？跳到你的桌子上开始写代码？肯定不会。

> 你需要组织和讨论你的每一项任务，并有一种思维模式，将它分成独立的发布就绪的任务，首先在你的脑海中，并最好将其记录在某个东西上。

那你是怎么做的呢？

[![How to split a new feature into independent (release ready) parts before coding it](../Images/0985b9d57f3a81d45e632aebb11b1b21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LXUJ26KJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3026/0%2Awl5sY2BxUMcz4tey.jpg)

## 认为积垢

CRUD——创建读取更新删除是按照给定的顺序进行的，因为在我看来，当你开始为一个新特性编写代码时，你应该按照这个顺序一个接一个地执行每个步骤。

> 打个比方，就像给你一块未切开的面包，你不会开始咬它。你先把它切成合适厚度的片，然后一片一片地吃。

当你为一个特性写代码时也是如此。关于后端的用户系统，请考虑您将首先完成创建用户和显示用户部件，然后只处理编辑和删除部分。

同样的技术可以移植到一个 API 开发场景中，在这个场景中，您将首先执行 POST，然后执行 GET，然后执行 PUT 和 DELETE。您可以很容易地用 curl 测试它，并看到您的 POST 将数据持久化到数据库表中，并且您的 GET 以期望的格式用正确的数据进行响应。PUT 和 DELETE 也是如此。

## 确定问题的范围并概述解决问题的步骤

这很重要，你需要为任务定义一个范围，你需要写下你将如何编写代码来完成任务。

> 最好与您的团队领导讨论解决方案的范围和步骤，沟通对于软件工程师来说总是很重要的。

您可以不太关心非功能性需求，但它必须得到您的首席开发人员的同意。例如，我在 markdown 中添加了一个[要点](http://bit.ly/1IAbO3t)，概述了 PX-4 用户系统解决方案的范围和步骤，也包括在下面。

```
## Scope and steps for user system ticket - PX-4

### Proposed steps to develop the ticket:

1\. Add new table to the main db users table, with needed columns (id, email, password, first_name, last_name, restore_key, created_at, updated_at) 
  1\. Add a migration for the columns mentioned above
  2\. Tests the migration on local and also record the time it takes to run locally.
2\. Add form to create new user and make persist it in the db table. Password will be encrypted using SHA512.
  1\. Do basic validation like required
  2\. Enhance validation to accept only email in email field, accept only alphabets (not numbers) for first name and last name. 
3\. List users in a [master detail](https://en.wikipedia.org/wiki/Master%E2%80%93detail_interface) tabular format
  1\. Create the listing page with id, email, first name, last name, created at, actions column as row for each user
  2\. Create the detail page with id, email, first name, last name, created at, updated at in a detail table format
  3\. Add pagination to users with fixed 10 users per page
4\. Edit users with the same form and validation as create, will update only one user
  1\. Add edit link in the users listing page
  2\. Adjust validation to make password optional
  3\. Passwords will be updated only if provided, if empty old password will not be changed
5\. Delete user will delete single user from db
  1\. Add delete link in the user listing page and delete button in the user detail page, both should confirm before deleting
  2\. After deleting from user detail page, user will be redirected to user listing page with a flash message on top

### Other consideration

1\. User will not be able to delete self
2\. Roles are not taken into consideration for this ticket, it is part of PX-7
3\. Front end user login and registration is part of PX-8 
```

Enter fullscreen mode Exit fullscreen mode

因此，这项任务被分为 5 个主要部分，每个部分有 2-3 个子部分，这让你清楚地知道需要做什么。现在，你可以估计每个子部分，加起来就是整个任务的总估计。尽管您可能有开发登录和注册部分的冲动，但是您需要停下来，将注意力集中在满足所提供的估计的范围上。专注对于任务的顺利完成是必要的。

## 独立发布准备好的零件

如上所述，您应该将任务分组为独立的发布就绪部件。您认为什么是发布就绪取决于许多因素，例如:

*   该项目已经投入生产了吗？
*   有多少人在用？
*   有没有可以开启或关闭功能的功能开关？(如 cookie 或电子邮件地址)

因此，您将再次需要与您的主管讨论什么可以被视为发布就绪。假设对于我们的示例用户系统任务 PX-4，讨论基于这样的理解而结束，即由于该项目没有向公众发布，因此独立任务是:

1.  创建用户并查看用户功能是否通过验证(要点中的第 1、2 和 3 点)

2.  更新和删除可以在下一个增量中完成(要点中的第 3 点和第 4 点)

对于你自己作为软件工程师的工作，如果你写测试，那么最好为任务分解中的每个要点写一个测试。如果你做测试驱动的开发，那么你将首先编写测试，如果你做测试支持的开发，在完成任务后为每个任务编写测试，而不是在最后堆积所有的测试。这也能节省你的时间。

## 每天推送，交流

假设 git 正被用于这个项目，强烈建议您每天结束推送。沟通始终是成功的关键，因此根据需要，您可能需要更新您的团队和团队领导的进度。如果你的团队遵循 scrum，并且有每日站立会议，那么更新你所做的事情会更容易，因为大任务是以一种容易跟踪进度的方式组织的。

## 结论

仅仅对一个特性进行编码并不能完成任务，你需要在测试阶段对它进行很好的测试，如果项目是在生产阶段进行的，那么测试也需要在生产阶段进行。

尽早提交，经常提交是一个非常好的实践，如果你有一个持续的集成设置，你将在整个测试套件中尽早得到反馈。还建议经常遵循 release 早期发布，如果您能够完成独立的发布就绪部分，并且很少/没有问题，这是可能的。然后，您可以在编写特性的过程中逐步发布。检查代码，并根据需要针对该特性重构代码也是必不可少的。

我希望在完成所有这些之后，我们的示例后端用户系统任务 PX-4 能够使用自动化部署在期望的环境中顺利部署。该任务已经过充分测试，没有报告该任务的进一步问题。是时候结束这个任务并继续练习你所学的新技能了，首先把一个给定的任务分割成独立的发布就绪的子部分。祝你好运！

* * *

*最初发表于[geshan.com.np](http://geshan.com.np/blog/2015/07/how-to-split-a-new-feature-into-independent-parts-before-coding-it/)。*