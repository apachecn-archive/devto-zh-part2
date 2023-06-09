# 作为 Vim 编辑器的 VSCode

> 原文：<https://dev.to/kewah/vscode-as-a-vim-editor-e8o>

*照片由 [@goshua13](https://unsplash.com/@goshua13) 在[Unsplash](https://unsplash.com)T5 拍摄*

2013 年，我开始使用 [Vim 作为我的主要编辑](https://blog.kewah.com/2014/from-sublime-text-to-vim)，这个过程并非没有挫折，但总的来说，我真的很喜欢使用它。这有时会花费我一些时间，但是我发现从自动完成到代码导航，有一个设置对我来说非常有用。我最喜欢的是 [Vim motions](https://blog.kewah.com/2014/from-sublime-text-to-vim/#magic-motions) ，我不会用它来换别的东西。

> 那么，如果一切正常，你为什么开始使用 VSCode 呢？

我主要对 VSCode 的调试和重构特性感兴趣。我习惯了在浏览器的开发工具中调试 JavaScript 或 CSS，但我从未找到调试节点应用程序的合适位置。使用 VSCode，可以直接在编辑器中进行调试，添加断点或调查堆栈跟踪。

但是，和往常一样，当我开始使用新的代码编辑器安装 Vim 插件时，我做的第一件事就是。

## VSCode Vim 插件

总而言之， [VSCodeVim](https://github.com/VSCodeVim/Vim) 确实不错。这是迄今为止我在 Vim(或 NeoVim)之外尝试过的最好的 Vim 集成。它很快，包含了我爱用的[插件](https://github.com/VSCodeVim/Vim#-emulated-plugins)([easymotion](https://github.com/easymotion/vim-easymotion)、[潜行](https://github.com/justinmk/vim-sneak)、[环绕](https://github.com/tpope/vim-surround))，支持我以前用的所有动作和大部分快捷键。

为了达到这个目的，我不得不调整设置来重新映射命令，但是这个插件的伟大之处在于我们可以重新映射它的负载！

例如，我使用`jk`在插入和正常模式之间切换。

```
{
  "vim.insertModeKeyBindings": [
    {
      "before": ["j", "k"],
      "after": ["<Esc>"]
    }
  ]
} 
```

*你需要在用户设置中定义。(在命令面板中搜索`Preferences: Open User Settings.`(`⇧⌘P`)。)*

这非常简单，您定义您想要在`before`中使用什么映射，以及您想要在`after`中得到什么输出。

我在很大程度上依赖 EasyMotion 来移动(跳到不同的行或字符)，但我发现默认设置相当烦人:在 Vim 中，我有两次按 leader 键，而不是一次，然后是动作(例如`j`跳到下面的行)。但是，重新映射它是可能的！

```
{
  "vim.normalModeKeyBindingsNonRecursive": [
    {
      "before": ["<leader>", "j"],
      "after": ["<leader>", "<leader>", "j"]
    }
  ]
} 
```

您不仅可以重新映射按键绑定，还可以执行命令。例如，使用`ma` :
切换[书签](https://marketplace.visualstudio.com/items?itemName=alefragnani.Bookmarks)

```
{
  "vim.normalModeKeyBindingsNonRecursive": [
    {
      "before": ["m", "a"],
      "after": [],
      "commands": [
        {
          "command": "bookmarks.toggle",
          "args": []
        }
      ]
    }
  ]
} 
```

我已经能够重新映射绑定来匹配我的 Vim 记忆肌肉。当开始使用 VSCode 时，我感觉就像在家里一样。

你可以在这个[要点](https://gist.github.com/kewah/a5d8c1eff08db1623f98593727d99353)上找到我的 VSCodeVim 设置。

## 重构

VSCode 使得一些重构任务比我以前在 Vim 中做的更容易。我最喜欢的一个方法是通过按`f2`来重命名一个变量。项目中的所有事件都将被更新。当您重命名文件或将其移动到另一个文件夹时，也会发生同样的情况，其路径会在使用它的位置进行更新。

我们也可以使用`cmd+.` :
来转换带有隐式返回的箭头函数

```
const add = (a, b) => a + b; 
```

将光标放在`(`上，然后按下`cmd+.`->-`add braces to arrow function`。它会将其转换为:

```
const add = (a, b) => {
  return a + b;
}; 
```

当你需要调试一个函数的时候非常方便。
也可用于禁用当前行的 ESLint 规则。

总的来说，我发现在 VSCode 中进行重构更舒服，多游标比在 Vim 上工作得更好，并且我仍然可以在需要时使用可视块模式。

## 调试

到目前为止，我只使用了“附加到进程”调试模式。当使用`—inspect`标志运行 node(或 nodemon)时，可以在 VSCode 中附加进程来调试应用程序。

在`launch.json`中

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "attach",
      "name": "dev:server inspect",
      "port": 5858,
      "restart": true,
      "protocol": "inspector"
    }
  ]
} 
```

这是调试 Node.js 应用程序的一种极好的方式，所以你不需要打开一个 Chrome 开发工具来添加断点并在代码中放置`debugger`语句。

## 导航

经过一些调整后，Vim 对在 VSCode 中导航很熟悉:

*   使用模糊查找器à la [CtrlP](https://github.com/ctrlpvim/ctrlp.vim) 搜索文件。
*   当我认为没有必要记住一个键绑定时，使用命令面板来运行命令。
*   使用`ctrl + hjkl`在窗口间跳转。(在“我从 Vim 中错过了什么”一节中有更多关于它的信息。)
*   使用`gf`进入定义。(在 Vim 中我用 Ternjs 有这个特性，“60%的时候，每次都有效”。)
*   使用代码大纲通过`cmd+shift+o`在文件中的符号之间跳转。我喜欢这个功能。
*   使用`gt`(下一个标签)或`gT`(上一个标签)在标签之间切换。当我打开太多文件的时候,`ctrl + tab`就派上了用场，这种情况对我来说更常见，因为 VSCode 上的窗口管理支持很差。
*   使用`*`跳转到光标下的下一个变量。
*   使用`<leader>n`(下一次变更)或`<leader>p`(上一次变更)跳转到[下一次变更](https://gist.github.com/kewah/a5d8c1eff08db1623f98593727d99353#file-vimusersettings-js-L205-L223)。
*   当然，还有我最爱的 Vim 运动。

## 语言支持

VSCode 社区很活跃，所以我还没有遇到不支持的语言:JavaScript (+JSX)、TypeScript、Sass、Elm、Elixir/Phoenix 或 Haskell。

自动完成在大多数情况下是正确的，除了 CSS 或 Sass 文件，它们很难给出模板文件中定义的类名。它在自动完成代码片段方面也有一些问题。如果我打字太快，我将需要关闭并重新打开建议框，以便能够运行代码片段。

## 我从 Vim 上错过了什么

显然，一切都不是完美的。Vim 最让我怀念的是窗口管理，在 VSCode 中，目前的支持是非常基本的。

在 Vim 中，可以在同一个选项卡下打开几个文件，因此可以直接创建不同的空间，打开相关的文件和终端。例如，您可以打开一个包含与组件相关的所有文件的选项卡。

```
| TAB A | TAB B
|       |-------------------------------------------------------------
Module.js     | Module.test.js     | Terminal that runs
              |                    | Module.test.js
              |--------------------|
              | Module.style.scss  |
              |                    | 
```

在 VSCode 中，tab 都是独立的，所以即使我可以把屏幕一分为二，我也不能切换 tab 来同时改变两个文件。因此，我需要在文件之间来回跳转，以相应地切换选项卡。烦人。

```
| TAB Module.js | TAB Module.test.js | TAB Terminal
------------------------------------------------------------------------- 
```

当我试图在文件和终端之间快速跳转时，会产生一些挫败感，因为我需要记住相关文件在哪里，并手动保持同步。

## 接下来是什么？

我将继续使用 VSCode，并且不打算回到我以前的设置 iTerm + NeoVim。它拥有(几乎)我所寻找的一切:它很快，它有很好的语言，和自动完成支持，一个内置的终端，我仍然可以在其中使用 Vim。一件好事是我的大多数同事也使用它，所以我们可以在一个项目中共享设置或直接在 VSCode 中进行远程结对编程，这太棒了。