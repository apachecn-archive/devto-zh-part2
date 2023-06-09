# 使用 AWS (Amazon Web Services)实现无服务器 CI/CD 管道。

> 原文：<https://dev.to/sagar/implement-a-serverless-cicd-pipeline-with-aws-amazon-web-services-438f>

本系列文章主要讨论如何使用 AWS 服务实现无服务器 CI/CD，在实现过程中，我们将引入许多新的 AWS 服务。由于主题的复杂性，本文分为两部分。你阅读的文章的第一部分，我们将实现持续集成(代码构建项目，代码管道和 jest 测试用例)。

要遵循本文中提到的步骤，您应该拥有自己的 [AWS 自由层帐户](https://aws.amazon.com/free/)。

在开始实施本系列文章之前，您应该知道些什么？

1.  基本了解 [git 工作流程](https://sandofsky.com/blog/git-workflow.html)。
2.  我们正在用[代码构建](https://aws.amazon.com/codebuild/)、[代码管道](https://aws.amazon.com/codepipeline/)、 [S3](https://aws.amazon.com/s3/) 和[无服务器框架](https://serverless.com/)等实现 CI/CD。因此，您需要对 AWS 服务有很好的了解。
3.  如果你在这两个过程之间停滞不前，你的好奇心会帮助你摆脱障碍。

如果你不了解 AWS 服务，我会尽可能简单地向你解释。

## 让我们跳进理解 CI/CD

### 什么是持续集成(CI) /持续交付(CD)？

**持续集成(CI)** 是软件开发的开发和测试阶段。当开发人员对软件代码进行更改时，这些更改会立即被签入共享源代码。当代码被检入时，自动构建过程和测试被触发，以确保更改不会破坏软件应用程序。它帮助我们更快地发现编码错误，并且在代码合并到共享源代码之前避免错误。

**连续交付(CD)** 意味着随着编码的改变，新的特性或错误修复通过了自动化构建测试，那么我们必须在将改变交付给用户之前进行一系列的配置，并尽可能快速地交付。

在这篇文章中，我们将创建一个体重监视器应用程序，它要求每天的体重，并且我们将这个体重存储在数据库中。

我们将在 GitHub 创建一个新的存储库。给存储库起一个名字`weight-watchers`，可以随意跳过描述，然后点击`Create Repository Button`。在成功创建`weight-watchers`存储库之后，我们准备将这个存储库克隆到本地机器上来编写神奇的代码。根据您的方便，使用 SSH 或 HTTP 方法克隆存储库。

如果你坚持克隆存储库，点击这个链接[帮助你](https://help.github.com/articles/cloning-a-repository/)

`git clone git@github.com:sagar-gavhane/weight-watchers.git`

为了简单起见，我们使用 GitHub。如果您想使用 CodeCommit，请随意在 AWS CodeCommit 上创建一个存储库并克隆它。

### 什么是 AWS CodeCommit？

AWS CodeCommit 是由 Amazon Web Services 托管的版本控制服务，您可以使用它在云中私有地存储和管理源代码。我们可以说它是 GitHub 的另一种选择，唯一的区别是你不能公开你的库。与其他版本控制服务相比，CodeCommit 与 CodeBuild 和 CodePipeline 有很好的集成。

### 给项目添加必要的文件和包

补充。gitignore file 忽略运行时生成的跟踪文件夹和文件，或者一些秘密文件，以避免上传到存储库。将[https://www.gitignore.io/api/node](https://www.gitignore.io/api/node)的原始内容粘贴到已创建的。gitignore 文件。

`touch .gitignore && gedit .gitignore`

让我们使用 npm init 命令创建一个 package.json 文件。

`npm init --y`

我们使用 express-js 来创建 API 端点。通过点击下面的 npm 命令安装项目依赖项。

`npm install aws-sdk body-parser express serverless-http --save`

好的，等一会儿，让我快速解释一下项目依赖关系。

aws-sdk 为 aws 服务提供了一个 API，您可以使用它来构建应用程序。API 允许开发人员构建使用 AWS 服务的库或应用程序。不同的语言有不同的 aws-sdk，我们对 JavaScript 使用 aws-sdk。

**body-parser** - body-parser 提取传入请求流的整个主体部分，并在 req.body 上公开它。这意味着我们使用 post 方法发送的任何数据都存储在 req.body 中。

express 是一个轻量级的 web 应用框架，帮助你在服务器端将 web 应用组织成 MVC 架构。我们使用来创建 API 端点。

**无服务器-http** -无服务器-http 模块允许您“包装”您的 express API 以供无服务器使用。它不处理任何 HTTP 服务器、端口或套接字。

还有一步，让我们添加一个`buildspec.yml`文件到我们的项目，创建一个名为 buildspec.yml 的文件并添加下面的代码片段。稍后，我将向您解释 buildspec 文件在 CI/CD 管道中的作用。

**建筑规范. yml**

```
version: 0.2
phases:
  install:
    commands:
      - npm install
      - npm test 
```

Enter fullscreen mode Exit fullscreen mode

这是向存储库提交变更的好时机。继续下一节，创建一个可靠的 CI/CD 管道。

`git add . && git commit -m "initial commit"`

`git push origin master`

### 设置 S3 桶(简单存储服务)

亚马逊 S3 是一种对象存储，旨在从任何地方存储和检索任何数量的数据。在我们开始创建管道之前，我们必须设置 S3 存储桶来存储我们的构建工件。构建工件是从我们的构建中输出的文件，我们希望保存这些文件以供将来使用。

转到 AWS 控制台，从服务下拉列表中选择 S3 服务，单击创建存储桶并输入存储桶的名称。这里，我用的是`weight-watchers-artifacts`。所以我在 S3 桶后面添加了`-artifacts`,让我的桶更容易找到。S3 时段名称是唯一的，因此您必须输入后缀随机数。选择一个地区，然后单击下一步。将默认设置设置为 S3 桶。

[![s3 bucket creation snapshot](img/f479af0de66adbb5311a3ec0cba16c82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0uccnqRe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fh4c2a3wysknec5u7n1x.jpg)

我强烈建议，在这篇文章中，只关注一个地区。我坚持使用美国东部(N. Virginia)地区，并且检查您选择的地区是否有必要的服务。

### 配置 AWS CodeBuild 项目

AWS CodeBuild 是一个持续的集成服务，它编译源代码，运行测试，并生成可以部署的软件包。我们只需要创建带有适当设置的 CodeBuild 项目。

[![CodeBuild Snapshot First](img/69c4228d1745a51b532aa35e693cbfbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JbSujADF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8asl7zr7gavhv8mws08k.png)

找到 CodeBuild 服务从服务下拉菜单，一旦你在 CodeBuild 仪表板点击创建项目按钮。根据命名惯例，我给这个项目起了一个名字`weight-watchers-builder`。选择 GitHub 的源提供者(如果使用 CodeCommit，则选择 CodeCommit)。它会提示您进行 GitHub 认证，认证您的 GitHub 帐户。输入你的储存库完整网址，我输入`https://github.com/sagar-gavhane/weight-watchers`储存库网址。在`Source: What to build`部分，跳过默认设置。

[![CodeBuild How to Build](img/9dd26722e93748da1e33dcfa11bd0d06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JtgcSJML--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/myikmrqd59hz6t7nis1l.png)

移动到`Environment: How to build`部分。有两种选择构建环境方法，您可以使用 AWS CodeBuild 映像或特定的 docker 映像。我坚持使用 AWS 代码构建映像。我选择了一个操作系统为 Ubuntu，运行时环境为 nodejs，具体运行时版本为`aws/codebuild/nodejs:8.11.0`。

构建规范，我们将使用位于存储库根目录下的`buildspec.yml`文件。现在只需选择`Use the buildspec.yml in the source code root directory`和`buildspec name`到`buildspec.yml`。

如果你知道建筑设置，那么你可以根据自己的需要随意切换设置。

[![CodeBuild Artifacts](img/9125199eae1f54c060005993f917bc05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vK7eiGOn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/la51n3par4gws2i4opp0.png)

现在，是时候告诉我们的代码构建项目将工件保存在我们创建的 S3 桶中了。`Artifacts: Where to put the artifacts from this build project`段选择类型为`Amazon S3`并设置名称，路径为空。桶名，我会选择`weight-watchers-artifacts`并将工件打包成 zip。

其余设置:缓存、服务角色和 VPC。对于本文，我们不需要对缓存和 VPC 做任何事情。

[![CodeBuild Service Role](img/fe38cd43d1772be53bbe2458ff0a8ab3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m4ljConb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wvfo8qmajexc6g9hc0qv.png)

移至`Service role`部分，在该部分中，我们将需要一个 IAM 服务角色，当您的构建运行时，它将承担该角色。该服务角色将授予 it 人员向 CloudWatch 写日志和向 S3 写工件的权限。选择`create a service role in your account`它将为我们创建一个服务角色。我给`codebuild-weight-watchers-builder-service-role`起了个角色名。

好吧，我们不需要改变任何高级设置。点击`continue`按钮，查看我们到目前为止构建的内容。

[![CodeBuild Review](img/258c64cf741189103264384c0619db29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EEHs4ar2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i8dmfncm7ile8vu5dt8w.png)

恭喜你！！您已经成功创建了一个代码生成项目。休息一下，我知道坚持这篇文章直到这一点很难，但我们会一步一步地提高我们的知识和技能。

### 配置代码管道

AWS CodePipeline 是一项持续的交付服务，帮助我们快速可靠地交付功能和更新。您可以定义您的发布过程工作流，并描述新的代码变更如何在您的发布过程中进行。

管道由一系列阶段组成(例如，构建、测试和部署)。每个阶段都由一系列操作组成，这些操作是诸如构建代码或部署到测试环境的任务。

理论够了！！。让我们专注于创建我们自己的管道。从服务下拉列表中选择代码管道。代码管道创建分为 6 个步骤，如名称、源代码、构建、部署、服务角色、审查。

[![CodePipeline Name](img/c0f7a2beb7bf959fb53d56cec50932f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7u9SZy-y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/itzx1ylh91h5op12cy16.png)

在`Name`步骤中，输入您的管道名称，出于命名约定，我将选择管道名称为`weight-watchers-pipeline`，然后点击下一步按钮。

[![CodePipeline Source](img/8c6612372a15a3a4c3ee3cbe83a3ba99.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mcZSnyvE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uagk6itjnwum65owxjs4.png)

在`Source`步骤中，选择 source provider 作为 GitHub 存储库(如果您在 CodeCommit 上创建了存储库，那么选择 CodeCommit)。它会提示您进行 GitHub 认证。验证您的 GitHub 帐户。成功认证后，您就可以输入存储库名称和分支名称了。我将使用存储库名称`http://github.com/sagar-gavhane/weight-watchers`和分支名称作为主名称。跳过`Change detection options`一节。然后进入下一步。

[![CodePipeline Build](img/2fed0f9d63aa9b5bce1cddd445a18d08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XLOfu6E0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0hbmhktdjvz6grhann1s.png)

在`Build step`中，选择您想要使用的构建提供者，我坚持使用 CodeBuild。移动到`Configure your project`部分，选择您的项目构建者姓名。我们已经在本文开头创建了一个项目构建器，所以我将选择`weight-watchers-builder`，然后进入下一步。

[![CodePipeline Deploy](img/c74e0568d92dc08ce90bbb51e4f7b186.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pyLqc8o0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/erl56mbzwzcwvahe4wjb.png)

在`Deploy step`中，选择部署提供为`No Deployment`。稍后，我们将编辑我们的管道并添加一个部署阶段。进入下一步。

[![CodePipeline Service Role](img/e710382c24b4048de372d6e7d70b5be4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yjNDRp1---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/al9k8vibmzz8bsiw5bpu.png)

在`Service Role`步骤中，我们的管道需要一个角色来授予访问 CodeBuild 项目的权限，监视 CloudWatch 事件。现在，我们选择现成的服务角色`AWS-CodePipeline-Service`。但是，如果所选的自定义角色和角色配置不正确，AWS CodePipeline 可能无法按预期工作。

[![CodePipeline Review](img/bcd4eccaa88ed964054261eed1defd11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZnWaV2TQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sulzjvwqoajr15cwfpgz.png)

完成，您已经完成了代码管道配置，只需快速查看管道，然后点击创建管道按钮。

**恭喜你！！您的管道已成功创建。**

### 用 Jest 配置测试环境。

Jest 是一个令人愉快的 JavaScript 测试框架，由开源贡献者和脸书员工社区创建和维护。

使用 jestjs 编写一个简单的单元测试来检查给定的数是否是质数。在`__test__`目录下创建一个名为`primeNumber.test.js`的文件并复制粘贴下面的代码片段。在代码片段中，我们编写了一个函数，如果给定的数字是质数，则返回 true，否则返回 false。

在编写代码之前，将 jestjs 作为开发依赖项进行安装。

`npm install jest --save-dev`

**prime number . test . js**T2】

```
function isPrime(num) {
  for (var i = 2; i < num; i++) if (num % i === 0) return false;
  return num !== 1;
}

describe("test prime number", () => {
  it("17 is a prime number", () => {
    expect(isPrime(17)).toBe(true);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

对我们的 package.json 文件做一点修改，在脚本部分添加脚本为`"test":
"jest"`。我们必须在代码构建执行期间运行 npm 测试命令。

```
"scripts":  {  "test":  "jest"  },` 
```

Enter fullscreen mode Exit fullscreen mode

[![CodePipeline Done](img/3a6785ec4c9b91f41f36dfe61543e351.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tQ-K3UYH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eq69g08i6zmcpd099etc.png)

好吧！无论您在本地机器上对`weight-watcher`项目做了什么修改，将您的更改提交到 GitHub 并检查`weight-watchers-pipeline`管道中的发布更改。如果两个阶段都成功通过，您就创建了自己的 CI 渠道。

感谢你阅读这篇文章，我知道你可能被这个问题困扰，或者你对这篇文章有好的建议。不要忘记对你的问题或反馈表示喜欢并发表评论。

在下一篇文章中，我们将实现 CD(持续交付),所以请与我保持联系。祝你今天开心！玩的开心！！

快乐编码...