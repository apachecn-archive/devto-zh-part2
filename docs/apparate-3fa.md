# 移形幻影

> 原文：<https://dev.to/sanketgautam/apparate-3fa>

[Apparate](//github.com/sanketgautam/Apparate) 是一个命令行工具，用于将 HackerRank 提交与 GitHub 同步。当我想在竞争性编程&开源项目/贡献之间保持平衡时，我想到了这个想法。我只是想用某种方式把我的在线状态集中在一个地方。我搜索了很多类似的现有服务或工具，但什么也没找到。

然后，我想试一试，给自己造一个。从 HackerRank 开始只是我个人的选择。无论如何，它可以在以后扩展到支持其他在线法官和平台。

以下是幻影显形者要做的几项任务:

*   它在 GitHub 上创建了一个提交库
*   它从 HackerRank 获取新的“已接受”的提交
*   它执行代码格式化(目前仅适用于 C++)
*   它将新提交推送到提交存储库
*   使用“crontab”计划它可以定期同步回购

## 用法

只需在终端上输入`apparate`就可以使用，如:

```
Usage: apparate [OPTIONS]

  Tool to Synchronize HackerRank Submissions with GitHub

Options:
  --repo TEXT Name of GitHub repository to store submissions
  --user TEXT Username of your HackerRank account
  --passwd TEXT Login Password of your HackerRank account
  --token TEXT GitHub Access Token with repository privileges
  --help Show this message and exit. 
```

Enter fullscreen mode Exit fullscreen mode

将`apparate`与`crontab`结合使用将自动定期更新提交库，并使我们的任务变得更加容易。建议将其部署在某个云实例或 AWS Lambda 上(为了更好的节省)。

## 使用的技术堆栈

Apparate 是用 Python(版本 3.6.5)开发的，因为它有利于快速开发，并且还支持 Selenium。

我使用以下 python 库进行开发:

*   Setuptools : Setuptools 是一个功能全面、积极维护的稳定库，旨在方便打包 Python 项目。这是用来使仪器容易安装，只需使用画中画。
*   **Selenium**(v 3 . 12 . 0):Selenium 是一个用于 web 应用程序的可移植软件测试框架。它用于从 HackerRank 获取新提交的内容。
*   **PyGitHub**(v 1.39):git Python 是一个用于与 git 库交互的 python 库。它基本上是 GitHub API 的包装器。它用于创建&维护用户的提交库。
*   **Click** (v6.7): Click 是一个用于创建漂亮命令行界面的 Python 包。

除此之外，我还使用了 Mozilla Firefox v59.0.2 和 Geckodriver v0.20.1 和 Selenium。它使用官方的 GitHub REST API v3。

## 项目结构

以下是 Apparate 的项目结构:

```
├── README.md
├── requirements.txt
├── scripts
│ ├── apparate.py
│ ├── config.py
│ ├── __init__.py
│ └── spider.py
└── setup.py 
```

Enter fullscreen mode Exit fullscreen mode

`README.md`包含仪器工具的安装&使用说明。

这里，`setup.py`是用打包设备的安装工具创建的，可以通过执行(设备/目录内)——
轻松安装

```
 pip install --editable . 
```

Enter fullscreen mode Exit fullscreen mode

`scripts/`目录包含主要代码文件:

*   包含获取新提交的代码，预验证代码等。
*   包含检查更新、更新报告等代码。
*   py——声明一些配置变量

## 举例

以下是设备运行示例的输出:

```
Executing Apparate on Mon, 14 May 2018, 11:06:00
GitHub Authentication Successful
Submissions Repo already exists
submissions size 0
Headless Firefox Initialized
HackerRank Login Successful
pagination params, 1 - 17
95 new submission(s) found.
Fetching code for new submissions..
- fetching code for submission 1\. Diagonal Difference
- fetching code for submission 2\. Birthday Cake Candles
- fetching code for submission 3\. Make It Anagram
- fetching code for submission 4\. Employee Names
- fetching code for submission 5\. Weather Observation Station 8
- fetching code for submission 6\. Points On a Line
   .
   .
   .
- updating repo with submission 91 
   -- created new file 
- updating repo with submission 92 
   -- created new file 
- updating repo with submission 93
   -- created new file 
- updating repo with submission 94 
   -- created new file 
- updating repo with submission 95 
   -- created new file 
submissions.txt updated successfully 
time taken to Apparate is 4 min(s), 17 sec(s) 
```

Enter fullscreen mode Exit fullscreen mode

## 它是如何工作的？

幻影显形的工作原理如下:

*   首先，它检查提交 repo 是否已经存在(如果不存在，那么创建一个)。
*   检查 submissions.txt(用于跟踪 repo 中的提交)是否已经存在(如果没有，那么用空的 submissions 数组创建它)。
*   然后，它执行登录到用户的 HackerRank 帐户
*   它获取所有不在 GitHub 库中的“已接受”提交的列表。
*   然后，它获取所有新提交的代码和格式代码(仅适用于 C++)。
*   然后，它在 GitHub Repo 中为每个新提交创建/更新一个文件，并提交它。
*   最后，它通过添加新的提交列表来更新 GitHub Repo 中的 submissions.txt。

## 结论&进一步计划

幻影显形对我和其他开发者来说是一个非常有用的工具。只是给自己做的，后来推广给大家。看到它帮助别人会给人巨大的快乐。我会通过修正错误、改进代码和维护来积极参与幻影显形。

此外，我想告诉大家，Apparte 是开放的贡献，并增加对新平台的支持，如 SPOJ，Codechef，Codeforces 等。