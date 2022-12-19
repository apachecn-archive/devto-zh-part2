# Angular 6、GitLab CI/CD 和 Google App Engine 入门

> 原文：<https://dev.to/runtime-revolution/getting-started-with-angular-6-gitlab-cicd-and-google-app-engine--2okk>

本文将提供一个初始设置，以便更好地理解和配置持续集成和交付架构(CI/CD)。在一个每个工具都相互重叠的世界里，我们想要简单、可靠、易于维护的东西。这就是选择 GitLab 的原因，因为它提供了一套工具，否则就必须从外部添加。

本指南假设标题中提到的工具都没有安装或配置。

## 棱角分明 6

让我们从全局安装 Angular CLI 开始。如果没有安装 NodeJS 和 npm，请检查[https://nodejs.org/en/download/](https://nodejs.org/en/download/)。

```
$ npm install -g @angular/cli 
```

创建新的角度应用程序

```
$ ng new demo-app
$ cd demo-app/ 
```

Angular 将为您的应用设置所需的一切，包括初始测试设置。

运行以下命令，以运行单元测试。

```
$ ng test 
```

下面是端到端测试。

```
$ ng e2e 
```

运行服务器。

```
$ ng serve --open 
```

当使用 Angular CLI 创建应用程序时，还会创建一个 git 存储库。

让我们转到 GitLab 配置。

## GitLab

在[https://about.gitlab.com/](https://about.gitlab.com/)创建账户，然后在 ***新建项目*** 。

[![](../Images/43b91bbd2a3221dd9ce56af054e98a91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E1sNEhTx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2APhZ-aMYorUXQN0I68UHtew.png)

一旦建立了空的存储库，就会出现一个列表，其中包含几个关于本地 git 配置的选项。

不要忘记设置您的 **ssh 密钥**，查看以下链接了解更多信息【https://docs.gitlab.com/ee/ssh/README.html T2】和[https://docs . git lab . com/ee/git lab-basics/create-your-ssh-keys . html](https://docs.gitlab.com/ee/gitlab-basics/create-your-ssh-keys.html)。

让我们将之前创建的 GitLab 存储库配置为我们的 **Angular 应用原点**。

```
$ git remote add origin git@gitlab.com:<your_account>/demo-app.git 
```

推它。

```
$ git push --set-upstream origin master 
```

## 谷歌应用引擎

这是一个无服务器平台，允许您构建高度可伸缩的应用程序。在[https://cloud.google.com/appengine/](https://cloud.google.com/appengine/)创建一个账户。

与其他服务类似，谷歌云在创建账户时会要求提供信用卡，而且，与其他服务一样，他们会提供一定数额的信用(300 美元)供你探索该平台。试用期持续一年，或直到所提供的信用额度用完。

创建新项目。以下是我们项目的定义。

[![](../Images/d77e900c715756c38fef7c28f60ceae0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nZ-r4A8J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A8p7AaBdotPk2jOwbyH8abg.png)

一旦项目准备就绪，将会有一组类似于下面的选项可用。

[![](../Images/e0e972c1cadb1fde1b133b2d8a8a92f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I0l25pbA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3360/1%2Aa_Dx5QbzDuuN7qv1k-7LBQ.png)

现在我们要创建一个应用程序。点击* *创建应用程序，* *为您的应用程序选择一个地区。

[![](../Images/dda3a77743f106a99287cbbe08b3fd7d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d6VV1TVB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AQEKJ3y3cck6MTZrFPVFeeA.png)

定义**语言**和**环境**。**语言**将是 **Node.js** 而**环境**将是**标准**。要了解关于这个主题的更多信息，请查看[https://cloud . Google . com/app engine/docs/the-app engine-environments](https://cloud.google.com/appengine/docs/the-appengine-environments)。

[![](../Images/b0329e48d1c4679044e2c40eaedac7a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_IBtpqaZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A3WXOW1cyhZFcXXKVEzXbfA.png)

瞧啊！我们已经创建了我们的应用程序！

在继续之前，我们希望确保部署工作正常，因此我们将通过命令行使用两种不同的配置来部署我们的 **Angular 应用程序**。

首先，我们需要构建我们的应用程序。

```
$ ng build --prod --aot 
```

我们还需要创建一个 **app.yaml.** 这是 Google App Engine 的部署配置。

要了解关于可用配置选项的更多信息，请查看[https://cloud . Google . com/app engine/docs/standard/python/config/appref](https://cloud.google.com/appengine/docs/standard/python/config/appref)

第二步，就是安装 **Google** **云 SDK。**遵循 https://cloud.google.com/sdk/[的步骤](https://cloud.google.com/sdk/)，以便我们能够**通过命令行**部署我们的应用程序。

### 使用用户凭证部署 gcloud

运行以下命令初始化并设置新配置。选择一个与您的用户相关联的。

```
$ gcloud init 
```

设置完成后，运行下面的命令进行部署，并按照说明进行操作。

```
$ gcloud app deploy 
```

### 使用服务帐户部署 gcloud

导航到**谷歌应用引擎控制台**，转到 **IAM & admin。* *在这里，我们可以找到用户/成员及其各自角色的列表。因为我们想通过 GitLab 的 CD 部署我们的 Angular 应用程序，所以我们需要添加一个* *服务帐户**。

*   转到服务帐户菜单。

[![](../Images/71d9b0d71257a16e5382804021371485.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bCf5gzTC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AnZvsM-UmM1WadjHA7MxmgA.png)

*   为 GitLab 添加服务帐户。

[![](../Images/8535f106a0502054dfe828426a806aff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cQGlQRRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AeUdqVnYSciwcHdngF14TfA.png)

*   为该帐户创建一个密钥并保存 json。

[![](../Images/284a54c6c29f80db4b811f2959dc718c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PCUaew4a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Al63hV4mDEMnmKEIzzxhI7A.png)

[![](../Images/6a3c1e72e683d3b9d33c475374d3e208.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PK0io_Jk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AaahbblNk8LW8eIXci-8bMA.png)

*   导航到列出了成员及其角色的 IAM，并将以下角色添加到我们刚刚创建的服务帐户中:**应用引擎** **管理员**、**云构建编辑器**和**存储管理员**。为了更好地理解角色，请查看 https://cloud.google.com/iam/docs/understanding-roles[。](https://cloud.google.com/iam/docs/understanding-roles)

[![](../Images/5de3249f1d5d72f07a06494afaa918ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5Efi4ZbQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3664/1%2ATNrbHjtWnwRVAuXTOJCKcA.png)

既然我们已经设置了 Google App Engine，我们可以使用**服务帐户**部署我们的 Angular 应用程序。运行以下命令，第一个是使用服务帐户进行身份验证，第二个是部署 Angular 应用程序。选项“—项目”是**项目 ID** 。

```
$ gcloud auth activate-service-account --key-file <service-acc-key.json>
$ gcloud app deploy --project=elite-academy-217722 
```

## 有角 6 + GitLab CI

在激活 GitLab 的 CI 之前，让我们改变测试运行的方式。如果你还记得，当你跑的时候

```
$ ng test 
```

或者

```
$ ng e2e 
```

打开浏览器并执行测试。但是，GitLab 的 CI 不支持这一点。为了克服这一点，我们将使用一个无头浏览器。

### 通过木偶师用无头浏览器测试

安装木偶师，它也会安装无头铬。

```
$ npm install puppeteer --save 
```

现在我们需要更新负责 **ng 测试**的 **karma.conf.js** 。

还有**量角器. conf.js** 负责 **ng e2e** 。

要自动激活 GitLab 的 CI，我们需要添加一个包含要运行的作业的文件。在这个特殊的例子中，我们将运行两个任务，每个测试命令一个。

### CI 岗位

创建一个名为**的文件。git lab-ci . yml * *在 demo-app 的根目录下，内容如下。

我们来看一下上面的配置:

*   **image** :这定义了 GitLab 的运行程序将要运行的 docker 图像。我们的基础是 NodeJS，所以这定义了我们将使用最新的 NodeJS 映像。

*   * *缓存:* *每当构建一个版本时，都会创建一个缓存。在这种情况下，我们缓存 node_modules。

*   * * before _ script:* *该选项定义了在执行作业之前必须运行的操作。第一行定义了 Chromium 需要的所有附加包，第二行安装了所有保存的 NodeJS 包。

*   **阶段:* *我们在这里定义工作组。一个组中的所有作业并行运行。* *阶段测试**首先运行，如果成功，**阶段构建**随后运行。

*   **作业 1:** 是负责运行单元测试的作业。它运行的阶段以及将要执行测试的脚本都已定义。必须设置项目的相对路径，否则将找不到可执行文件。

*   **作业 2:** 是负责端到端测试的作业。配置与之前的作业非常相似。

*   除了已经提到的选项，发布还包括一个工件和一个需求标签。* *工件是角度构建的结果，在这种情况下，它被设置用于生产，并通过提前选项进行编译以获得更好的性能。需求标签为 **only** ，设置为**标签**。这种配置意味着 release_job 仅在创建发布时可用。

将所有之前的更新推送到资源库，并检查 **CI/CD** 下 GitLab 的**管道**。

[![](../Images/a1b7af0a728a5b54cfd7e59587a84630.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b4SDMPjk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4076/1%2AYNWsZjoPCgoiy31CNPP8Eg.png)

点击左侧显示**通过**的图标，访问作业。

[![](../Images/81e19a3ef50a66b37877d21f10271808.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KQiR07Mi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Aq0oCWj45gQFTYu1eQ_XsrA.png)

### 发布

让我们创建一个发布来检查我们的发布工作。在 GitLab 中，在**库**下的侧菜单中，你会找到**标签**。

[![](../Images/8b9d8444bbd21726f560e58871630e83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PwoJh2qX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4060/1%2A0p0JJtz6noRv_qZGvF8mUA.png)

我们的第一个版本被创建

[![](../Images/7d99ba95dafdf8a13e4cb58deca49210.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TXhsxsRw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A574LOcpsYgFLQOCycMgk6g.png)

让我们单击与发布相关联的 git 引用中的链接，然后单击 pipeline 选项。我们的管道现在有一个额外的步骤，**构建**。

[![](../Images/75dc5900167c473d561a788c0246ee07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2XsonISq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AM3sV96VRqaHCc1HwsznS0A.png)

在标签下，你可以选择下载你的新版本。您可以通过选择**下载‘发布 _ 作业’**来完成。

[![](../Images/471848e837cf412271a1124804f4c06a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HMXzubFi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4052/1%2Ar7i31s3m3jdILOkCnexPcA.png)

## Angular 6+git lab+Google App 引擎

现在一切都设置好了，我们将配置从**git lab * *到** Google App Engine**的**手动**部署。它可以是自动的，但是人们会犯错误，所以我们将它设置为手动模式。

为此，我们需要做两件事:配置环境变量和更新。gitlab-ci.yml 运行我们的部署命令。

### 在 GitLab 中设置环境变量

在 GitLab 项目的设置菜单下，我们会找到**变量**菜单，点击展开。

[![](../Images/dd3add2ad28b5e23deac61d8e4443f77.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LLUve7Rb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3132/1%2AmJUnzWerg6vNahS-ZRcXuw.png)

创建两个变量:

*   **GAE _ 密钥 _ 文件**:复制与 **Google App Engine 服务账号**关联的 json 文件内容，粘贴到值字段。

*   **GAE _ 项目 _ID** :项目 ID，本例中其值为**精英-学院-217722**

保存变量。

### 。gitlab ci . yml 更新

主要的增加是部署阶段，但是引入了一些关于作业名称和脚本执行的其他更改。

将 **before_script** 移动到每个作业内部，这意味着它将为**作业:测试**和**作业:e2e** 运行，但不会为**部署:生产**运行。

让我们来看一下工作**部署:生产**:

*   **阶段**:与之关联的阶段，本例中为**部署**

*   环境:这允许我们在必要时回滚。当部署成功时，它会出现在 **GitLab 的操作>环境**下。

[![](../Images/51e572f3f2a2a85e7c884a4123b51fe4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xkWhDNqo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3860/1%2AOfi99QMOcfbGhDPgKgB3ig.png)

*   **before_script** :只有部署所需的脚本。

*   **依赖关系**:这将取决于作业**发布:构建**，意味着在依赖关系下创建的**工件**将在该作业中可用。也意味着构建只运行一次。

*   **脚本**:用于部署的脚本。如你所见，它们与我们之前用**服务账户**测试的相同。服务帐户密钥文件和项目 ID 都在 GitLab 的环境变量中设置。

*   **当**时:设置为**手动**，这样展开不会自动发生。

*   **only** :与构建类似，该任务仅在创建发布/标签时可用。

下一次我们**创建发布/标签**时，我们的**管道**中将会有新的东西。

[![](../Images/edb95febc817bb9b0c7ea3ec0f409941.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C_bBS-d3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2448/1%2AO84mXoZxo3JtZWwnkzmTAA.png)

按下 ***播放*** ， **Angular** 应用将通过 **GitLab 的光盘部署到 **Google App Engine** 。**

就是这样！希望本指南对您有所帮助。尽情分享你的想法、问题或建议，不要忘记总有改进的空间！

把它拿走！

*在[运行时革命](http://www.runtime-revolution.com/)中，我们认真对待我们的工艺，总是付出额外的努力来交付一个可靠的、可维护的和可测试的产品。你有要推进的项目或想要推出的产品吗？我们很乐意帮助你！*