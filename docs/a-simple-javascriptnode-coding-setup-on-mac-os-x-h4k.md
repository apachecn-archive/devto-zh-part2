# Mac OS X 上简单的 JavaScript/节点编码设置

> 原文：<https://dev.to/hugo__df/a-simple-javascriptnode-coding-setup-on-mac-os-x-h4k>

下面是我如何在 Mac OS 上提高 JavaScript/Node 的工作效率。

# 终端

## Setup iTerm2

*   安装 [iTerm2](https://www.iterm2.com/)

### 将编辑模式改为自然文本

*   iTerm Preferences → Profiles →选择您的配置文件→ Keys 选项卡→ Load Preset… →自然文本编辑(参见 [this StackOverflow answer](https://apple.stackexchange.com/a/218639)

[![](../Images/2184c4779d1cf23dd5dcff39714013ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_e0WVifH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4D85104EB4612E36D39F1EDC98FBA3CCC5F99A3320C73070E613DFAB4FC5809A_1542554401407_image.png)

### 新的会话应该从先前停止的地方开始

*   iTerm 首选项→配置文件→选择您的配置文件→常规选项卡→工作目录部分→重用以前会话的目录选项

[![](../Images/84914abe485e65487c7b01a43b0ab398.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uKLUvW8s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4D85104EB4612E36D39F1EDC98FBA3CCC5F99A3320C73070E613DFAB4FC5809A_1542554259914_image.png)

### 退出标签页关闭

*   iTerm 首选项→常规，“关闭”→“当所有窗口都关闭时退出”

[![](../Images/3d2e6c51bac11b4026f2143ee7ec59cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A9j9CrI9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4D85104EB4612E36D39F1EDC98FBA3CCC5F99A3320C73070E613DFAB4FC5809A_1542554318639_image.png)

### 增加字体大小

*   iTerm 首选项→配置文件→选择您的配置文件→文本选项卡→字体部分→更改字体→在弹出窗口中更新字体
*   16 磅摩纳哥的球迷(12，14 磅太小了)

[![](../Images/65b621eb3bbbded323a6895616933f15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iuIXBecI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4D85104EB4612E36D39F1EDC98FBA3CCC5F99A3320C73070E613DFAB4FC5809A_1542554456878_image.png)

## 外壳设置

### zsh

与 [oh-my-zsh](https://ohmyz.sh/) 经理。为您设置自动完成功能。

### 时髦的色彩主题

使用 [iterm-snazzy](https://github.com/samtgarson/iterm-snazzy) ，这是一个下载`.itermcolors`文件并从(iTerm Preferences→Profile>Colors>Colors Presets…)中选择主题的例子。

### 纯提示

它简单、干净，但给你足够的信息来提高工作效率。

*(有关如何启动和运行节点/npm，请参见设置和配置)*

*   使用 npm 安装:`npm install --global pure-prompt`
*   将以下内容添加到您的`.zshrc`中进行初始化:autoload-U promptinit；promptinitprompt 纯

# 浏览器和测试

作为开发人员，手边有一些浏览器和工具总是好的:

*   谷歌浏览器(Google Chrome):由于其坚实和广泛的开发工具，仍然是一个目标。通常我会安装 React 或 Vue dev 工具。
*   [Postman for Mac](https://www.getpostman.com/apps) :手动测试 API
*   火狐浏览器:排名第二的浏览器
*   Brave :自动屏蔽广告和跟踪，类似于“播放”浏览器，它的开发工具是 Chrome 开发工具的一个 bugger/less 人体工程学版本(这是因为 Brave 在引擎盖下使用了 Chrome)
*   safari——默认情况下安装在 Mac OSX 上，这是一个漏洞百出的浏览器，使用它进行测试很好，因为它出现了奇怪的 SVG 和 cookies 安全策略怪癖。因为这是默认设置，所以它也被非技术人员广泛使用。
    *   启用开发工具:Safari →首选项→高级→在菜单栏中显示开发菜单。

# 编辑

我使用 [Visual Studio 代码](https://code.visualstudio.com/)，它在现成可用和可定制之间取得了恰当的平衡。在我看来，像 vim 或 Atom 这样的编辑器需要一点配置才能工作，而像 Sublime 或 IDEs (WebStorm)这样的编辑器没有相同的插件生态系统。

## 安装 VSCode 命令行工具

使用 CMD + P 打开对话框。

用途:`Shell Command: Install 'code' command in PATH`

[![](../Images/419c7d5865148e80ed917ec3d485f0af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rvtC7-wB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4D85104EB4612E36D39F1EDC98FBA3CCC5F99A3320C73070E613DFAB4FC5809A_1542556492194_image.png)

VSCode 命令行工具使用示例:

*   `code .`:打开 VSCode 中的`.`目录
*   `code -r .`:用当前目录替换 VSCode 中打开的目录
*   `code -a .`:将当前目录添加到 VSCode，即。初始化工作区

## 必备扩展

*   Atom keymap:我不喜欢默认的键绑定，这个使用 Atom 风格的键绑定，[从 Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscode.atom-keybindings) 或者从`CMD + P`菜单`ext install atom-keybindings`获得
*   editor config for VS Code:“editor config 帮助开发人员在不同的编辑器和 ide 之间定义和维护一致的编码风格。”(见 editorconfig.org[)，即。帮助您处理标签大小，修剪空间等。在代码编辑器中，](https://editorconfig.org/)[从 Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig) 或者从`CMD + P`菜单`ext install EditorConfig`获得它

## 有扩展功能真好

*   ESLint:“将 ESLint JavaScript 集成到 VS 代码中。”，[从 Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 或从`CMD + P`菜单`ext install vscode-eslint`获取
*   npm Intellisense:“自动完成导入语句中的 npm 模块”，[从 Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=christian-kohler.npm-intellisense) 或从`CMD + P`菜单`ext install npm-intellisense`中获取
*   时髦的主题:相同的颜色主题(时髦的)，因为我已经为 VSCode 的终端设置，[从 Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=alexanderbast.vscode-snazzy) 或`ext install snazzy theme`获得
*   导入成本:“在编辑器中显示导入/要求包大小”，[从 Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost) 获取，或`ext install import-cost`

# 生产力

没有太多的生产力应用程序，只有 [Alfred](https://www.alfredapp.com/) 和 [Clipy](https://clipy-app.com/) ，前者是我用来做更好的 Spotlight 搜索的，后者是一个剪贴板管理器。对于剪辑，增加“内联项目”的数量(剪辑→首选项→菜单→内联项目数)。

[![](../Images/e9067c7c41e0d53ac9a8b71c9ece4dc3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vqxy4eXE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_4D85104EB4612E36D39F1EDC98FBA3CCC5F99A3320C73070E613DFAB4FC5809A_1542557479751_image.png)

# 工装

使用`xcode-select` `--` `install`更新 Xcode。

为包管理安装[自制软件](https://brew.sh/)(想想 Mac 的 apt 或 pkg):

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

安装 [Node.js](https://nodejs.org/en/) 或者从源代码，使用 Mac 安装程序或者使用自制软件:

```
brew install node 
```

安装`n`–使用 npm 交互管理您的 Node.js 版本(现在我们已经安装了 Node):`npm install -- global n`

使用`n` : `sudo n latest`切换到最新的节点版本

安装 jq(在终端中格式化并很好地处理 JSON ),并使用 Homebrew
观察(重复运行命令)

```
brew install jq watch 
```

添加一些 git 扩展:

*   [git-open](https://github.com/paulirish/git-open) :“键入`git open`在你的浏览器中打开一个存储库的 GitHub 页面或网站。”使用`npm install --global git-open`
*   [git-lg](https://coderwall.com/p/euwpig/a-better-git-log) :更简单/更漂亮`git log`:

```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit" 
```

# 设置和配置

添加以下最小的`.vimrc`，它支持语法突出显示，具有基本的 tab/tabsize 配置，并支持行号显示:

```
syntax enable
set tabstop=2
set shiftwidth=2
set expandtab
set number 
```

设置 SSH 密钥并添加到 VCS 主机，[参见 GitHub 帮助文章](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/):

*   生成新密钥:`ssh-keygen -t rsa -b 4096 -C "*your_email@example.com*"`
*   将您的公钥复制到剪贴板，这样您就可以将它粘贴到托管版本控制系统要求您粘贴的任何地方:`pbcopy < ~/.ssh/id_rsa.pub`

将百分比放在电量水平上，右键单击电池指示器并选择“显示百分比”。

# 杂项和附加项目

❤️ Spotify

## (可选)坞站、虚拟箱

Docker 是一种集装箱化技术，认为 VMs 更小。我推荐 [Docker for Mac](https://docs.docker.com/docker-for-mac/install/) 。VirtualBox 允许你在 Mac 上运行虚拟机，安装在 [VirtualBox 下载](https://docs.docker.com/docker-for-mac/install/)。

奥斯卡·伊尔迪兹