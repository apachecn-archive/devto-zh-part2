# 如何创建 GitHub 资源库？

> 原文：<https://dev.to/nisevi/how-to-create-a-github-repository-3o1j>

GitHub 入门的简单指南。

[![](img/fb251cc6e69b1d263922050107b9d464.png)T2】](https://cdn-images-1.medium.com/max/1024/1*I_VX8P2sOhU83ijUtbRY5g.jpeg)

### 上下文

这篇博客将带你经历你需要的不同步骤，以便设置你的 GitHub 帐户，并准备好创造令人惊奇的东西:)！我们将设置 SSH 密钥以及用于签署提交的 GPG 密钥。我将使用的操作系统是 Linux，发行版是 Ubuntu 16.04.3 LTS 版。如果你使用的是 Windows 或者 MAC，你可以遵循这篇文章的指导，但是对于你的操作系统来说，相应的命令会稍有不同。你可以发表评论，这样我可以帮助你。我们将[安装 GIT](https://git-scm.com) 并推送我们的第一个签名提交。

### 创建 GitHub 账户

首先，你只需要去[这里](https://github.com):

[![](img/397a3c293fc5c562bbc7e8ba91dba2bc.png)T2】](https://cdn-images-1.medium.com/max/930/1*LLAC5eTXNfEAg8Qgkwnjdg.png)

用要求的信息完成表格，你将立即创建你的帐户。它看起来会像这样:

[![](img/d30b665246a5eef42be2cb9e087c2124.png)T2】](https://cdn-images-1.medium.com/max/905/1*jCDIMrrMIFtDj47gRoZObA.png)

### 创建您的第一个存储库

请注意，您的个人资料视图中有不同的选项卡:`Overview`、`Repositories`、`Stars`、`Followers`和`Following`。我们的兴趣之一是`Repositories`，点击那里:D！

将出现一个视图，其中有新按钮，单击它:

[![](img/59d39db6d56430d99aefc1e12427fd28.png)T2】](https://cdn-images-1.medium.com/max/686/1*mdlbWciucIkLoTMOTIXz5w.png)

之后，您将看到以下表格:

[![](img/c42b842df00c3f9e45519652622ec8f6.png)T2】](https://cdn-images-1.medium.com/max/687/1*dOI3QnLE1Q-ieEqt5-mvQw.png)

注意，我完成了`Repository name`，我添加了一点`Description`，并点击了`Initialize this repository with a README`。这最后一步很重要，因为我们将创建一个包含文件的存储库，这与我们创建一个空存储库是不同的(我们将在后面介绍空存储库)。

关于存储库的名称，我总是只使用`downcase`或者与`_`结合使用。

一旦通过点击按钮`Create repository`创建了存储库，您将会看到如下内容:

[![](img/bdaac800262e6622e5f22b152ceef036.png)T2】](https://cdn-images-1.medium.com/max/911/1*bk2z061_F003nViAlanyEA.png)

这里需要注意一些事情，因为我们将配置 SSH 密钥和 GPG 密钥，所以我们将使用带 SSH 的克隆选项来克隆存储库。

### GIT 安装

现在，如果我们想在本地(意思是用我们的计算机)使用我们刚刚创建的存储库，我们将需要安装 GIT。

打开控制台`Ctrl+T`或进入您的程序并点击`Terminal`图标:

[![](img/ccb46d6c859b740ce808682e2176ca03.png)T2】](https://cdn-images-1.medium.com/max/566/1*6UqeDEt-szNQkXXm9YUU1A.png)

使用打开的终端后，编写以下命令:

`sudo apt-get update`

这将更新您的本地包索引。

[![](img/4e9e7eb0af819c5868edc856a2bd26d0.png)T2】](https://cdn-images-1.medium.com/max/619/1*2LqpZ8woEjd126WAw23gQg.png)

现在，我们将继续[安装 GIT](https://git-scm.com) ,使用:

`sudo apt-get install git`

[![](img/200a46b8b036e6ee876be5e233feea5e.png)T2】](https://cdn-images-1.medium.com/max/976/1*d7PQw5c1emPxF0OkvamBog.png)

我们还需要告诉 GIT 我们是谁，为此，我们将使用以下命令设置我们的`name`和`email`:

`git config --global user.name "Nicolas Sebastian Vidal"`

`git config --global user.email "nicolas.s.vidal@gmail.com"`

[![](img/5ecf4d7d70a29b37143bcc33866044fa.png)T2】](https://cdn-images-1.medium.com/max/880/1*ds2WQFBY1YmnZqt_GzLDhw.png)

当然，我把我的名字放在哪里，它就会放在你的位置，电子邮件也是如此:)。

### 配置您的 SSH 密钥

在克隆我们的存储库之前，为了能够提交和推送我们对 GitHub 存储库的更改，首先，我们需要生成我们的 SSH 密钥。

*   打开控制台`Ctrl+T`或进入程序，点击`Terminal`图标；
*   粘贴以下文本，替换为您的 GitHub 电子邮件地址。这将使用提供的电子邮件作为标签创建一个新的 ssh 密钥。`Generating a public/private rsa key pair.`
*   当提示您“输入保存密钥的文件”时，按`Enter`。这接受默认的文件位置。`Enter a file in which to save the key (/home/you/.ssh/id\_rsa):[Press enter]`
*   在提示符下，键入安全密码。我建议您输入一个密码，不要留空，这样会更安全。`Enter passphrase (empty for no passphrase): [Type a passphrase]` `Enter same passphrase again: [Type passphrase again]`

[![](img/f395069c37894eeec2584f7f3df07151.png)T2】](https://cdn-images-1.medium.com/max/695/1*dvb6brlY9y_V3ig-nlH5cg.png)

*   在后台启动 ssh-agent。
*   将您的 ssh 私有密钥添加到 SSH 代理中。如果您用不同的名称创建了您的密钥，或者如果您正在添加一个具有不同名称的现有密钥，请用您的私钥文件的名称替换命令中的 *id_rsa* 。

[![](img/03fb806f39008310b9acccd42656ed45.png)T2】](https://cdn-images-1.medium.com/max/613/1*aUYwWGNkEWO46D0uGPA1tw.png)

这最后两个步骤是为了在每次您想要将您的更改推送到您的存储库时不输入密码短语[。](https://stackoverflow.com/questions/10032461/git-keeps-asking-me-for-my-ssh-key-passphrase)

这些提到的步骤也可以在这里找到[。](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

#### 将 SSH 密钥添加到您的 GitHub 帐户

在终端中，编写以下命令:

`cat ~/.ssh/id\_rsa`

这个命令将向您显示您的 SSH 公钥:

[![](img/691478eccff1557b53a019ab720d05f4.png)T2】](https://cdn-images-1.medium.com/max/1024/1*EuHWXhvBHmrdaILJxtpF6A.png)

将此密钥从`ssh-rsa`复制到您的电子邮件`nicolas.s.vidal@gmail.com`中。

现在我们需要进入我们的 GitHub 个人资料，点击`Settings`:

[![](img/d640b334994bdbd478663eca14853180.png)T2】](https://cdn-images-1.medium.com/max/953/1*nlE2crDHwdIRcdxZ9tgWmA.png)

一旦你在那里点击选项说`SSH and GPG keys`，点击`New SSH key`之后，它将出现一个表格，我们将粘贴我们的`SSH key`，还添加一个标题，以确定机器将使用提到的关键:

[![](img/eed13b8444d94db833126139c06fbe1e.png)T2】](https://cdn-images-1.medium.com/max/908/1*g05Qq5ly67Hfna27f2OopA.png)

按下`Add SSH key`键，最后我们的键将被配置使用:

[![](img/8e4f7f8841a1b97c0b493e55f083e04a.png)T2】](https://cdn-images-1.medium.com/max/649/1*eVly3rsNk7zmDbtzCAIWkg.png)

### 配置您的 GPG 键

如果您想要签署所有的提交，请遵循以下步骤。否则，你完全可以避免这一点。

回到您的终端，键入以下命令:

`gpg --gen-key`

将出现一些选项，这里是每个选项的详细信息和我使用的常用配置:

*   `Please select what kind of key you want`我用默认选项`RSA and RSA`；
*   `What keysize do you want?(20148)`在这里，你会看到我选择了“2048 ”,但是，我只选择了默认选项，因为我使用的是虚拟机，生成获取所有所需字节所需的熵有点麻烦。我总是选择 4096，因为这是推荐的密钥大小。
*   `Key is valid for?(0)`对于这个例子，我选择了密钥不应过期，但我总是每个月更改 GPG 密钥:D！
*   下一步将是设置你的`Real name`、`Email address`和`Comment`，最后一个我用它来放我的用户名:P！
*   最后一步，你将不得不产生足够的熵来得到需要的字节，所以为此，你将不得不开始在你的计算机上做一些工作。你可以开始疯狂地移动你的鼠标(这对我很有效)，也可以在文本编辑器上输入许多随机字符。一件事，要快！。

[![](img/be747e50b91dffdc570fa0244666b217.png)T2】](https://cdn-images-1.medium.com/max/496/1*aHsr4B0cOJKy8-tT8845kw.png)

一旦我们生成了我们的 GPG 密钥，我们仍然需要将它添加到我们的 GitHub 帐户，需要遵循以下步骤:

*   使用`gpg --list-secret-keys --keyid-format LONG`命令列出您既有公钥又有私钥的 GPG 密钥。签名提交或标记需要私钥:

[![](img/8e1fcf4e8b5872557b9edb70d4b81f34.png)T2】](https://cdn-images-1.medium.com/max/806/1*7NHBEU7WaVSK3uYSHP6C_A.png)

*   从 GPG 键列表中，复制您想要使用的 GPG 键 ID。在这个例子中，GPG 密钥 ID 是 77384044A63D6CFF
*   在您的机器上配置 Git，使用这个 GPG 密钥来签署您的提交:`git config user.signingkey 77384044A63D6CFF`；
*   粘贴下面的文本，替换您想要使用的 GPG 密钥 ID。在这个例子中，GPG 密钥 ID 是`77384044A63D6CFF` : `gpg --armor --export 77384044A63D6CFF`

[![](img/837188ef66a62d166b6a4138504b44a5.png)T2】](https://cdn-images-1.medium.com/max/690/1*IIerOEvTL-gkIfRrEGfUJA.png)

*   复制你的 GPG 键，以`-----BEGIN PGP PUBLIC KEY BLOCK-----`开头，以`-----END PGP PUBLIC KEY BLOCK-----`结尾；

#### 将 GPG 密钥添加到您的 GitHub 帐户

再次像我们使用`SSH`键一样，您需要进入您的 GitHub 配置文件并点击`Settings`选项。

一旦你到达那里，点击显示`SSH and GPG keys`的选项，然后点击`New GPG key`，将出现一个表单，我们将在其中粘贴它:

[![](img/43e66a6bb77698cc326fb2cb8a44c52d.png)T2】](https://cdn-images-1.medium.com/max/925/1*T9HjK9g610CkrkwFaJMcgw.png)

按下`Add GPG key`键，最后我们的键将被配置使用:

[![](img/57ccc3ca4c6b25df95e7be64a534b898.png)T2】](https://cdn-images-1.medium.com/max/650/1*2CXhH7vivn8U6d-PYfB5cg.png)

我提到的生成 GPG 密钥的步骤也可以在这里找到。

### 克隆我们的知识库

回到我们的终端，我们将前往`Documents`，在那里我们将克隆我们的 GitHub 库:

[![](img/b6cea210efd871b10cdbf9c8b326777e.png)T2】](https://cdn-images-1.medium.com/max/917/1*HAx0uyCxjB7i6uJtH9QWdQ.png)

我们将通过使用 SSH 来克隆我们的存储库，因此我们的命令行如下所示:

`git clone git@github.com:nisevi/staticwebsite.git`

[![](img/cff053161d211815d4aceb101bfc8d5e.png)T2】](https://cdn-images-1.medium.com/max/807/1*JbPG84fmsM8_O1DpmPHUPg.png)

它会问我们是否要继续，因为主机 github.com 的真实性无法确定，但是！因为我们信任 GitHub，所以我们会说`yes`。

之后，您将在`Documents`文件夹中克隆存储库，并且您将找到我们选择用于初始化存储库的`README.md`文件:

[![](img/b57c1a7a8e97c041426071300bd2c3ed.png)T2】](https://cdn-images-1.medium.com/max/498/1*enGtVdDUobQfmrVfCWvWpw.png)

### 第一次提交

为了创建一个提交，首先我们需要对我们存储库中的文件进行一些更改，否则，我们将没有任何东西可以提交。

为了检查文件的状态并查看其中是否有任何更改，我们将使用以下命令:

`git status`

[![](img/fba5f096d9d23e1dbbf4a8bf64bf37dd.png)T2】](https://cdn-images-1.medium.com/max/449/1*n9e75Hko8MrPWjzLH5Y4kA.png)

这是`README.md`文件的实际内容:

[![](img/3e78a54457553111802a74663a4b746f.png)T2】](https://cdn-images-1.medium.com/max/475/1*05xZhy2t8TOyEV-MhrukZQ.png)

修改之后，我们可以看到，通过再次键入以下命令，我们的文件发生了更改:

`git status`

[![](img/26708baa5cd6bb76aa081f75f15d2bbd.png)T2】](https://cdn-images-1.medium.com/max/685/1*L44WlVIx_FFyLCfQKZqZVA.png)

我们可以看看我们使用以下命令所做的更改:

`git diff`

[![](img/ad1160efa1585cca3620fc305efb07d6.png)T2】](https://cdn-images-1.medium.com/max/431/1*NmnEtFI72q8M24pdKt3bHw.png)

我们可以看到`Static website`。已经删除，加了空格，写了`Hello World.`。

现在，我们将通过执行以下操作将该文件添加到提交中:

`git add README.md`

运行该命令后，我们将能够看到我们的更改已经添加到我们想要创建的提交中。我们可以通过执行以下操作来检查这一点:

`git status`

[![](img/c68448cce2598cb1afa3f78a5d79326f.png)T2】](https://cdn-images-1.medium.com/max/450/1*FvyzTNl8HIMVE_Vag1c0PA.png)

我们的下一步是创建一个带签名的提交，其中包含关于我们对文件所做的更改的适当消息。请注意，这里会要求您键入创建`GPG key`时使用的密码短语(如果您跳过了这一部分，则不需要使用`-S`标志):

`git commit -S -m "Update README.md"`

*   标志用于签署我们的承诺；
*   `-m` flag 是我们要写的消息；

一旦提交被创建，我们将继续推动我们的变化:D！！

键入以下命令:

`git push origin master`

[![](img/8a6f33e4a91c3a0efdb40e88b95bfefa.png)T2】](https://cdn-images-1.medium.com/max/555/1*O5yKS-IoB9z7NXPfZMcfwg.png)

搞定了。！！我们已经推动了我们的变革。如果你想看，回到你的 GitHub 库。您将看到`README.md`文件已经更改，如果您想知道您的提交是否已经被签名，请单击显示`2 commits`的位置:

[![](img/4393adc6f87211c33ca145dc44a3ce7b.png)T2】](https://cdn-images-1.medium.com/max/1005/1*8N1EWKRbgA-arRCtEWypgg.png)

在下面的视图中，您会发现我们刚刚使用添加的注释进行了提交；写着“已验证”的绿色标签意味着我们的`commit`已被:D 签下！！！

[![](img/4dfe9162f633599590503a45c0f47b68.png)T2】](https://cdn-images-1.medium.com/max/1017/1*b-hiw42dSgKnO7G2v7m9GQ.png)

这里需要注意的是，我已经有了一个写着`Initial commit`的签名提交。这个提交是在我们决定用一个为我签名的`README;`或`Verified`来初始化我们的库时创建的，因为我已经有其他的`GPG key`和`SSH key`在我的笔记本电脑上工作。重要的是你刚才推的提交:)！

本帖到此结束。

接下来你会发现一些我在本教程中所做的事情的替代品。我希望你喜欢它！！

### 创建一个空存储库

如果您决定创建一个空的存储库，您将会看到不同的选项。在这里，我们将介绍克隆这个库并在您的计算机上运行的步骤。

[![](img/f0f3323c01fecc806c97a62e0e87affd.png)T2】](https://cdn-images-1.medium.com/max/1018/1*23glSrCbPISp-YyhOlFq3w.png)

注意，我没有选择选项`Initialize this repository with a README`。点击`Create repository`后，您将看到以下视图:

[![](img/b585121e3f35b28b832efd7d4d0b3e43.png)T2】](https://cdn-images-1.medium.com/max/1017/1*fQqKJYZXFvQFH51tOYR1mw.png)

请注意，我选择了`SSH`,因为我们将通过执行以下操作来克隆创建的存储库:

`git clone git@github.com:nisevi/staticwebsite.git`

[![](img/b650ee0dadebfeabd0e1fd9cd8b18830.png)T2】](https://cdn-images-1.medium.com/max/673/1*HsKEGz4aWUgg7DRoek-zQA.png)

正如我们所看到的，存储库已经被克隆，但是文件夹中没有任何东西(除了存储库的`.git`配置文件夹)。此时，您可以创建任何文件，并遵循我们在`Pushing your first commit`中描述的步骤。

### 使用 HTTPS 克隆存储库

另一种选择是用 HTTPS 克隆存储库，而不是做所有的 SSH 和 GPG 配置来推送提交:

[![](img/03af04d84e967913ca774fe76251d037.png)T2】](https://cdn-images-1.medium.com/max/907/1*H5FUNT7W-egH_qduOCqKOg.png)

回到您的终端，我们将通过执行以下操作来克隆存储库:

`git clone [https://github.com/nisevi/staticwebsite.git](https://github.com/nisevi/staticwebsite.git) https-staticwebsite`

我刚刚添加的最后一部分只是给文件夹一个名称，在本例中是`https-staticwebsite`。我这样做是因为我不想与我用`SSH`克隆的同一个存储库发生冲突:

[![](img/7d5eb8f02e41ccb2e57693eee1c433c5.png)T2】](https://cdn-images-1.medium.com/max/889/1*dFAd_wkijiRI4syILsaQ0A.png)

如果我们看一下`Documents`中的文件夹，我们会看到:

[![](img/6f8588dc6f8763d7570b2a45a3a8ce98.png)T2】](https://cdn-images-1.medium.com/max/602/1*q2dXKpPjiG439Zq6K-eNfA.png)

我们将进入`https-staticwebsite`并对`README.md`文件做一些更改，然后创建一个签名提交。但是！由于这是另一个具有另一个 Git 配置的文件夹，我们必须通过以下操作再次设置我们的 GPG 密钥:

`git config user.signinkey 77384044A63D6CFF`

如果你不记得这个数字`77384044A63D6CFF`来自哪里，你可以回头看看我们已经配置了`GPG key`的部分。

[![](img/890f576cc6bd43d337264a271b8dc6d6.png)T2】](https://cdn-images-1.medium.com/max/847/1*_8Wh_cr1qIRIPE3kKaQWJw.png)

创建签名提交后，我们将推送我们的更改:

`git push origin master`

[![](img/4ce3ba84cc5ee4710ac60ebdd84b3d7e.png)T2】](https://cdn-images-1.medium.com/max/610/1*yKjbQAi0TgsFkosoyMb7PQ.png)

请注意，使用`HTTPS`时，您将始终需要输入您的`username`和`password`。如果您使用双因素认证而不是`password`，您将需要输入`token`。

### 如何生成 GitHub 令牌

转到您的个人资料并点击`Settings`。之后点击`Developer settings`选择`Personal access tokens`选项，按`Generate new token`赋予其回购权限，如下图:

[![](img/3fa5f7e4796a8900c7918cacda45e04c.png)T2】](https://cdn-images-1.medium.com/max/1002/1*tovwwChnol9YQcAYJiXRSw.png)

按下`Generate token`后，您将看到以下内容:

[![](img/ef64846766aca12a08e9c780b7504187.png)T2】](https://cdn-images-1.medium.com/max/1008/1*T5u4HJyRyH7MMAaZVgwY1w.png)

复制令牌并将其保存在安全的地方，因为这是您最后一次在这里看到它。复制后，如果您已经激活了双因素身份验证并希望使用`HTTPS`推送提交，则可以将其用作密码。

* * *