# 我的 Rust 程序发布清单

> 原文：<https://dev.to/sharkdp/my-release-checklist-for-rust-programs-1m33>

发布项目的新版本是开源维护者更费力的任务之一。有许多很棒的工具可以自动完成这个过程的一部分，但是通常仍然需要很多手动步骤。此外，还有很多事情可能出错。可能会引入新的 bug，依赖性更新可能会出错，自动部署可能不再工作。

在对我的三个 Rust 项目( [fd](https://github.com/sharkdp/fd) 、 [hyperfine](https://github.com/sharkdp/hyperfine) 和 [bat](https://github.com/sharkdp/bat) )进行了一些实践之后，我的工作流程已经收敛到一些工作得相当好的东西，并且避免了我在过去走进的许多陷阱。我写这篇文章的希望是这个过程也能对其他人有用。

以下是我对 [fd](https://github.com/sharkdp/fd) 的发布清单，但我对其他项目也有非常相似的清单。按照给定的顺序执行这些步骤很重要。

1.  检查并更新**依赖关系**。

    a)使用 [`cargo outdated`](https://github.com/kbknapp/cargo-outdated) 来检查过时的依赖关系。 [deps.rs](https://deps.rs/repo/github/sharkdp/fd) 也可以用来获取同样的信息。
    b)运行`cargo update`将依赖项更新到最新的兼容(次要)版本。如果可能和有用，手动更新到新的主要版本。

    至于新的主要版本的更新，看看上游的变化，仔细评估是否有必要更新(现在)。

2.  获取自上次发布以来的更新列表。

    转到 GitHub-> Releases-> "*XX*commits to master since this release "以获得自上一个版本以来所有更改的概述。

    *示例: [fd/compare/v7.1.0...](https://github.com/sharkdp/fd/compare/v7.1.0...master)大师*

3.  更新**文档**。

    a)审查并更新`-h`和`--help`文本。
    b)更新自述文件(程序使用、文档新功能、更新最低要求的 Rust 版本)
    c)更新手册页。

4.  本地安装最新的`master`和**测试新功能**。

    a)运行`cargo install -f`。
    b)手动测试新功能。
    c)运行基准测试以避免性能退化。

    在理想的世界中，我们已经为所有的新代码编写了测试。这些测试也在我们的 CI 管道中运行，所以没有什么可担心的，对吗？以我的经验，总有需要人工审核的东西。对于更难测试的 CLI 工具来说尤其如此，因为它们对交互式终端环境有着复杂的依赖性。

5.  **清理**代码库。

    a)运行`cargo clippy`并检查建议的更改【可选】
    b)运行`cargo fmt`以自动格式化您的代码。
    c)运行`cargo test`以确保所有测试仍然通过。

    在我的 CI 渠道中，最后两个步骤通常是自动化的。为了完整起见，这里包含了它们。

6.  凹凸**版本**信息。

    a)在`Cargo.toml`
    中更新项目版本 b)运行`cargo build`更新`Cargo.lock`
    c)在整个存储库中搜索旧版本并根据需要更新(自述文件、安装说明、构建脚本、..)

    *忘记同时更新`Cargo.lock`在过去阻止了我成功发布到 [crates.io](https://crates.io/) 。*

7.  **试运行`cargo publish`的**。

    `cargo publish --dry-run --allow-dirty`

    *运行`cargo publish`是发布流程的最后一步。在这个阶段使用预演功能可以避免以后出现意外。*

8.  提交、推动并等待 CI 成功。

    `git push`最后步骤和**中的所有更新都将等待 CI 通过**。

    我过去常常立即标记我的“版本更新”提交，以开始自动化部署。有了这个中间的“等待 CI”步骤，肯定可以防止一些失败的发布。

9.  写**发行说明**。

    在等待 CI 完成的时候，我已经开始写[发布说明](https://github.com/sharkdp/fd/releases)。我浏览了更新列表，并将更改分类为“特性”、“更改”、“错误修复”或“其他”。我通常会包含相关 Github 问题的链接，并尽量注明原作者。

10.  标记最新提交和**开始部署**。

    `git tag vX.Y.Z; git push --tags`

    *这里假设已经建立了 CI 管道来处理实际的部署(将二进制文件上传到 GitHub)。*

11.  创建**发布**。

    在 GitHub 上创建实际版本，并复制发行说明。

12.  **验证**部署。

    确保所有二进制文件都已上传。如果可能，手动测试二进制文件。

13.  **发布**到[板条箱. io](https://crates.io/) 。

    确保您的存储库是干净的，或者克隆一个新的存储库副本。那就跑

    `cargo publish`

    *在`git push --tags`步骤之后这样做。如果`cargo publish`调用出错，可以删除 git 标签，但是如果通过`git push --tags`的部署失败，则`cargo publish`无法撤销。*

14.  通知**包维护者**更新。

    *例如，Arch Linux 有可能[将软件包标记为“过期”](https://www.archlinux.org/packages/community/x86_64/fd/)。包含您的发行说明的链接，并突出显示与软件包维护者相关的更改(需要安装的新文件、新的依赖项、构建过程中的更改)*

你有类似的放行清单吗？如果是这样，我很想听听你有什么不同的做法，或者我可能遗漏了哪些步骤。