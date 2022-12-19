# 另一个点文件库

> 原文：<https://dev.to/leonardofaria/another-dotfiles-repository---leonardos-version-4k6e>

几天几个星期我得到了一台 MacBook Air 作为备用机。由于它的 SSD 比我的主 Mac 小，我无法使用 Time Machine。这绝对是最终创建我的[点文件](https://github.com/leonardofaria/dotfiles)库的好时机。我的知识库是基于[的 webpro/dotfiles](https://github.com/webpro/dotfiles/) (如果你感到困惑，你可能想看看他的回购协议来理解这个)。

[![](../Images/d5af30cf6141d3488fe0a928c6c76bae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LvSbnRfo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/leonardofaria/dotfiles/master/screenshot.jpg)

## 怎么用？

*   确保你有更新的 macOS 和 Xcode 的命令行工具:`sudo softwareupdate -i -a && xcode-select --install`
*   在您的主目录中克隆回购:`cd ~ & git clone git@github.com:leonardofaria/dotfiles.git ~/.dotfiles`
*   运行`./install.sh`

## 装的是什么？

### 通过 brew 安装

> 查看不太受欢迎的应用程序的描述

*   bats 自动化测试系统
*   [dock util](https://github.com/kcrawford/dockutil)–用于管理 dock 项目的命令行工具
*   [exa](https://the.exa.website/)——现代版`ls`
*   ffmpeg
*   饭桶
*   git-extras
*   快上来
*   一个现代的命令行 HTTP 客户端
*   雨果
*   图像魔术
*   关系型数据库
*   neo fetch-一个命令行系统信息工具
*   nginx
*   python3
*   sqlite
*   树
*   [unar](https://theunarchiver.com/command-line)–一种简单的文件解压方式
*   wget
*   wifi 密码
*   故事

### 通过酿造桶安装

> 使用 brew cask 安装 Mac 应用程序绝对可以节省你几个小时的浏览时间

*   appcleaner
*   原子
*   椰子电池
*   码头工人
*   火狐浏览器
*   font-meslo-for-powerline
*   极客工作台
*   使数字化
*   谷歌浏览器
*   iterm2
*   发射火箭
*   续集专业版
*   松弛的
*   sourcetree(资源分区)
*   景象
*   声田
*   tableplus
*   取消归档
*   播送
*   visual studio 代码
*   vlc

### 快速查看插件

*   betterzip
*   qladdict
*   qlcolorcode
*   qlimagesize
*   qlmarkdown
*   qlstephen
*   qqvideo
*   快速查看-csv
*   快速查看-json
*   可疑包裹
*   webpquicklook

### 其他东西也装上了

*   通过`nvm`的节点 8，带有几个全局包:
    *   [对接](https://www.npmjs.com/package/dockly)
    *   [列表-脚本](https://www.npmjs.com/package/list-scripts)
    *   [npm 检查更新](https://www.npmjs.com/package/npm-check-updates)
    *   [npm 脚本树](https://www.npmjs.com/package/npm-scripts-tree)
    *   [脚本列表](https://www.npmjs.com/package/script-list)
    *   太空猪
    *   [速度测试](https://www.npmjs.com/package/speed-test)
*   Ruby 2.5.1 via `rvm`
*   [哦我的 zsh](https://github.com/robbyrussell/oh-my-zsh) ，带有 [powerlevel9k](https://github.com/bhilburn/powerlevel9k) 主题和 git、rails、ruby、npm 和 osx 插件

## Mac 默认值

有许多可定制的 Mac 设置。查看 [`macos/defaults.sh`](https://github.com/leonardofaria/dotfiles/blob/master/macos/defaults.sh) 了解详情。

### 停靠物品

您可以更新您的 dock 运行:`dotfiles dock`。

注:这将删除您当前所有的 dock 项目。

## 什么事情没有自动完成

### 额外环境变量

在`system/.custom`中添加额外的信息环境变量。

### iTerm 2

打开首选项，一般情况下，选中:“从自定义文件夹或 URL 加载首选项”。将文件夹指向`macos`–那里有一个名为`com.googlecode.iterm2.plist`的文件，包含所有设置。