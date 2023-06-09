# 我的 Visual Studio 代码安装程序

> 原文：<https://dev.to/whoisryosuke/my-visual-studio-code-setup-igj>

最近，我不得不在另一台计算机上进行一些开发，在使用默认的 VSCode 配置安顿下来后，我很快意识到我的本地环境和工作流需要进行多少配置和安装。

### 备份您的 VSCode 设置⬇️

幸运的是，VSCode 使得备份设置和扩展变得相当容易。您的配置是一个 JSON 文件，这使得复制和粘贴变得非常容易。至于扩展，您只需安装一个 bash 脚本来备份一切。这将生成一个 Bash 命令列表，用于安装每个扩展:

```
code --list-extensions | xargs -L 1 echo code --install-extension 
```

Enter fullscreen mode Exit fullscreen mode

> *要在 Mac 上安装代码 Bash alias，进入命令面板，输入“install command”找到 shell 脚本。请确保将它安装在您的备份计算机和新计算机上。*

### 我的设置🎨

### JSON 配置🔧

这里没什么特别的。只有几个自定义的热键(我曾经使用 Sublime，所以我更喜欢这些快捷方式)。我修改了 Palenight 斜体主题，因为我不喜欢某些部分的对比度(比如侧边栏的颜色)。

```
{  "workbench.colorTheme":  "Palenight Italic",  "editor.fontFamily":  "Fira Code",  "editor.fontSize":  13,  "workbench.iconTheme":  "material-icon-theme",  "sublimeTextKeymap.promptV3Features":  true,  "editor.multiCursorModifier":  "ctrlCmd",  "editor.snippetSuggestions":  "top",  "editor.formatOnPaste":  true,  "window.zoomLevel":  0,  "workbench.colorCustomizations":  {  "[Palenight Italic]":  {  "sideBar.background":  "#222635",  "sideBarSectionHeader.background":  "#222635",  "tab.activeBackground":  "#222635",  "editor.background":  "#222635",  "editorHoverWidget.background":  "#222635",  "debugExceptionWidget.background":  "#222635",  "peekViewTitle.background":  "#222635",  "panel.background":  "#222635",  "statusBar.noFolderBackground":  "#222635",  "notifications.background":  "#222635",  "debugToolBar.background":  "#222635",  "tab.inactiveBackground":  "#2d3244",  "tab.inactiveForeground":  "#929ac9",  "sideBar.foreground":  "#7179a1",  }  },  "javascript.updateImportsOnFileMove.enabled":  "never",  "files.associations":  {  "\*.js":  "javascriptreact"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### [t1【扩展】](#extensions)

只是一些必备的。

*   项目管理器非常适合在你的电脑/硬盘上跟踪项目。
*   ES7 / ReactJS 片段是必须的。开始输入 r...获得预制的 React 组件结构(如功能容器或冗余容器)
*   PHP 智能感知(PHP Intellisense)—这是处理复杂应用程序的 PHP 开发人员的必备技能。将鼠标悬停在任何方法/函数上以查看其文档，并单击工具提示直接导航到该函数的源文件。
*   PHP 文档拦截器 —使 PHP 代码的文档化变得容易。键入/**以基于您的函数生成文档块。它会自动获取返回值之类的东西，并将其放入您的文档中(包括类型检查)。
*   **Todo 树** —如果你发现自己在代码中留下了// [@todo](https://dev.to/todo) 注释，这个扩展会扫描你的代码，找到所有注释并放入 Todo 列表。
*   Polacode——快速截屏代码的好方法，所有代码都以你的编辑器主题为风格。
*   **多行查找和替换** —曾经需要在不手动添加正则表达式的情况下复制多行代码吗？选择文本，打开命令面板，选择这个插件来复制一个有效的正则表达式搜索。这对于 VSCode 来说是必不可少的，因为默认情况下它不支持这一点。

```
code --install-extension alefragnani.project-manager
code --install-extension axlan.multiline-find-and-replace
code --install-extension cjhowe7.laravel-blade
code --install-extension dbaeumer.vscode-eslint
code --install-extension dsznajder.es7-react-js-snippets
code --install-extension felixfbecker.php-intellisense
code --install-extension Gruntfuggly.todo-tree
code --install-extension HookyQR.beautify
code --install-extension joelday.docthis
code --install-extension juanmnl.vscode-theme-hydra
code --install-extension kumar-harsh.graphql-for-vscode
code --install-extension leighlondon.eml
code --install-extension mikestead.dotenv
code --install-extension ms-vscode.sublime-keybindings
code --install-extension neilbrayfield.php-docblocker
code --install-extension neilding.language-liquid
code --install-extension octref.vetur
code --install-extension PKief.material-icon-theme
code --install-extension pnp.polacode
code --install-extension sdras.night-owl
code --install-extension whizkydee.material-palenight-theme
code --install-extension zhuangtongfa.Material-theme 
```

Enter fullscreen mode Exit fullscreen mode

### 你的设置是什么样的？💻

我总是对学习他人的工作流程感兴趣，也许是一些不太为人知的必备扩展。请在评论中告诉我你的 VSCode 设置是什么样子的！👍

Cheers 🍻

Ryo

*最初发表于*[*【whoisryosuke.com】*](http://whoisryosuke.com/blog/2018/my-visual-studio-code-setup/)*。*