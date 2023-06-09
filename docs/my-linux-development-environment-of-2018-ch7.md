# 我的 2018 Linux 开发环境

> 原文：<https://dev.to/brpaz/my-linux-development-environment-of-2018-ch7>

在这篇博文中，我将谈一谈我的个人笔记本电脑的安装和软件，由 Linux 驱动。这多少受到了这篇文章的启发，但是是基于 Linux 的设置。

我不会列出我在这台机器上安装的所有应用程序，而是只列出我认为相关的和更侧重于开发的应用程序。

## 基础系统

现在我的机器运行的是 Linux Mint 18.3，但我想在 Ubuntu 18.04 发布后再移植回来。我真的对 Mint 没有任何抱怨，但是随着 Gnome 3 对 Unity 的替换，我想尝试一下。

## 生产力工具

*   [Boostnote](http://boostnote.io) —我的主要笔记应用。我主要用它来存储代码片段和开发活动的参考资料。它开源，跨平台，完全离线工作。我希望有一种更简单的方法在多台机器之间同步，但由于笔记存储为 CSON 文件，我使用一个私有的 Git repo 来同步。

*   简单笔记(simple note)—我用简单笔记作为我的“便签本”，用来快速记笔记和思考。主要是在我的手机上。

*   [Cerebro](http://cerebroapp.com) — Cerebro 是一款开源的跨平台启动器，类似于 Spotlight 和 Alfred for Mac。维护有点低 atm 和我觉得有必要叉它，但它工作得很好。它没有阿尔弗雷德那样多的插件，但我一直在开发一些。

## 互联网

*   谷歌浏览器(Google Chrome)——我的首选浏览器。

*   [火狐](https://www.mozilla.org/en-US/firefox/new/)——我的二级浏览器。

*   [Min](https://github.com/minbrowser/min) — Min 是一款极简主义的网络浏览器。由于 Linux 没有像 Mac 版的 [Fenetre](https://xn--fent-ipa.re/) 那样的东西，所以当我想浏览文档、跟随教程或以图片/并排模式在图片中观看视频时，我会使用它。

*   [Dropbox](https://www.dropbox.com/?landing=dbv2) —用于共享文件。

*   [Mailspring](https://getmailspring.com/) —邮件客户端。

*   Corebird — Twitter 桌面客户端

*   [Ramme](https://github.com/terkelg/ramme) — Instagram 桌面客户端

*   [Caprine](https://github.com/sindresorhus/caprine) —脸书信使桌面客户端

*   [Whatsapp 桌面](https://github.com/Enrico204/Whatsapp-Desktop) —内置通知和系统托盘集成的 Whatsapp 桌面客户端。

*   [Slack](https://slack.com/) —用于聊天和参与社区活动。

*   [Wireshark](https://www.wireshark.org/) —用于网络嗅探。在开发时查看多个应用程序之间的通信非常有用。

## 图形和多媒体

*   Pinta 是一个用于绘图和图像编辑的免费开源程序。这是我选择的图片编辑器。虽然 Gimp 是 Linux 上最流行的图像编辑器，但我发现 Pinta 使用起来更简单，更像 Photostop。

*   [XnConvert](https://www.xnview.com/en/xnconvert/) —一个强大的免费跨平台批量图像处理器，允许您组合 80 多个动作。

*   Draw.io 是我设计各种图表的首选应用，从流程图到序列图甚至是线框。它的 100%免费使用，可以与谷歌驱动器集成。

*   [快门](http://shutter-project.org/) —用于截图，并快速用箭头、方框等标注。它也有一些不错的效果，如模糊图像的敏感部分。

*   [ffmulticonverter](https://sourceforge.net/projects/ffmulticonv/) —用于在多种格式之间转换图像/视频。

*   [Peek](https://github.com/phw/peek) — Peek 允许录制屏幕并保存为 GIF 格式。对于创建演示 gif 非常有用，例如放在项目自述文件中。

*   [挑选](https://kryogenix.org/code/pick/) —颜色挑选器

*   [Trimage](https://trimage.org/) —图像压缩工具

## 声音和视频

*   VLC——我最喜欢的媒体播放器。版本 3 内置了 chromecast 支持，这就更好了。

*   [Kdenlive](https://kdenlive.org/) —用于视频编辑

*   [Kazam](https://github.com/sconts/kazam) —录制截屏视频

*   Spotify —用来听我最喜欢的音乐。

## 系统实用程序

*   [漂白位](https://www.bleachbit.org/) —系统清洁工具。

*   [堆栈器](https://github.com/oguzhaninan/Stacer) — Linux 系统优化和监控

*   咖啡因——让我的机器保持清醒

*   [套头衫](https://github.com/cgrossde/Pullover) —在我的桌面上接收[推倒](https://pushover.net)通知

*   管理我的剪贴板

## 编辑人员

*   Jetbrains (PHPstorm，WebStorm)——我的首选 IDE，

*   [Visual studio 代码](https://code.visualstudio.com/) —全部在一个文本编辑器中

*   vim(用于在命令行上编辑文件)

## 终端设置

*   [Tilix](https://github.com/gnunn1/tilix) —我最喜欢的终端模拟器。

*   Zsh shell——bash 的一个功能丰富的替代品

*   [zplug](https://github.com/zplug/zplug) —管理 zsh 插件。

*   [Zsh-完井](https://github.com/zsh-users/zsh-completions)—Zsh 的附加完井定义。

## 命令行实用程序

*   [tldr](https://github.com/tldr-pages/tldr) — tldr 是手册页的替代产品，但仅限于基本产品。快速查找任何命令语法的好方法。

*   [yadm](https://github.com/TheLocehiliosan/yadm) —点文件管理器

*   [fasd](https://github.com/clvv/fasd) — Fasd 是一个命令行生产力加速器。Fasd 为 POSIX shells 提供了对文件和目录的快速访问。它的灵感来自于[自动跳转](https://github.com/joelthelion/autojump)、 [z](http://github.com/rupa/z) 和 [v](https://github.com/rupa/v) 等工具。Fasd 跟踪您访问过的文件和目录，以便您可以在命令行中快速引用它们。

*   fzf —命令行模糊查找器

*   银色搜索器(silver searcher)——类似于 ack 的代码搜索工具，但速度更快

*   [lf](https://github.com/gokcehan/lf) —命令行文件管理器。

*   [pandoc](https://pandoc.org/) —从命令行在多种文件类型之间转换。

*   [pwgen](https://github.com/jbernard/pwgen) —密码生成器

*   [lnav](http://lnav.org/) —一种小型的高级日志文件查看器

*   [trash-cli](https://github.com/andreafrancia/trash-cli) — trash-cli 丢弃记录原始路径、删除日期和权限的文件。

*   opn-cli —打开网站、文件、可执行文件等内容。跨平台。

*   [ascicinema](https://asciinema.org/) —以正确的方式记录并分享您的终端会话。

*   一个用于管理/etc/hosts 文件的幂等命令行工具。

*   [get-port-cli](https://github.com/sindresorhus/get-port-cli) —获取可用端口

*   [public-ip-cli](https://github.com/sindresorhus/public-ip-cli) —获取您的公共 ip 地址

*   I[internal-ip-CLI](https://github.com/sindresorhus/internal-ip-cli)—获取您的内部 IP 地址

*   speedtest-cli —使用 speedtest.net 测试互联网带宽的命令行界面

*   fkill-cli —惊人地杀死进程。跨平台。

*   [doctoc](https://github.com/thlorenz/doctoc) —在本地 git 存储库中生成降价文件的目录。链接兼容 github 或其他网站生成的锚点。

## *开发环境*

我在开发环境中广泛使用 Docker。我仍然在主机中安装了常见的运行时，如 go、php、node(带有 [nvm](https://github.com/creationix/nvm) )和 ruby(带有 [rvm](https://rvm.io/) )。

*   [码头工人](https://www.docker.com/)

*   [Docker-compose](https://github.com/docker/compose) —使用 Docker 定义和运行多容器应用程序

*   [Minikube](https://github.com/kubernetes/minikube) —在当地经营 Kubernetes

*   [网真](https://github.com/datawire/telepresence) —针对远程 Kubernetes 或 OpenShift 集群的本地开发

*   ctop —类似顶层的容器指标界面

*   [流浪者](https://www.vagrantup.com/) —开发环境变得容易了。我现在很少流浪，除非我想从事基础设施方面的工作。(例如:测试旋转新集群或测试可行的行动手册)。

*   [Dnsdock](https://github.com/aacebedo/dnsdock) —用于管理 Docker 容器的 dns。

## 开发工具

*   [how2](http://stackoverflow%20from%20the%20terminal) —从终端堆栈溢出

*   [Pet](https://github.com/knqyf263/pet) —简单的命令行片段管理器。我用它来存储我最常用的命令。

*   [can use-cmd](https://github.com/sgentle/caniuse-cmd)—can use 命令行工具

*   hub 是一个命令行工具，它包装了 git，以便用额外的特性和命令来扩展它，使使用 GitHub 更容易。

*   [传统-变更日志](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-cli)-从 git 元数据生成变更日志

*   [release-it](https://github.com/webpro/release-it)—Git repos 和 npm 包的 CLI 发布工具。

*   [git-open](https://github.com/paulirish/git-open) —键入`git open`在浏览器中打开 GitHub 页面或存储库网站。

*   git-semver —用于语义版本控制的 git 插件

*   git 的文本模式界面

*   jq 是一个轻量级的、灵活的命令行 JSON 处理器

*   [python-gitlab](https://github.com/python-gitlab/python-gitlab) — GitLab 客户端

*   [过量使用](https://github.com/brigade/overcommit)—管理和配置 [Git 挂钩](http://git-scm.com/book/en/Customizing-Git-Git-Hooks)的工具。

*   [tmux](https://tmux.github.io/) 和 [tmuxp](https://github.com/tmux-python/tmuxp) —终端复用器。

*   [gitbook-cli](https://github.com/GitbookIO/gitbook-cli) — GitBook 的命令行界面

*   [pageres-cli](https://github.com/sindresorhus/pageres-cli) —抓取网站截图

*   现代命令行 HTTP 客户端——用户友好的 curl 替代产品，具有直观的 UI、JSON 支持、语法突出显示、类似 wget 的下载、扩展等

*   yeoman 是一个允许从一系列模板中搭建项目的工具。

*   [generator-editorconfig](https://github.com/Knorcedger/generator-editorconfig) —生成。编辑器配置文件。

*   [许可证](http://nishanths.github.io/license/) —从命令行创建许可证。

*   [gi](https://www.gitignore.io/) —创造有用。git 忽略项目的文件

*   [travis.rb](https://github.com/travis-ci/travis.rb) — Travis CI 客户端(CLI 和 Ruby 库)

*   [http-server](https://www.npmjs.com/package/http-server) — http-server 是一个简单的零配置命令行 http 服务器。对于生产应用来说，它足够强大，但是对于测试、本地开发和学习来说，它又足够简单和容易掌握。

*   codeclimate-CLI—Code Climate 是 Code Climate 分析平台的命令行界面。它允许您在 Docker 容器内的本地机器上运行代码气候引擎。

*   my CLI—MySQL 的终端客户端，具有自动补全和语法突出显示功能。

*   [json-server](https://github.com/typicode/json-server) —不到 30 秒就能获得零编码的全假 REST API。

*   [localtunnel](https://github.com/localtunnel/localtunnel) — localtunnel 向全世界公开您的本地主机，以便于测试和共享！没有必要为了让其他人测试您的更改而搞乱 DNS 或进行部署。

*   [mailhog](https://github.com/mailhog/MailHog) —基于 Web 和 API 的 SMTP 测试

*   artilery—cannon 是一个现代化的、功能强大的&易用的负载测试工具包。使用它来发布可扩展的应用程序，在高负载下保持高性能&弹性。

*   [Postman](https://www.getpostman.com/) — Postman 让 API 开发变得简单。

*   [dev docs-Desktop](https://github.com/egoist/devdocs-desktop)—[dev docs . io](https://devdocs.io/)在一个快速、有序、可搜索的界面中结合了多个 API 文档。这是一个非官方的桌面应用程序。

## DevOps 工具

*   用于数字海洋服务的命令行工具。

*   gcloud-sdk —与谷歌云基础设施交互的官方 sdk。

*   aws-cli —这个包为 Amazon Web Services 提供了一个统一的命令行界面。

*   自动化基础设施

*   [地形](https://www.terraform.io/) —以代码形式编写、规划和创建基础设施

*   [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) — kubectl 是一个命令行界面，用于针对 Kubernetes 集群运行命令

*   [kubectx](https://github.com/ahmetb/kubectx) —在 kubectl 中快速切换集群和名称空间的方法！

*   [kubeval](https://github.com/garethr/kubeval) —验证您的 Kubernetes 配置文件，支持多个 Kubernetes 版本

*   [helm](https://github.com/kubernetes/helm)—Kubernetes 套餐经理

*   [heroku-cli](https://devcenter.heroku.com/articles/heroku-cli)—Heroku 命令行界面(CLI)让您直接从终端轻松创建和管理 Heroku 应用程序。这是使用 Heroku 的重要部分。

*   [forge.sh](https://forge.sh/) —在 Kubernetes 中定义和部署多容器应用程序，来自源代码

*   [Firebase 工具](https://github.com/firebase/firebase-tools)—Firebase 命令行工具

*   Dpl (dee-pee-ell)是一个为持续部署而设计的部署工具。

## 结论

这是当前开发机器。希望这对你有用。

我一直在寻找方法来改进我的工作流程，所以如果你使用了任何你认为有用的工具，但没有列在这里，请随意评论这篇文章。

Linux 可以是一个非常强大的开发环境。遗憾的是，像阿尔弗雷德[或 T2[Fenetre]这样令人惊叹的 Mac 应用程序没有可行的 Linux 替代品。这是我最怀念的。我从未使用过 Mac，但我感受到了这些应用程序的力量！](https://www.alfredapp.com/)