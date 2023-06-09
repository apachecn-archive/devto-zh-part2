# 通过 Jekyll 使用 Linux 和 Ruby 的 Windows 子系统

> 原文：<https://dev.to/seankilleen/using-windows-subsystem-for-linux-and-ruby-with-jekyll-1ji7>

## 目标/问题

这个博客已经存在一段时间了。我一定会有一些死链接或图像。我想我应该检查一下，清理一下这个地方。

幸运的是，这个博客也运行在 Jekyll 和 ruby 上，Ruby 生态系统有一个很棒的宝贝叫做 [html-proofer](https://github.com/gjtorikian/html-proofer) 可以帮助我们做到这一点。

因此，我将 gem 添加到我的项目中，安装它，并运行 html-proofer，只看到:

> (load error)y23/lib/ruby/gems/2 . 3 . 0/gems/FFI-1 . 9 . 18-x64-mingw 32/lib/FFI/library . Rb:147:在“ffi_lib 中的块”中:无法打开库“libcurl”:找不到指定的模块。
> 
> 无法打开库“libcurl.so.4.dll”:找不到指定的模块。

那…不太好。我在谷歌上搜索了一堆，发现这个链中的一些工具似乎不能很好地与 Windows 兼容，我也没能很快找到解决方案。所以我打算暂时放弃这个任务，而不是与 Ruby/Windows 生态系统作斗争。

…直到我想起我可以将 Linux 直接引入我的环境并完成工作。

## 解决方案:进入 Windows 子系统 for Linux (WSL)

如果你没有听说过[Windows System for Linux(WSL)](https://docs.microsoft.com/en-us/windows/wsl/about)，这是微软的一个非常棒的项目，它允许你在 Windows 中运行特定的 Linux 发行版，让你可以两全其美。

我觉得值得一试。伙计，我是对的。

## 获取 WSL

我打开微软商店应用，搜索“Ubuntu”。我从 Canonical 找到了 Ubuntu 发行版，点击了`Install`。完成后，我点击了`Launch`。

我等了几分钟，让一些设置完成，按照提示创建用户名和密码，然后我就有了一个可以运行的 shell。

…真的有那么简单吗？的确如此。

## 安装 Ruby

RVM 似乎是安装和管理 Ruby 版本的正确方法，所以我决定使用它。

我按照[的安装说明](https://rvm.io/rvm/install)操作。以下所有步骤都是在 Ubuntu 窗口中执行的。

*   接受公钥:`gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB`
*   为 rvm 安装一些先决条件:`sudo apt-get install software-properties-common`
*   添加 RVM 源回购:`sudo apt-add-repository -y ppa:rael-gc/rvm`
*   更新 apt-get: `sudo apt-get update`
*   安装 RVM: `sudo apt-get install rvm`

RVM 安装后，它打印出一些额外的说明，所以我也:

*   将我的用户添加到“rvm”组:`sudo usermod -a -G rvm sean`
*   运行附加的安装后脚本:`source /etc/profile.d/rvm.sh`
*   虚拟机注销:`logout`
*   从 Windows 中重新启动应用程序，并重新打开外壳。

然后我用 RVM 安装了 Ruby: `rvm install ruby`

酷！我有鲁比。

## Git 安装和克隆

为了构建和运行 html-proofer，我必须访问我的博客的存储库。这意味着用 GitHub repo 设置 git。我:

*   Install git: `sudo apt-get install git`
*   设置我的用户名:`git config --global user.name "SeanKilleen"`
*   设置我的电子邮件:`git config --global user.email "SeanKilleen@gmail.com"`
*   查看博客回购:`git clone https://github.com/SeanKilleen/seankilleen.github.io`

## 建博客

我需要:

*   进入博客的目录:`cd seankilleen.github.io`
*   安装捆扎机:`gem install bundler`
*   为博客安装宝石:`bundle install`

现在，我可以通过`bundle exec jekyll build`建立博客了

而且很管用！不寒酸。

## 添加 html-proofer(从 Windows！)

好的，所以我需要通过修改我的 Ubuntu 发行版上的`gemfile`来添加`html-proofer`宝石。但是，我不像了解 windows 环境那样了解环境，我宁愿快速使用记事本。

…谢天谢地，您完全可以在 Windows 中编辑 Linux 发行版文件！

我:

*   已打开 Windows 资源管理器
*   导航至`C:\Users\%USERNAME%\AppData\Local\Packages`
*   点击进入 Ubuntu 发行版文件夹:`CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc`
*   导航至`\LocalState\rootfs\home\sean`

哇，那是我的回购文件夹！我只需要点击进入`seankilleen.github.io`文件夹，然后就可以在记事本内打开`gemfile`了。

*   我添加一行:`gem 'html-proofer'`并保存。
*   然后我切换回我的 Ubuntu 窗口。我输入`cat gemfile`,你瞧，我看到我的变化反映在那里。不错！

如果能直接从我的 Ubuntu 发行版将这些修改提交给 GitHub 就太好了。这可行吗？

…是的，这是可行的。

## 在 WSL 中设置我的 GitHub 推送账户

当您启用了双因素身份验证时，推送 GitHub 需要一个个人访问令牌(您应该这样做！).所以，我:

*   在 Windows 上打开网络浏览器，前往[https://github.com/settings/tokens](https://github.com/settings/tokens)
*   创建一个个人访问令牌，并将其保存在可以保存密码的地方(因为它本质上就是一个密码)
*   切换回我的 Ubuntu WSL 窗口
*   如果我不在 repo 文件夹中，请导航到该文件夹
*   添加我未提交的文件:`git add .`
*   用`git status`检查
*   提交更改:`git commit -m "Adding html-proofer gem"`
*   尝试推动变更:`git push`
*   输入用户名和个人访问令牌代替密码

现在我已经提交了对我的 repo 的修改，来自 Ubuntu。

## 回报:运行 html-proofer 没有问题！

在我的 WSL 窗口中，我:

*   导航到 repo 文件夹
*   运行`bundle install`以确保我已经安装了所有的东西
*   运行`bundle exec jekyll build`来创建输出，它位于一个`_site`文件夹中。
*   运行`bundle exec html-proofer ./_site`执行 html-proofer。

瞧啊。我看到了输出。

## 哇。太酷了。

WSL 背后的团队投入了大量的工作来使这种体验无缝。一点点的谷歌搜索让我按照指示在 Ubuntu 上运行，而没有牺牲作为我的主设置的 Windows 环境的易用性和内存。

我希望这有助于介绍 Linux 的 Windows 子系统。但是如果你现在原谅我，我有一些链接去修复！

[用 Jekyll 使用 Linux 和 Ruby 的 Windows 子系统](https://SeanKilleen.com/2018/09/using-wsl-to-make-ruby-play-nice/)最初由 Sean Killeen 于 2018 年 9 月 14 日在 SeanKilleen.com[发表。](https://SeanKilleen.com)