# VSCode -基础知识

> 原文：<https://dev.to/equiman/my-essential-visual-studio-code-extensions-and-configurations-5197>

“你必须……”这是许多帖子和视频的主要标题，当谈到 Visual Studio 代码扩展时，这是为了获得更多的关注，但这个**编辑器**就像一只变色龙，可以随心所欲地改变他的环境，这就是为什么你需要创建**你自己的配方**。

这些事情很固执己见。我将分享**通用**的“我的配置”,我希望有人能从中挑选一些有用的东西。

建议先阅读 visual studio 代码上的[设置，以了解用户和工作区设置之间的差异。](https://code.visualstudio.com/docs/getstarted/settings)

# 多个设备

开始之前，强烈建议安装和设置[设置同步](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)扩展。它增加了在云中备份设置和扩展的能力，使用私有的 gist 来存储这些文件。

它同步所有扩展和包含以下内容的完整用户文件夹:

1.  设置文件
2.  按键绑定文件
3.  启动文件
4.  片段文件夹
5.  VSCode 扩展和扩展配置
6.  工作区文件夹

因此，您可以同步您的设备，不要在重置出厂设置或购买新设备时浪费时间。

# 字体

*   下载并安装 [Victor Mono](https://rubjo.github.io/victor-mono/) 字体

> Victor Mono 是一种开源的等宽字体，具有可选的半连接草体斜体和编程符号连字。

<figure>

```
{  "editor.fontFamily":  "'Victor Mono', Consolas, 'Courier New', monospace",  "editor.fontLigatures":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

# 分机

## 编辑

*   [书签](https://marketplace.visualstudio.com/items?itemName=alefragnani.Bookmarks)

<figure>

```
{  "bookmarks.saveBookmarksInProject":  true,  "bookmarks.navigateThroughAllFiles":  true,  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

*   [文档视图](https://marketplace.visualstudio.com/items?itemName=bierner.docs-view)

*   [文件嵌套更新器](https://marketplace.visualstudio.com/items?itemName=antfu.file-nesting)

<figure>

```
{  "fileNestingUpdater.autoUpdate":  true,  "fileNestingUpdater.autoUpdateInterval":  720,  "fileNestingUpdater.promptOnAutoUpdate":  true,  "fileNestingUpdater.upstreamRepo":  "antfu/vscode-file-nesting-config",  "fileNestingUpdater.upstreamBranch":  "main"  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

*   [filesize](https://marketplace.visualstudio.com/items?itemName=mkxml.vscode-filesize)

<figure>

```
{  "filesize.showGzipInStatusBar":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

*   [用于 Visual Studio 代码的小精灵跟踪器](https://marketplace.visualstudio.com/items?itemName=nhoizey.gremlins)

<figure>

```
{  "gremlins.showInProblemPane":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

*   [缩进-彩虹](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

*   [图片预览](https://marketplace.visualstudio.com/items?itemName=kisstkondoros.vscode-gutter-preview)

<figure>

```
{  "gutterpreview.sourceFolder":  "public"  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

*   [【SVG】t1㎡型](https://marketplace.visualstudio.com/items?itemName=jock.svg)

*   [待办事项树](https://marketplace.visualstudio.com/items?itemName=Gruntfuggly.todo-tree)

[![equiman](../Images/ebe835e1ec966206e00a01bb329a3e86.png)](/equiman) [## VSCode 扩展-待办事项树

### 卡米洛马丁内斯 9 月 22 分钟阅读

#vscode #productivity #webdev #markdown](/equiman/vscode-extension-todo-tree-1n02)

## 比较

*   [部分差异](https://marketplace.visualstudio.com/items?itemName=ryu1kn.partial-diff)

*   [L13 差异(目录)](https://marketplace.visualstudio.com/items?itemName=L13RARY.l13-diff)

## 套餐

*   [版本镜头](https://marketplace.visualstudio.com/items?itemName=pflannery.vscode-versionlens)

## 棉绒

*   [误差镜头](https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens)

*   [代码拼写检查器](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) + [西班牙语](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker-spanish)

<figure>

```
{  "cSpell.language":  "en,es"  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

*   [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)

<figure>

```
{  "[markdown]":  {  "editor.formatOnSave":  false,  "editor.quickSuggestions":  {  "comments":  "on",  "strings":  "on",  "other":  "on"  },  "editor.tabSize":  2,  "files.trimTrailingWhitespace":  false,  },  "markdownlint.config":  {  "MD024":  {  "siblings_only":  true  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

*   [Mintlify](https://marketplace.visualstudio.com/items?itemName=mintlify.document)

## 休息

*   [迅雷客户端](https://marketplace.visualstudio.com/items?itemName=rangav.vscode-thunder-client)

*   [粘贴 JSON 作为代码](https://marketplace.visualstudio.com/items?itemName=quicktype.quicktype)

*   [vscode-random](https://marketplace.visualstudio.com/items?itemName=jrebocho.vscode-random)

## 配对编程

*   [现场分享](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare)

## 主题

*   [素材图标主题](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)

<figure>

```
{  "workbench.iconTheme":  "material-icon-theme"  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

## 版本控制

*   [饭桶怪](https://marketplace.visualstudio.com/items?itemName=waderyan.gitblame)

*   [Git 图](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph)

*   git ignore

*   [GitHub 拉取请求](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github)

*   [远程存储库](https://marketplace.visualstudio.com/items?itemName=github.remotehub)

* * *

# 不需要

有很多扩展是没用的，可以用一些 VSCode 配置来代替，做同样的事情。

[![robole](../Images/a826046bf6ae131ce7ddd48b2a722e3c.png)](/robole) [## VS 代码:你不需要那个扩展

### rob OLeary Aug 5 ' 207 分钟读取

#webdev #vscode #productivity #tips](/robole/vs-code-you-don-t-need-that-extension-18d7)[![robole](../Images/a826046bf6ae131ce7ddd48b2a722e3c.png)](/robole) [## VS 代码:你不需要那个扩展第 2 部分

### rob ol early 11 月 8 日 2113 分钟阅读

#vscode #tooling #productivity](/robole/vs-code-you-dont-need-that-extension-part-2-34o3)

* * *

# 设置

在每个项目中将通用目的(用户设置)与特定目的(工作空间设置)结合起来。

<figure>

```
{  "breadcrumbs.enabled":  true,  "diffEditor.ignoreTrimWhitespace":  true,  "editor.autoIndent":  "full",  "editor.bracketPairColorization.enabled":  true,  "editor.guides.bracketPairs":  true,  "editor.codeActionsOnSave":  {  "source.organizeImports":  false  },  "editor.cursorBlinking":  "expand",  "editor.formatOnPaste":  false,  "editor.formatOnSave":  true,  "editor.formatOnType":  true,  "editor.letterSpacing":  1,  "editor.minimap.enabled":  false,  "editor.linkedEditing":  true,  "editor.renderWhitespace":  "boundary",  "editor.rulers":  [80,  120],  "editor.snippetSuggestions":  "top",  "editor.stickyScroll.enabled":  true,  "editor.suggest.preview":  true,  "editor.tabCompletion":  "on",  "editor.unicodeHighlight.ambiguousCharacters":  true,  "editor.unicodeHighlight.invisibleCharacters":  true,  "editor.wordWrap":  "off",  "explorer.autoReveal":  true,  "files.exclude":  {  "**/.git":  true,  "**/.DS_Store":  true  },  "files.trimTrailingWhitespace":  true,  "git.autofetch":  true,  "git.alwaysShowStagedChangesResourceGroup":  true,  "git.mergeEditor":  true,  "git.suggestSmartCommit":  false,  "merge-conflict.diffViewPosition":  "Below",  "search.exclude":  {  "**/build":  true,  "**/coverage":  true,  "**/dist":  true  },  "search.showLineNumbers":true,  "screencastMode.onlyKeyboardShortcuts":true,  "terminal.integrated.fontFamily":  "MesloLGS NF",  "terminal.integrated.fontSize":  12,  "terminal.integrated.shellIntegration.enabled":  true,  "terminal.integrated.defaultProfile.windows":  "Git Bash",  "terminal.integrated.defaultProfile.osx":  "iTerm.app",  "window.openWithoutArgumentsInNewWindow":  "on",  "window.menuBarVisibility":  "compact",  "window.commandCenter":  true,  "workbench.editor.highlightModifiedTabs":  true,  "workbench.editor.decorations.colors":  true,  "workbench.editor.decorations.badges":  true,  "workbench.settings.editor":  "json",  "workbench.startupEditor":  "none",  "workbench.tree.renderIndentGuides":  "none",  "[json][jsonc]":  {  "editor.defaultFormatter":  "vscode.json-language-features",  "editor.formatOnSave":  true,  "editor.tabSize":  4,  },  "[yaml]":  {  "editor.formatOnSave":  false,  "editor.tabSize":  2,  "files.trimTrailingWhitespace":  false  },  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

* * *

# 键盘快捷键

<figure>

```
[  //  Wrap  Emmet  {  "key":  "ctrl+shift+w",  //cmd+w  on  mac  "command":  "editor.emmet.action.wrapWithAbbreviation",  "when":  "editorHasSelection",  },  //  Comments  {  "key":  "ctrl+numpad_divide",  "command":  "editor.action.commentLine",  "when":  "editorTextFocus && !editorReadonly"  },  {  "key":  "ctrl+/",  "command":  "editor.action.commentLine",  "when":  "editorTextFocus && !editorReadonly"  },  ] 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>keybinding.json</figcaption>

</figure>

[![equiman](../Images/ebe835e1ec966206e00a01bb329a3e86.png)](/equiman) [## VSCode -不使用鼠标创建文件和文件夹

### 卡米洛马丁内斯 10 月 2 日 203 分钟阅读

#vscode #productivity #terminal #programming](/equiman/vscode-create-files-and-folders-on-the-go-2hd6)[![equiman](../Images/ebe835e1ec966206e00a01bb329a3e86.png)](/equiman) [## VSCode -不使用鼠标的终端

### 卡米洛马丁内斯 9 月 30 日 4 分钟阅读

#vscode #terminal #productivity #programming](/equiman/vscode-terminal-without-using-mouse-5bd0)

* * *

# 亲爱的圣诞老人

我只错过了这个了不起的编辑器的一件事，从一个环境改变到另一个环境的能力，并且没有加载所有的工具，即使他们不需要它们。

实际上，您可以在每个项目中手动禁用它，但是将来有能力在环境之间切换并关闭不需要的扩展会很好。

虽然 VSCode 团队解决了这个问题，但我们可以使用一种变通方法来创建有特色的版本: [React](https://dev.to/equiman/vscode-react-flavored-134h) ， [Node.js](https://dev.to/equiman/vscode-node-js-flavored-37di) ，[。网](https://dev.to/equiman/vscode-net-flavored-6e9) y [Arduino](https://dev.to/equiman/vscode-arduino-flavored-mgj) 。

[![dock-macos](../Images/035b5db00ea5b82642e18bb7f484fb74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JIb3s8F4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/ps4q9dzf8x5hpnqa8c6r.png)

# 一枚戒指统治他们所有人！

您可以让您的团队使用相同的设置，只需将其保存为`settings.json`文件，扩展名为`extensions.json`文件，两者都在`.vscode`文件夹中。

然后，当有人克隆/拉库时，会用工作区设置覆盖您的代码用户设置，并显示推荐的扩展。

# 特别提及

*   [电源模式](https://marketplace.visualstudio.com/items?itemName=hoovercj.vscode-power-mode)

<figure>

```
{  "powermode.combo.location":  "off",  "powermode.enabled":  true,  "powermode.explosions.duration":  300,  "powermode.explosions.size":  5,  "powermode.shake.enabled":  false,  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>settings.json</figcaption>

</figure>

*   [VSCode HackerTyper](https://marketplace.visualstudio.com/items?itemName=jevakallio.vscode-hacker-typer)

没有恐慌的现场演示？是...简单的日语！

[https://www.youtube.com/embed/ulnC-SDBDKE](https://www.youtube.com/embed/ulnC-SDBDKE)

# 奖金追踪:

强烈推荐访问[https://vscodecandothat.com/](https://vscodecandothat.com/)网站，这是学习有用代码技巧的绝佳资源。敬请关注本次[直播分享](https://visualstudio.microsoft.com/services/live-share/)延期。

进入菜单`Help > Interactive Playground`，你可以在一个简短的演示中尝试基本的编辑器特性。

* * *

**都是乡亲们！
快乐编码** 🖖

[![beer](../Images/192892baef71a32ea4a5e98a4927b05e.png)T2】](https://github.com/sponsors/deinsoftware)