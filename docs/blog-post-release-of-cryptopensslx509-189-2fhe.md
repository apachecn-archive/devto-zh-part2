# 博文:发布 Crypt::OpenSSL::X509 1.8.9

> 原文：<https://dev.to/jonasbn/blog-post-release-of-cryptopensslx509-189-2fhe>

我刚刚发布了 Crypt::OpenSSL::X509 1.8.9。请注意，这原本不是我的发行版，但我帮了作者[丹·萨利](https://metacpan.org/author/DANIEL)一点忙，因为我是他的 [Crypt::OpenSSL::PKCS12](https://metacpan.org/pod/Crypt::OpenSSL::PKCS12) 和 [Crypt::OpenSSL::X509](https://metacpan.org/pod/Crypt::OpenSSL::X509) 的用户，我对发行版的继续存在、可用性和功能感兴趣。

所以这篇博文更多的是描述参与的过程，用我参与 Crypt::OpenSSL::X509 和它的表亲 Crypt::OpenSSL::X509 作为例子。

我开始时为我们遇到的一些问题做了一些 pr，我慢慢地参与进来，因为 Dan 没有真正积极地维护发行版，没有做 Perl 和其他东西——所有这些都是完全可以接受的理由。丹开始在暂停/CPAN 上给我*联合维护者*，这样我就可以上传发布。我做的第一个版本只是从一个分支开始，合并了一个公共关系后期发布，这不是最好的策略，但它成功了。

现在我在两个库上都有提交特权，在暂停/CPAN 上我有*共同维护者*，所以我可以实现和上传发布。鉴于这种特权肯定令人望而生畏，我面临着许多问题，有些很容易回答，有些比较难，有些目前还不能回答——总之，问题会在你的脑海中出现:

1.  我能改变多少？
    *   风格？
    *   工具链？
    *   功能性？
2.  我应该修复所有问题吗？
3.  我了解实现的所有方面吗？
4.  如果我不能做出贡献呢？

随着时间的推移，随着你越来越熟悉所讨论的项目和其他部分，以及越来越多的实践，许多答案将会出现。目前我认为自己是一个学徒，在这种背景下，一切都是新的，令人困惑，你害怕打破一些东西。

现代软件开发非常宽容，我们有:

–版本控制和分支策略

–持续集成和单元测试套件

–协作平台和开源

–当然还有 Google 和 StackOverflow

因此，很容易回到最初的状态，从人或机器那里获得反馈，或者获得帮助或找到例子，这些都与你试图完成的事情相似。

我创建的一些 PRs 支持 Travis integration for continuous integration，这是我可以在不影响实际代码的情况下做出的贡献——可以说很简单。其他 pr 解决了构建工具的问题。两个发行版都基于[模块::安装](https://metacpan.org/pod/distribution/Module-Install/lib/Module/Install.pod)，我自己的所有发行版都基于 [Dist::Zilla](https://metacpan.org/pod/Dist::Zilla) ，但是现在看来坚持已经在工作的东西是个好主意，没有必要为了改变而改变。

对于编码风格，我认为坚持项目现有的编码风格是个不错的主意。当项目进一步发展时，也许甚至是在接纳更多的贡献者时，或者如果 PRs 变得难以审查或理解时，也许是时候记录编码风格或实施编码风格了。

这就引出了下一点。Crypt::OpenSSL::X509 和 Crypt::OpenSSL::PKCS12 都是基于 C 库的 Perl 实现。对我来说，当审查 PRs 或熟悉项目代码库时，阅读一些 C 代码是一个了不起的变化。

熟悉现有的代码库，也可以通过对 bug 进行分类来完成，两个项目当前的 bug 数量如下所示；

–GitHub:[Crypt::OpenSSL::X509](https://github.com/dsully/perl-crypt-openssl-x509)(17 期)

–GitHub:[Crypt::OpenSSL::PKCS12](https://github.com/dsully/perl-crypt-openssl-pkcs12)(2 期)

所以应该有一些东西让我开始。

在我看来，你不必修复所有的错误，但这是一个很好的方式来挖掘和学习很多东西。如果你有问题，不要犹豫联系 bug 报告者，他们可能是长期用户，对项目内部工作有广泛的了解。贡献者也是如此，他们甚至可能知道更多，因为他们实际上已经做出了改变，并且正在请求合并。

让我发布 Crypt::OpenSSL::X509 1.8.9 的实际上是一个公关，我审查过，它是我自己提出修改的代码的一部分，所以我会说我对正在发生的事情有所了解。然而，这一改变针对的是一个我不熟悉的操作系统——所以我给投稿人写了封信，问他们什么时候有什么不清楚的地方。我得到了一个很棒的回应，指向一些好的文档，所以我学到了一些东西，我可以完成我的审查。

另一个你可以应用的策略，或者急于开始破解的策略，是添加测试。检查测试覆盖率，并在薄弱点实施更多的测试，这也是了解项目的功能和组成的好方法。

我的建议是开始，回顾，阅读，编码，学习，测试……我确实考虑了所有的学徒水平，当你第一次发布你自己的特性或应其他人的要求，你不再是一个学徒——你是一个真正的贡献者——这是值得追求的目标。

祝你的努力好运，有很多项目要做，当学徒没什么不好，所有的大师都曾经是学徒。