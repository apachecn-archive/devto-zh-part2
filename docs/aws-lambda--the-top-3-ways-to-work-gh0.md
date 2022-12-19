# AWS Lambda —三大工作方式

> 原文：<https://dev.to/samwsoftware/aws-lambda--the-top-3-ways-to-work-gh0>

AWS 兰博达简直不可思议！它们是托管在亚马逊网络服务上的功能，可以通过多种方式触发。

最好的部分之一是，你只需为 lambda 运行的时间付费。有没有一个小时才运行一次，只需要 2 秒钟的东西？你一天只会被收费 48 秒！与运行 24/7 ec2 或您自己的私人服务器相比，这简直是疯了。

今天我们将创建一个 lambda，然后看看处理代码的三种最佳方式。

### 创建 Lambda

一旦你建立了你的 AWS 账户，有几种方法可以创建一个新的 lambda。对于第一个 Lambda，我们将使用 AWS 控制台。

#### AWS 控制台

创建第一个 lambda 的最简单方法是在 AWS 控制台中创建它。你可以在*服务*下找到 *Lambda* ，带你去 Lambda 控制台。

[![](../Images/2c38920d8cec1f33fa2207c206c0bc53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jL01wvZl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A23UBDu9eiNn9CasvX9dUqg.png)

如果这是你第一次使用 Lambda，你会看到这个。点击*创建功能*按钮，开始设置您第一个功能。

您将进入设置页面，在这里您可以配置一些功能。你可以从蓝图或无服务器应用程序回购中创建一个 Lambda，但为此我们将从头开始创作。

[![](../Images/e1d1cce22a948290d73fd4fb0991f16e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rLONInQe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjmKCKMsIPuMLhgsMxNMBog.png)

输入您的函数的名称(这对于您的用户或子帐户必须是唯一的)，选择您的运行时(我们将使用 Node.js 8.10)，并选择一个角色。

如果您还没有一个角色，您必须创建一个新角色。从模板创建一个，您可以将*策略模板*留空。

### 编写您的 Lambda 代码

Lambdas 的一大优势是你可以选择如何编写和编辑它们。主要有三种方法:在 Lambda 控制台中，在 Cloud9 中，以及在您的本地机器上。我将涵盖这三个方面，并讨论它们的优缺点。

### Lambda 控制台

这是您创建函数时进入的屏幕。你会发现有很多事情正在进行。我们现在关心的是*功能代码*部分，大约在中间位置

[![](../Images/4436fd8247b00264dce76235faa8ca20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Am2SjpR3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVxeSaa8uQgP92Wl4zCPMUg.png)

在这里我们有一个基本的编辑器。我相信它是基于 Cloud 9 IDE 的，对于简单的 Lambdas 来说非常好用。你可以在下面看到这个处理程序是一个异步函数，因为我选择了使用 Node 8.10。如果你喜欢回调，那么 Node 6.10 就是你的运行时。

[![](../Images/531bb115d80dbf2f879efcf9fa3a8f05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pSR2ONCq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/693/1%2AUkr1dY8xL4mT2IIEw0DVZg.png)

#### **优势**

*   这是一个体面的编辑。
*   您可以通过 AWS 控制台从任何计算机访问它。

#### **缺点**

*   好像不是 100%稳定。有时它不允许你保存，所以你必须把你所有的工作复制到本地文件，重新加载页面，然后把你的工作复制回来。我希望这个问题很快得到解决！
*   它没有终端。这意味着您可以单独使用这种方法使用 NPM 安装软件包。

### 云 9 编辑器

亚马逊最近收购了在线开发平台 Cloud 9。它似乎运行一个非常基础的 Ubuntu 版本，该版本与 AWS 平台的其余部分集成在一起。

在控制台中搜索 *Cloud 9* ，进入页面选择*创建环境。*从这里开始，您为您的环境命名并进入下一步。

在这里，您可以选择想要在什么样的环境上运行。很棒的一点是 t2.micro 是免费层，所以如果你在免费层，你可以免费使用这个方法。我从来不需要比 t2.micro 更强大的东西。

[![](../Images/5b5ae8082dc684610434116a670f9bf5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_7xQYYPj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/919/1%2AmIMSy6hKCQuer20ZOTjVSQ.png)

从这里继续，您将进入新的云 9 环境！

[![](../Images/3e56397f534f82bbc0745388d526c16d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZOoLqJth--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuaTpBEey0EHYd-_aWa165g.png)

最酷的是，你可以从你的环境内部访问所有的 Lambdas。点击 *AWS 资源*然后在*远程功能*下你会找到你所有的功能。单击您想要编辑的 Lambda，然后点击上面的下载按钮，将它导入到您的环境中。

一旦完成，就像你在本地工作一样。

[![](../Images/19ed076b6584652c77df9dd7f537799e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EdLzHBl4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/733/1%2AP2Y6g3Juw5T7lltbooxhbg.png)

完成后，只需从本地列表中选择您一直在使用的功能，然后点击上传按钮。几秒钟之内，您的所有更改都将生效。

#### **优势**

*   同样，这都是远程的，所以如果你在多台机器上工作，你不需要担心忘记提交你的工作或者把它保存到记忆棒上。
*   获取您的功能并上传它们非常简单。这是这种方法迄今为止最好的一点。
*   现在，您有了一个集成的终端，允许您安装 npm 软件包，并使用该终端做任何您想做的事情。

#### **缺点**

*   它仍然有 Lambda 编辑器的稳定性问题。我有过多次尝试保存却无法保存的情况，不得不拷贝到本地，刷新并重新拷贝到 Cloud 9。几次之后，我放弃了，转到了本地剪辑。

### 本地编辑

我要做的有点不同，我会列出优点和缺点，然后告诉你如何做得更好。

[![](../Images/80c449ac4a354791f97385e02f8af859.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--au-T1huz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXMS7swq0ptF24er7L0fgmg.png)

#### **优势**

*   本地编辑是大多数开发者的工作方式，我们可以使用我们最喜欢的 IDE，扩展和配色方案。
*   它很稳定(只要你的电脑稳定)。

#### **缺点**

*   没有花哨的按钮来获取和上传您的工作到 AWS。
*   您的工作是本地的，因此多用户或仅在多台设备上工作会更加复杂。

#### 本地编辑技巧

因为这种方法的优点是如此吸引人(或者其他方法的缺点是如此骇人听闻),我们将利用一些基本的变通方法。应该需要大约 15 分钟来设置我们需要的一切！

#### AWS CLI

要将我们的工作上传到 AWS，我们可以使用 AWS CLI。这允许我们上传一个 zip 文件到我们的 AWS 帐户，它填充了一个给定的 Lambda。

为此，我们首先需要设置 AWS CLI。您可以使用本教程中的来安装它，或者在您的终端中键入 npm install -g aws-cli。现在，我们需要为我们的 CLI 设置一个用户以登录。

在 IAM 管理中，点击*添加用户，*给用户一个名字，选择*程序化访问。*这将允许我们远程充当用户。

[![](../Images/3043818fed8d02229159283522d95b4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vqYkLqgx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALkB76XZZwPt6soPHPtWlCQ.png)

在权限屏幕中，选择*直接附加现有策略*，并选择*管理员访问。*这将允许您通过 CLI 做任何您想做的事情。如果您愿意，您可以对该用户设置更严格的策略，或者这是供其他人访问的。

在你显示你访问键之前还有另一个屏幕。复制您的访问密钥并打开一个终端。运行命令 aws configure，它将要求您做 4 件事。

```
AWS Access Key ID [None]: "Your Access Key ID" AWS Secret Access Key [None]: "Your Secret Access Key" Default region name [eu-west-1]: Default output format [json]: 
```

前两个可以在用户创建的最后一页找到，接下来的两个可以保留为缺省值或者更改为您想要的值。

#### 使用 AWS CLI

现在我们已经设置了 CLI，我们可以使用它来使我们的生活变得更加轻松。如果你有一个带有 lambda 函数的文件夹，那么我们可以运行一些简单的命令来上传它。

```
cd MyLambdaFunction rm index.zip zip –X –r ./index.zip \* aws lambda update-function-code --function-name MyLambdaFunction --zip-file fileb://index.zip cd .. 
```

#### AWS CLI 构建脚本

这很好，但是每次你想上传一个新的 lambda 版本的时候都把这些输入出来会很烦人。所以我们将使用一个构建脚本。

为了让这个脚本正常工作，您需要一个这样的文件夹结构。每个 lambda 都有一个包含相关文件的文件夹和一个 region.txt 文件。

[![](../Images/c499c03a3264b92fb4e77e944d23c19e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--899NCQ1f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/270/1%2AXEed7aP1zbg6CyB3B8maWA.png)

这个脚本不仅运行基本的 AWS CLI 命令，而且还执行额外的检查，运行 npm 安装并显示进度的详细信息。