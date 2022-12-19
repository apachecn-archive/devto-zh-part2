# 确保 git 提交不会在 Windows 上欺骗您

> 原文：<https://dev.to/ankursheel/securing-git-commits-from-tricking-you-on-windows-39ig>

您知道默认情况下，您可以以任何人的身份签入 git 存储库中的代码吗？你只需要运行`git config.username = {username}`和`git config.email={email}`就可以骗 git 以为你是别人。为了更好地理解这会产生什么样的问题，去读读迈克·格维茨的文章，[一个可怕的故事](https://mikegerwitz.com/papers/git-horror-story)。幸运的是，Git 允许您很容易地解决这个问题——让您使用 GPG(GNU Privacy Guard)签署提交。

GitHub 帮助文章[使用 GPG](https://help.github.com/articles/signing-commits-using-gpg/) 签署提交是如何设置它的一个很好的指南。但是，它要求您使用 git bash 控制台。那么，如果你像我一样在 Windows 机器上并且喜欢使用 GUI，你会怎么做呢？不要害怕这本指南会告诉你你需要知道什么。

使用 Gpg4win 和 Git 需要一点配置，所以让我们开始配置它。

## Setup Kleopatra

1.  下载 [Gpg4win](https://www.gpg4win.org/) 并使用安装程序安装。

2.  进入开始菜单，启动 Kleopatra

3.  点击文件->新密钥对

[![Kleopatra](../Images/bed329eb16047f2100eed0270d371abc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kBH2gVmN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xwhs5ckowzq553eteww4.png)

1.  点击创建个人 OpenPGP 密钥对

[![Key Pair Creation](../Images/5468b897504dafd02031267075433e64.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZjpRSngc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f17aykhv4sy3wg18sfyh.png)

1.  输入详细信息，然后单击下一步。

[![Enter details](../Images/402e1cd08722b80f28b31ba26c00f4d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gslXUK4O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h5eykvkrnbfl7td4g8oy.png)

1.  查看并创建密钥。这将显示一个弹出窗口，要求您输入密码以保护密钥。

2.  输入密码，然后单击确定

[![Enter Paraphrase](../Images/6d462e95bed5129b4bfeacfc53de1a25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3n92HTbv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2n08773fa1paru3rtnr4.png)

1.  此时，应该创建了密钥对。单击完成。

您可以创建密钥的备份，并将其保存在安全的地方。

[![Key pair Created](../Images/36bab39c5d545ba2269aa6a96e5e31b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WKU_sQr6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/az95njpwjagh7847s7nl.png)

1.  您现在应该可以在 Kleopatra 中看到密钥了

[![Key in Kleopatra](../Images/915730a6e85e1c49dd312b52b74a3f3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jazb-QKF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y5mmsdpetdi431wmk2lx.png)

1.  记下密钥 ID。我们马上就需要它。

*这是上面截图中的 d1e 4471*

1.  双击密钥以查看证书详细信息

[![Key Details](../Images/4f298f34e142dbec0f807a298f19c202.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VZ06dV-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/skr40shaed3fb5y0mdob.png)

1.  点击导出并复制公共 gpg 密钥。

*确保你复制了所有东西，包括`-----BEGIN PGP PUBLIC KEY BLOCK-----`和`-----END PGP PUBLIC KEY BLOCK-----`T3】*

[![PGP public key](../Images/03785fff79e183773859043e2df4c156.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D1hfKT7u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pksmdnqadh7o1u1sw2qu.png)

## 配置 Git 对提交进行签名

1.  更新全局 git 配置以使用签名密钥。请记住上面的密钥 ID，您在这里会用到它。

2.  在 git 控制台中输入`git config user.signingkey D1E4471`

如果您想在全局级别上设置它，请使用`git config --global user.signingkey D1E4471`代替

1.  指示 git 自动签署每次提交。

2.  在 git 控制台中输入`git config commit.gpgsign true`。如果您想在全局级别上设置它，请使用`git config --global commit.gpgsign true`来代替。

3.  指示 git 自动签署每个标签。

4.  在 git 控制台中输入`git config tag.gpgsign true`。如果您想在全局级别上设置它，请使用`git config --global tag.gpgsign true`来代替

5.  告诉 git 使用 gpg4win 版本的 gpg.exe

6.  在 git 控制台中输入`git config --global gpg.program "C:\Program Files (x86)\GnuPG\bin\gpg.exe"`。

如果您将程序安装在自定义位置，您可能需要调整程序的位置

为了检查它是否工作，将一些代码提交到一个已经设置为使用签名的存储库中。应该会提示您输入之前输入的密码。

[![Enter Passphrase](../Images/e745ee71359763a2608bdea3fd3be2e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--veg18cXk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tm0n7ggg576sadu404ra.png)

## 配置 Kleopatra 将密码缓存更长时间

1.  运行克利奥帕特拉。点击*设置- >配置 Kleopatra*

2.  在配置页面上点击*GnuPG System->Private Keys*并向下滚动到控制安全部分的选项。

3.  设置缓存时间。86400 秒等于 1 天。

[![Configure Kleopatra](../Images/bbe70389f0a1b563e39b65c3ef2e267d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SHaopJIQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/owh8hjtqe44uk6yirzno.png)

## 配置 Github 以显示已验证的提交

1.  按照[中的步骤向你的 Github 账户](https://help.github.com/articles/adding-a-new-gpg-key-to-your-github-account/)添加一个新的 GPG 密钥，这样 GitHub 就可以显示已验证的提交。*使用你之前复制的公共 gpg* 密钥。

要检查它是否工作，请按签名提交。您应该会在 Github 的提交历史中看到 Verified 标签。

提交应该有一个经过验证的标记，您可以单击它来查看其他详细信息

[![Verified Commits](../Images/dc86c1ff17042302feef79630a5cb0c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BM_nYCby--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oz35fl3rfl074o8osdyb.png)

## 结论

现在所有的提交都将有一个经过验证的标签。你签署你的承诺了吗？请在评论中告诉我