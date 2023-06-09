# 如何在 macOS 上安装 Node.js 并创建本地开发环境

> 原文：<https://dev.to/digitalocean/how-to-install-nodejs-and-create-a-local-development-environment-on-macos-52bb>

### 简介

[Node.js](https://nodejs.org) 是一个开源的 JavaScript 运行时环境，用于轻松构建服务器端应用。它也是支持现代 JavaScript 框架的许多客户端开发工具的运行时。

在本教程中，您将使用 [Homebrew](http://brew.sh) 在本地 macOS 机器上设置 Node.js 编程环境，并通过编写一个简单的 Node.js 程序来测试您的环境。

## 先决条件

您需要一台运行 High Sierra 或更高版本的 macOS 电脑，并具有管理权限和互联网连接。

## 步骤 1 —使用 macOS 终端

您将使用命令行安装 Node.js 并运行与开发 Node.js 应用程序相关的各种命令。命令行是一种与计算机交互的非图形方式。你将不再用鼠标点击按钮，而是以文本形式输入命令并接收基于文本的反馈。命令行，也称为 shell，可以让您自动执行您每天在计算机上执行的许多任务，是软件开发人员的一个重要工具。

要访问命令行界面，您将使用 macOS 提供的终端应用程序。像任何其他应用程序一样，您可以通过进入 Finder，导航到“应用程序”文件夹，然后进入“实用工具”文件夹来找到它。在这里，双击终端应用程序将其打开。或者，您可以通过按住`COMMAND`键并按下`SPACE`来使用 Spotlight，通过在出现的框中键入来找到终端。

[![macOS Terminal](img/74ee4319633c9fcced7a06d56160fee2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OQxUE0Wu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.digitalocean.com/articles/eng_python/OSXSetUp/MacOSXSetUp.png)

如果您想更好地使用命令行，请看一下[对 Linux 终端](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal)的介绍。macOS 上的命令行界面非常相似，直接套用那个教程里的概念。

现在您已经运行了终端，让我们安装一些 Node.js 需要的先决条件。

## 步骤 2——安装 Xcode 的命令行工具

Xcode 是一个集成开发环境(IDE ),由用于 macOS 的软件开发工具组成。你不需要 Xcode 来编写 Node.js 程序，但是 Node.js 和它的一些组件将依赖 Xcode 的命令行工具包。

在终端执行该命令，下载并安装这些组件:

```
xcode-select --install 
```

Enter fullscreen mode Exit fullscreen mode

系统将提示您开始安装，然后再次提示您接受软件许可。然后工具会自动下载并安装。

我们现在准备安装软件包管理器 Homebrew，这将让我们安装最新版本的 Node.js。

## 步骤 3 —安装和设置家酿软件

虽然 macOS 上的命令行界面有很多您在 Linux 和其他 Unix 系统中可以找到的功能，但是它没有附带一个好的包管理器。一个*包管理器*是一个软件工具的集合，用于自动化软件安装、配置和升级。他们将安装的软件保存在一个中心位置，并可以用常用的格式维护系统上的所有软件包。[家酿](http://brew.sh)是一个免费的开源软件包管理系统，简化了 macOS 上的软件安装。我们将使用 Homebrew 安装 Node.js 的最新版本。

要安装 Homebrew，请在您的终端窗口中键入以下命令:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

该命令使用`curl`从 GitHub 上的 Homebrew 的 Git 存储库中下载 Homebrew 安装脚本。

让我们浏览一下与`curl`命令相关的标志:

*   - `f`或`--fail`标志告诉终端窗口在服务器出错时不输出 HTML 文档。
*   `-s`或`--silent`标志使`curl`静音，使其不显示进度表，结合`-S`或`--show-error`标志，它将确保`curl`在失败时显示错误信息。
*   `-L`或`--location`标志将告诉`curl`处理重定向。如果服务器报告请求的页面已经移动到不同的位置，它会自动使用新的位置再次执行请求。

一旦`curl`下载了脚本，它就会被 macOS 自带的 Ruby 解释器执行，从而开始自制软件的安装过程。

安装脚本将解释它将做什么，并提示您确认您想要这样做。这让你确切地知道家酿将对你的系统做什么，然后再让它继续。它还确保您在继续之前具备了先决条件。

在此过程中，系统会提示您输入密码。但是，当您键入密码时，您的按键不会显示在终端窗口中。这是一种安全措施，当命令行提示输入密码时，您会经常看到这种情况。即使你看不到它们，你的按键也会被系统记录下来，所以一旦你输入了密码，就按下`RETURN`键。

每当提示您确认安装时，按字母`y`表示“是”。

现在让我们验证家酿设置是否正确。执行此命令:

```
brew doctor 
```

Enter fullscreen mode Exit fullscreen mode

如果此时不需要更新，您将在您的终端上看到:

```
OutputYour system is ready to brew. 
```

Enter fullscreen mode Exit fullscreen mode

否则，您可能会得到一个警告，要求您运行另一个命令，如`brew update`，以确保您的 Homebrew 安装是最新的。

既然已经安装了 Homebrew，就可以安装 Node.js 了。

## 步骤 4 —安装 Node.js

安装了 Homebrew 后，您可以安装各种软件和开发工具。我们将使用它来安装 Node.js 及其依赖项。

您可以使用 Homebrew 来搜索您可以用`brew search`命令安装的所有东西，但是为了给我们提供一个更短的列表，让我们搜索与 Node.js:
相关的包

```
brew search nodejs 
```

Enter fullscreen mode Exit fullscreen mode

你会看到一个你可以安装的软件包列表，就像这样:

```
Output==> Formulae
node.js
nodejs 
```

Enter fullscreen mode Exit fullscreen mode

这两个包都在您的系统上安装 Node.js。它们都是存在的，以防你不记得是否需要使用`nodejs`或`node.js`。

执行这个命令来安装`nodejs`包:

```
brew install nodejs 
```

Enter fullscreen mode Exit fullscreen mode

您将在终端中看到类似如下的输出。家酿会安装很多依赖项，但最终会下载安装 Node.js 本身:

```
Output==> Installing dependencies for node: icu4c
==> Installing node dependency: icu4c

==> Installing node
==> Downloading https://homebrew.bintray.com/bottles/node-11.0.0.sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring node-11.0.0.sierra.bottle.tar.gz
...

==> Summary
🍺 /usr/local/Cellar/node/11.0.0: 3,936 files, 50.1MB 
```

Enter fullscreen mode Exit fullscreen mode

除了 Node.js 本身，Homebrew 还安装了一些相关的工具，包括`npm`，这使得安装和更新您可能在自己的项目中使用的 Node.js 库和包变得很容易。

要检查您安装的 Node.js 的版本，请键入

```
node -v 
```

Enter fullscreen mode Exit fullscreen mode

这将输出当前安装的 Node.js 的特定版本，默认情况下，这将是可用的 Node.js 的最新稳定版本。

```
Outputv11.0.0 
```

Enter fullscreen mode Exit fullscreen mode

用
检查`npm`的版本

```
npm -v 
```

Enter fullscreen mode Exit fullscreen mode

您将看到显示的版本:

```
Output6.4.1 
```

Enter fullscreen mode Exit fullscreen mode

您将使用`npm`来安装额外的组件、库和框架。

要更新你的 Node.js 版本，可以先更新 Homebrew 获取最新的包列表，然后再升级 Node.js 本身:

```
brew update

brew upgrade nodejs 
```

Enter fullscreen mode Exit fullscreen mode

现在 Node.js 已经安装好了，让我们编写一个程序来确保一切正常。

## 第五步——创建一个简单的程序

让我们创建一个简单的“Hello，World”程序。这将确保我们的环境正常工作，并让您轻松地创建和运行 Node.js 程序。

为此，使用`nano` :
创建一个名为`hello.js`的新文件

```
nano hello.js 
```

Enter fullscreen mode Exit fullscreen mode

在文件中键入以下代码:`hello.js`

```
let message = "Hello, World!";
console.log(message); 
```

Enter fullscreen mode Exit fullscreen mode

按`CTRL+X`退出编辑器。然后在提示保存文件时按`y`。你将返回到你的提示。

现在用下面的命令运行程序:

```
node hello.js 
```

Enter fullscreen mode Exit fullscreen mode

程序执行并在屏幕上显示其输出:

```
OutputHello, World! 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的程序证明您有一个工作的开发环境。您可以使用这个环境继续探索 Node.js 并构建更大、更有趣的项目。

## 结论

您已经成功安装了 Node.js，`npm`，并通过创建和运行一个简单的程序测试了您的设置。现在，您可以使用它来开发客户端应用程序或服务器端应用程序。查看以下教程，了解更多信息:

*   [如何使用 Vue.js 和 Axios 显示来自 API 的数据](https://www.digitalocean.com/community/tutorials/how-to-use-vue-js-and-axios-to-display-data-from-an-api)
*   [如何在 Ubuntu 18.04 上使用 Django 和 React 构建一个现代化的 Web 应用程序来管理客户信息](https://www.digitalocean.com/community/tutorials/how-to-build-a-modern-web-application-to-manage-customer-information-with-django-and-react-on-ubuntu-18-04)

* * *

[![CC 4.0 License](img/4662fd03838b335557c183fd57c6b02c.png)T2】](http://creativecommons.org/licenses/by-nc-sa/4.0/)

*本作品根据[知识共享署名-非商业性使用-类似共享 4.0 国际许可证](http://creativecommons.org/licenses/by-nc-sa/4.0/)* 进行许可