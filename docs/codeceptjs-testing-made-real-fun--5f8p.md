# CodeceptJS:测试变得非常有趣😎

> 原文：<https://dev.to/droidmakk/codeceptjs-testing-made-real-fun--5f8p>

所以我听说了这个叫做 [CodeceptJS](https://codecept.io/) 的新工具，它已经存在一段时间了。我以为它会类似于*木偶师*或*硒*

然后我浏览了它的文档，文档中说你可以轻松地切换框架而不需要修改代码，或者只需要提供特定的配置就可以在多种框架和浏览器上运行它。它可以进行 API 测试，不管有没有浏览器自动化。测试您的 android 应用程序。

🤔这很有意思。

PS:如果你读得更多(文档),事情会比你看到的更复杂👀。

> 最后，您将知道如何使用 Codecept JS 为您的 web 应用程序✅运行一个基本的浏览器自动化测试。

所以我决定亲自动手👐。为了开始，我必须运行一些先决条件检查。基本的事情是我应该安装**节点**和 **npm/yarn** 。

`node -v`
`npm -v`
T2】

然后安装名为 **selenium-standalone** 和 **webdriverio** 的包作为全局包

> 对于纱线
> `yarn global add selenium-standalone webdriverio`
> 
> 对于 npm
> `npm install --global selenium-standalone webdriverio`

[![Selenium and webdriverio installation](img/4d9673db86f6ceb4b5898aab39b89ffd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0RhUISM2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4k3LUeN.png)

现在我们将允许 selenium-standalone 安装它需要的依赖项。为此，我们需要运行以下命令。

`selenium-standalone install`

现在，让我们安装我们在这里的目的！🙇

> 对于纱线
> `yarn global add codeceptjs`
> 
> 对于 npm
> `npm install --global codeceptjs`

然后在您的主目录或任何项目目录中创建一个文件夹。

`mkdir google-logintest`
T1】

现在让我们生成一个测试样本。

`codeceptjs init`

一旦你这样做了，你将通过一个互动的问题集。
首先

您将在这里指定测试文件的位置。现在，我们将确定命名约定。
[![Codeceptjs init](img/a29f7ca4c6caed1ee5b02337f4ff34bc.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--1UPPvJSA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/gkT6kmu.png)

现在，您需要指定您将在后台使用的驱动程序。

> CodeceptJS 美妙之处在于，您可以并行或顺序地在具有多种配置的多个驱动程序上执行，只需一点点定制，无需额外的代码😇。

我们现在将使用基于 **selenium** 的 **webdriverio** 。

[![Select Driver](img/8e1d9a057d204766a691cfed2c733ca3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nWQc8uWX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/v9QKDqN.png)

现在输出的位置。在执行您的代码时，如果任何特定的命令失败，则执行时的屏幕截图会被获取并保存到这个特定的目录中，失败的步骤作为映像的名称。
甜的没错！

[![Location of outputs](img/9cb6c3deeb8136387fca66ef14d02273.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nrum7RUW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Pq16WHx.png)

现在，这将是我们要扩展来编写代码的对象。
当我们开始编码时，你会明白那是什么。

[![Extend with custom steps](img/3e9f2fc38f6678e8f06729b78fac2aab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K_yAP4gF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/p64pMhY.png)

在这里，您将指定本地化。我们现在就用英语吧！

[![Localization](img/a29e7c0d51ecdf44d39457d9e1d07c5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9v-iQOzx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/lUJmWtu.png)

这是我们将放置自定义步骤的步骤文件。

[![Custom steps location](img/9fe9dd61900ac8942d54d78540cfc659.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I7iZobdM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7oJE55u.png)

现在是我们要测试的网站的 URL。我们就用*[https://google.com](https://google.com)T3】*

[![URL of Site](img/c0c76b7b77a2e225fa23cf2138dc26ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uyUf2dzf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/xG1Rrbx.png)

最后是我们将要使用的浏览器。现在我们将使用 chrome
只需按下`enter`键并接受默认设置。

[![Select Browser](img/1f7779fc456b0e1357c60bf7bcbdbe91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2f50ERmL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/D8cZYHk.png)

请注意，一旦您完成了设置，您的目录将如下所示。👇

[![Directory tree](img/1ac75da60322947a9bf0961832cd61f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nb4JfWwX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Hynt5BG.png)

如果你看一下 **codecept.json** 文件，它将包含我们在上述步骤中指定的配置。

[![codcept.json](img/00d32c26cede10e946324b23d120bf0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KbzlORzo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Jzi7DxJ.png)

✔️，舞台已经搭建好了，我们将开始测试。Codecept JS 提供了一个使用 CLI 本身生成测试的特性。

`codeceptjs gt`

另一个交互式菜单...现在我们将文件名命名为 login_google。

[![Login Google](img/423cb77661d3c94275854c383396aef0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H-GDBktC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/up6u3EE.png)

让我们保留默认的特性名称(**登录 google** )。

[![Feature Name](img/6b448ec6136472c3cf8b8396d930dcda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--feyvvFSq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4FoommA.png)

现在，这将为我们生成如下测试文件。看起来像这样。

[![Login Google file](img/d1d2b88ba7570c7e9ba9a7cc564fe09b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_TgX1T2b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ytKOWqu.png)

让我们现在做一些代码🙇。

因此，编辑您生成的测试文件，如下所示。
用你的*电子邮件 id* 和*密码*替换标记的标签

[![Code](img/7a5596ab9e27c932b0c8bc40d8e98687.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LbaPOwE7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/KYmimFS.png)

如果你试着一行一行的解码。
✔️ `I.amOnPage('/')`你在页面上[https://google.com](https://google.com)T10】✔️`I.click('Sign in')`我正在点击一个名为**的按钮登录**t13】✔️`I.fillField('xpath of email field','your email-id')`键入我的电子邮件 id
✔️ `I.click('xpath to next button below')`点击文本字段下面的**下一步**按钮
✔️ `I.wait('2')`我正在等待两秒钟，等待页面加载。
✔️ `I.fillField('xpath ot password field','your password')`填写我的密码
✔️ `I.click('xpath to next')`我点击**下一步**按钮登录。

> 你可以在开发者工具**【F12】**
> 中右键点击元素代码来获得元素的 x 路径👉复制👉复制 XPath

现在保存并退出`[ESC] + :x`(如果在 **VIM** )。

打开一个新的终端并运行`selenium-standalone start`
现在从你的测试目录运行`codceptjs run`

成功执行后，您将获得如下内容。

[![output](img/43f0591f49836b98e68dc6c3f16f5a3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EqNnZ-M5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/UhLoQGI.png)

🎉恭喜你。现在，您已经使用 codeceptjs 完成了第一个自动化测试👏,

首件...评论家非常欢迎✌️！