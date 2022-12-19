# TIL:将 Visual Studio 代码与 Shell / CLI 集成

> 原文：<https://dev.to/jonasbn/til-integrate-visual-studio-code-with-shell--cli-2l1l>

到目前为止，我一直使用 **Sublime Text** 作为我的首选编辑器，在此之前是 **TextMate** ，在此之前是 **BBEdit** ，有时在它们之间是 **Atom** 、 **Komodo** 、 **Vim** 和 **vi** 。

我对 Visual Studio 代码非常非常感兴趣，我的大部分编程和文本编辑都是用这个编辑器完成的。

通常我从命令行启动**代码**，但是今天我决定一直使用指定的
作为编辑器，当使用`$EDITOR`或`$VISUAL`变量从其他命令行工具启动编辑时执行。

```
export EDITOR="code --wait --new-window" 
```

Enter fullscreen mode Exit fullscreen mode

这意味着例如可以使用`CTRL-x`、
、`CTRL-e`组合在**代码**中编辑复杂的命令行结构。

**code** 拥有出色的 **Git** 集成，但是如果你像我一样有时在命令行而不是编辑器上工作，设置`$GIT_EDITOR`环境变量会非常有用。

如果您已经如上所述设置了`$EDITOR`或`$VISUAL`(另请参见: [Git 手册页](https://git-scm.com/docs/git#git-codeGITEDITORcode))，这可能是不必要的，但是如果您有特殊的命令行设置并希望有所不同，这可能是一个选项，因此值得一提。

```
export GIT_EDITOR="code --wait --new-window" 
```

Enter fullscreen mode Exit fullscreen mode

现在，我可以使用**代码**和所有其他依赖于$EDITOR 集成的 CLI 来编辑提交消息和命令行。

## 参考文献

*   [Visual Studio 代码文档:命令行界面(CLI)](https://code.visualstudio.com/docs/editor/command-line)
*   [Git 手册页](https://git-scm.com/docs/git#git-codeGITEDITORcode)
*   [Bash 手册页](https://linux.die.net/man/1/bash)

从[我的 TIL 收藏](https://jonasbn.github.io/til/vscode/integrate_with_cli.html)中精选。