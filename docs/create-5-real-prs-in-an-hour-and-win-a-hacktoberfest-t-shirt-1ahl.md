# 在一个小时内创建 5 个真正的 pr 并赢得一件 Hacktoberfest T 恤

> 原文：<https://dev.to/andyli/create-5-real-prs-in-an-hour-and-win-a-hacktoberfest-t-shirt-1ahl>

一年一度的 [Hacktoberfest](https://hacktoberfest.digitalocean.com) ，向创建 5 个或更多 GitHub pull 请求的人免费赠送 t 恤，将在一周后结束。

多年来，我一直是一个积极的开源贡献者，所以我很有信心，即使不积极努力，我最终也会实现 5 PRs。结果我错了——因为我现在是 Haxe Foundation org 的一员，我在其中贡献了最多，我并不真的让 PRs 来贡献，因为我只是推我的提交，这并不计入 Hacktoberfest 的目标。

幸运的是，周围有很多唾手可得的水果，我很容易就成功地达到了 5 PRs 标志。请注意，我没有编写无用的 PRs 来解释句子或对代码进行微小的格式化。我做出了有益的“真正的”贡献。以下是方法。

## 维护套餐！

人们可能没有意识到的一件事是维护软件包，这需要持续的工作并且很容易做到(即使对于初学者来说)。每当你使用一个包管理器来安装某个东西时，它会从一个存储库中取出这个包。通常在一个库中有数千个包，并且由于新版本不断出现，这些包需要一直更新(通常是手动的)。

Homebrew-core 是一个非常好的资源库，因为它是通过 GitHub 维护的，这是当今大多数开源贡献者所熟悉的。我们只是给他们发送 PR 来更新它的包(他们称之为公式)。

### 找出过时的东西

我们可以利用 [Repology](https://repology.org/) 来找出什么是过时的。我们查找 [Repology homebrew 页面](https://repology.org/repository/homebrew)，点击“过时的”元包编号链接。

[![Outdated homebrew packages](img/e3e80e0a9a828c38ca06a427a0e141ba.png)T2】](https://repology.org/metapackages/?inrepo=homebrew&outdated=1)

从那里，我们只是挑选一些熟悉的软件来工作——也许你以前用过那个软件，或者它是一个简单的用你擅长的语言构建的软件。尝试选择只需要次要/补丁更新的版本，例如从 1.2.3 到 1.2.4 或 1.3.0，而不是到主要的 2.0.0 版本。确保没有已创建的现有[更新 PRs](https://github.com/Homebrew/homebrew-core/pulls) 。

### 做更新

我们可以参考 [homebrew-core 的 CONTRIBUTING.md](https://github.com/Homebrew/homebrew-core/blob/master/CONTRIBUTING.md#submit-a-version-upgrade-for-the-foo-formula) 来了解如何进行更新。

我们可以(A)通过更新`url`和`sha256`来编辑公式文件，然后手工发送一个 PR，或者(B)使用 [`brew bump-formula-pr`](https://github.com/Homebrew/brew/blob/master/Library/Homebrew/dev-cmd/bump-formula-pr.rb) 命令来做同样的事情。

作为参考，[这里有一个我用如下命令制作的 PR](https://github.com/Homebrew/homebrew-core/pull/33377) 。

```
brew bump-formula-pr --strict bmake --url=http://www.crufty.net/ftp/pub/sjg/bmake-20180919.tar.gz 
```

### 就是这样！

发出 PRs 后，我们可以等待维护人员的反馈。如果可以成功构建包，通常会马上接受。此外，你可以在登录后点击 Hacktoberfest 主页上的“检查你的进度”按钮，以确保你已经完成了足够的 pr:)