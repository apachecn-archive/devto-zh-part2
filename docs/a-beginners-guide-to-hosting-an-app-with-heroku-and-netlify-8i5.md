# 用 Heroku 和 Netlify 托管一个应用程序(如果你以前从未做过)

> 原文：<https://dev.to/lindakatcodes/a-beginners-guide-to-hosting-an-app-with-heroku-and-netlify-8i5>

在过去的 7 个月里，我一直在努力学习谷歌/ Udacity 移动网络专家奖学金项目。这是一次紧张而有益的经历，我学到了很多有趣的东西！

我们所有的 3 个项目都集中在创建一个餐馆评论应用程序上。我们得到了启动代码和一个本地服务器，并逐渐使项目更易访问，响应更快，并允许它离线工作。我们处理了服务工作者、本地缓存和索引数据库。用户需要能够将一家餐馆标记为收藏，并在线或离线发布新的餐馆评论，并在重新连接时将这些离线编辑发布到服务器。

很自然地，完成了所有这些并从这个项目毕业后，我希望能够与我的作品集上的其他人分享这份辛苦的工作。我必须能够展示我得到了这个工作的原型，对不对？这个站点有一个面向客户端的部分和一个服务器部分(虽然我们从来没有连接到数据库——只是有一个 db 文件，我们的服务器代码可以访问和存储信息)。

我以前从未托管过“应用程序”——任何需要处理后端服务器的网站。我不知道从哪里开始。

在我的班级 slack 小组中看到一个类似的问题后，似乎用 Heroku 做后端，用 Netlify 做前端是一个很常见的解决方案。我目前使用 Netlify 来托管我的投资组合网站，所以这似乎是一个不错的解决方案。但是我从来没用过 Heroku。

我第一次得到这个网站是关于如何在 Heroku 上部署一个 Sails 应用程序(这是我们得到的服务器库)...

....但说实话，这对我来说似乎没什么意义。这很简单，但肯定更适合那些可能已经有一些主机或制作网站经验的人。

所以我搜索了一下，找到了这两个帖子:
[部署 JavaScript 应用第一部分](https://auth0.com/blog/the-complete-guide-to-deploying-javascript-applications-part-1)
[部署 JavaScript 应用第二部分](https://auth0.com/blog/deploying-javascript-apps-part-2)

这些帖子比我的项目需要的信息多得多，但它给了我运行所需的信息。我在这里链接的所有图片都来自那些博客帖子，所以所有图片都归功于他们！

我想其他人可能和我处于相同的位置——一个新手在不止一个网站上托管一个应用程序，并把它们彼此连接起来。因此，这里是我用初学者的术语介绍如何在 Heroku 后端站点和 Netlify 前端站点之间建立一个简单的连接！

### 1。创建一个 Heroku 帐户

首先，如果你还没有一个账户，去 Heroku 的网站注册一个。

### 2。从 GitHub 获取您的后端代码

我没有把我的服务器代码放在我自己的名字下，所以我把最终的项目服务器分支到我的名字下。这样我知道回购不会在我不知情的情况下突然消失或者发生变化。

### 3。用 Heroku 连接你的仓库

好吧，这对我来说是个棘手的部分。这些图片应该会有帮助！

a.登录到您的帐户后，您可以单击右上角的新建，然后单击创建新应用程序。
[![Create New App Screen](img/bb3b29709097eab34e5a1906ce466b56.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--r1S8iI2N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.auth0.com/blog/ultimateguide/create_new_app.png)

b.您将为您的服务器命名，然后单击底部的创建应用程序。
[![Name and Create Screen](img/ec5c1a064f8aed67fe5f03e32a9cdc34.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s---qOegGVP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/herokunewname.png)

c.这将带你到一个有一些选项的页面。因为我们托管的是一个只有一个贡献者的简单站点，所以你现在不必担心管道问题(这就是你如何处理一个由多人定期维护的项目，所以你可以通过一个阶段化的过程来推动事情，进行测试等等)。

接下来你要做的是将它连接到你的 GitHub 帐户，这样我们就可以访问后端代码了。
[![Connect to GitHub screen](img/df25dc78ac93cdfe7fbde404d4bd225a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--1PFT0PT5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/connectogithub.png)

d.一旦连接上，您就可以搜索您的服务器存储库的名称，并单击 connect，这样，正如您可能猜到的那样，它就会连接到该存储库。
[![Connect screen](img/9886f5ebf232b4faa669281cb57b02b0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--dQGpDWs6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/clickonconnect.png)

e.这里的最后一件事是设置部署。您将选择您想要它使用的分支(对于我的例子，主分支是我想要的)，并且您将想要点击 Enable Automatic deployments。(您不需要选中此图中显示的“等待 CI”链接，除非您已经设置了要在代码更新之前运行的测试。)
[![Automatic Deploys screen](img/bf609491e786b914117057adae87525f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--OvRF1dgd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/enableautomaticdeploy.png)

f.然后，我首先错过的部分是:您需要通过单击 Deploy Branch 来运行手动部署，以启动并运行您的站点！一旦完成，在底部会有一个按钮来查看您的网站。恭喜-您的服务器现在已经启动并运行了！
[![Deploy and view screen](img/e0917df47cce2b90467f046ab05b1a61.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zn_HQsbf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/deployprocess.png)

### 4。测试你的新网站！

现在，您可能想要测试它，确保它连接正确。因此，我会转到您为获取信息而设置的一个端点，并确保它显示您期望的数据。对于我的项目，我获取了餐馆端点，并确保我可以看到餐馆列表。(这看起来有点像 my-server-name.herokuapp.com/restaurants.)

### 5。用新的 URL 更新前端代码

好了，完成一半了！现在您将转到托管您的前端代码的 GitHub 存储库。在您链接到您的服务器的本地主机版本的任何地方，您将使用现在托管您的服务器的 Heroku 链接进行更新。(还记得我们点击上面的“向上查看”吗？将您带到的链接是您的新端点。)

在任何你认为链接到你的本地主机的页面上进行搜索——我第一次错过了一些地方！

### 6。创建网络帐户

好的，现在我们将在 Netlify 上建立一个[账户。](https://www.netlify.com)

### 7。启动你的网络生活网站

这里的另一个多部分步骤——这个更简单！

a.一旦你的帐户被创建，你将点击右上角的来自 Git 的新站点。
[![Create Site screen](img/5c2f1ab03d4a467beea0240db20e4fd2.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--KgsEg0iI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/ncreatesitefromgit.png)

b.从这里，您将再次选择从 GitHub 部署并连接您的帐户。
[![Connect GitHub screen](img/986e35503b88485c8b99a40f7454dcff.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--39kt-fSG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/nchoosegithub.png)

c.然后，您将选择前端存储库....
[![Pick repo screen](img/416d95756a5600641e4f9791ed089196.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ArMNHDbW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/nselectrepo.png)

d....并选择您的设置。我再次用我的主树枝作为 Netlify 阅读的主要树枝。

您还将设置另外两件事情:
如果您有一个构建步骤(我在我的项目中使用 gulp 来编译我的 CSS 和 JS 文件)，那么您将进入构建您的项目的任务。

您还将设置(或创建)一个文件夹，Netlify 将使用它作为您的主/根文件夹。据我所知，这是 Netlify 能够看到的唯一文件夹，所以你需要所有的文件在这里——HTML，CSS，JS，图像，服务工作者文件，manifest.json....您的前端网站使用的任何内容，您都希望放在此文件夹中。我把我的命名为 dist，就像图片一样，但是你可以随意命名。
[![Build commands screen](img/6dd4c68301c7e127060e432b1f07eb3e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--UK7BVmzK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/netliftyputbuildcommands.png)

e.然后点击底部的部署网站，让这个过程开始！

### 8。重命名您的项目

默认情况下，Netlify 为你的站点选择一个随机的名字。您可以点击仪表板页面上的站点设置(如下图)并根据需要进行更改。
[![Deployed screen](img/b100e346d881dd853321a572ad7dec89.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--y2EY3s2u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/jsdeploy/sitedeployinprogress.png)

### 9。恭喜——主要步骤完成了！！

看一眼你的 Netlify 站点，看看你的站点是否如你所愿地出现了！现在故障排除开始。:)

## 期末笔记

我最大的问题是意识到我需要调整我的构建步骤来包含我需要的所有文件。起初，我在那里只有我编译的文件——所以当我访问 Netlify 链接时，什么也没有显示。所以我不得不添加我的 HTML 文件，以及我的图片和我的根文件夹中的其他随机文件。因此，我修改了我的 gulp 构建任务，以便在我做更改时复制这些文件。这是最让我犹豫的一步——所以请确保将前端需要的所有文件添加到您的 dist 文件夹(或您命名的任何文件夹)中，以便一切都能正确显示！

我还错过了一些我一直在进行的本地主机调用，所以不得不回去替换我的新 Heroku URL 几次。因此，请务必检查您的网站使用的所有功能，并确认它们的工作。:)

使用 Netlify 托管的好处在于，每当你将更改推送到 GitHub 时，它会自动运行一个新的构建并更新你的站点，所以看到你的新更改非常容易。:)使用 Heroku 也是一样——当我意识到必须进行手动部署来启动它时，我几乎不需要做任何事情，我所做的一些小更改(主要是我在本地对数据库做的小更改，后来忘记了)也会被自动推送和更新！

我也对我的布局做了一些小的调整——因为我现在把它放在网上了，我在手机上测试了一下，发现有些东西显示得不太对。一定要让那些款式好看！

我希望这一切都有意义！如果你有任何问题，请随时问我，我会尽力帮忙的。下次我有项目要主持的时候，也可以随时给我一些改进工作流程的建议。

## 快乐大厦！

(如果你想看看我的项目，它就在这里。)