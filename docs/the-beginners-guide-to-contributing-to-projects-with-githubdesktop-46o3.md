# 使用 GitHub 桌面参与项目的初学者指南

> 原文：<https://dev.to/highflyer910/the-beginners-guide-to-contributing-to-projects-with-githubdesktop-46o3>

提高编程技能的最好方法之一是在开源项目中进行协作。第一次，这可能会很吓人，我自己也很害怕。我认为我还不够好，不足以向更大的项目提交代码，但我在这里告诉你不要担心这些事情，不要害怕做一个小的改变和提交一个大项目的拉请求，因为它很可能会帮助别人。

## 寻找项目

第一步是找项目。只要在 GitHub 的搜索栏中输入`label:beginner`、`label:easy`、`label:starter`或`label:first-timers-only`，就会出现项目开发者认为适合初学者的问题。一旦你找到了你喜欢的项目，是时候投入到你作为开源开发者的新生活中了。

## 叉一个储存库(又名制作一个副本)

第二步是派生存储库。为此，点击项目 GitHub 页面右上角的`Fork`按钮。
[![Alt text of image](img/d425fe22e684d4bc96a9f46e35a2143a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xcyj82-I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrp45ackvsukvv4lqkpl.png)

现在，您拥有了项目的个人副本，可以在不更改任何其他人的工作的情况下进行编辑。

## 克隆存储库(也就是将副本下载到您的机器上)

对于这一步，你需要安装 [GitHub 桌面](https://desktop.github.com/)应用程序(在本教程中，我们将使用 GitHub 桌面应用程序，尽管每个开发人员都应该了解任何版本控制系统)。打开你的 GitHub 桌面应用，点击`File`，然后点击`Clone repository`。你可以在 GitHub 上看到你的库和分支列表。选择分叉的存储库并点击`Clone`。
[![Alt text](img/bffdbd01416528681594c1988c1ed971.png)T8】](https://res.cloudinary.com/practicaldev/image/fetch/s--RGxH88fw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ka669b71k7grb8nud3b.png)

现在您有了项目的本地副本。

## 创建新的分支

每个存储库都有一个默认分支。当您提交时，您会得到指向您上次提交的主分支。当您有自己的分支时，您可以在不影响主分支的情况下进行更改。点击`Current branch`，点击`New`，给你的分支一个名字。
[![Alt text](img/7d450c89336cce8b9031d481f4a88988.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lr7f9lkF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1iv2c750pmz1ux9flyn9.png) 
然后创建一个分支。

[![Alt text](img/c12ad264306315c6cc5328d00ecba0df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eqKeO6L9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/azr2zblkm83kktqnxj75.png)

## 做一些工作的时间

若要用您喜欢的代码编辑器打开要编辑的文件，请在您的计算机上找到您的项目。克隆的默认目录(对于 Windows)应该是`C:\\Users\(username)\Documents\GitHub`。现在，您可以开始对它进行更改了。

## 提交您的更改

完成编辑后，你应该将你的修改组合成一个 commit 并发送到 GitHub，然后提交它们与主项目合并。打开你的 GitHub 桌面应用，你会看到 Git 已经记录了所有保存的更改。要提交，您必须在`Summary field`中键入提交消息并添加描述。然后单击按钮提交您的更改。
[![Alt text](img/03c10cca8cdfb00dd7d324bddc0c8cba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ek2rm7Gi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ve9oysek6rjsb9wn2l3m.png)

## 推动你的改变

下一步是将本地更改与 GitHub 上的存储库同步。您需要将您的更改推送到 GitHub。点击显示`Publish branch`的按钮同步更改。现在你的电脑和 GitHub 上都有了这个分支的相同副本。
[![Alt text](img/356ef7e9c727be0f6b273696ef526edc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y4ggGuzp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xdarcuosch8tmjdsnvgc.png)

## 提交您的 PR

下一步是提交您的拉取请求。Pull 请求通知项目维护人员，您有一些工作需要他们检查并最终添加到项目中。
[![Alt text](img/737fe1247b1d57acb7984a2a05a49d38.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--3QhGHgyj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aekfekv7rv7e3orpym7k.png)

在 GitHub 上进入你的 fork 主页，点击高亮显示的 PR 消息，有一个绿色按钮显示`Compare & pull request`。
[![Alt text](img/dd93d314a607c4969007b008e87492f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dgr7ozcw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ianoyci0k3mbd2zu9lmd.png)

写一个标题，在评论部分总结你所做的工作。写下“这是我的第一个拉请求”这样的话可能会有帮助。如果有任何反馈，请告诉我。”点击`Create pull request`并选中显示`Allow edits from maintainers`的复选框。

最后一步是耐心等待变化。请记住，可能要过一会儿才会有人检查您的工作。在那之后，你可能永远得不到回应，或者得到一个负面的回应。然后你可以尝试另一个问题，另一个项目。而你的作品一旦通过审核，就会被合并进去！你做到了！现在，您已经掌握了在 web 上为开源项目做贡献的技能。恭喜你！