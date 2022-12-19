# 每周命令:用 fzf 模糊查找一切

> 原文：<https://dev.to/roperzh/weekly-command-fuzzy-finding-everything-with-fzf-3i0k>

## 基础知识

```
fzf [--options] 
```

`fzf`是一个交互式模糊查找器，它从 STDIN 中读取一个项目列表，并将选中的项目写入 STDOUT。

如果没有通过 STDIN 提供输入，默认情况下，`fzf`将使用 [`find`](http://man7.org/linux/man-pages/man1/find.1.html) 命令获取文件列表，不包括隐藏文件。

很好地融入了*nix 理念，这给了它很大的灵活性，包括 bash 和 zsh shells 的许多内置优点(要了解更多信息，请阅读[examples][#examples])。

该程序是可移植的，没有依赖性，可以按照项目自述文件中的[说明轻松安装。](https://github.com/junegunn/fzf#installation)

## 例子

无需进一步介绍，下面是一些`fzf`在行动中的例子。

### 光溜溜的

[![fzf-bare](../Images/bc1fac46c2d3313e0e039556c886f0c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--urbQBO0T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/39314537-d868a596-494a-11e8-9adf-aa6043912510.jpg)

如前所述，`fzf`可以单独调用，它将为当前目录和子目录中的项目提供模糊补全。

### 预览

[![fzf-preview](../Images/904b60eb7469fce0aa89e32d72039d7c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EOhByBK9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/39314540-d8d3e73e-494a-11e8-937e-85e8396b95f8.jpg)

```
$ fzf --preview="head -$LINES {}" 
```

此外，您可以提供一个`--preview`标志和一个在选择文件时执行的命令。在文件选择过程中，命令字符串接受由`fzf`替换的占位符:

*   `{}`当前行的单引号字符串。
*   `{+}`以空格分隔的所选行列表(如果没有选择，则为当前行),单独引用。
*   `{q}`当前查询字符串。

### 模糊补全为命令

[![fzf-asterisk](../Images/efc85ed829c9ba52a2324ccc030d4c84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wOw-9zok--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/39314536-d83dd348-494a-11e8-8b7c-0e4af0186114.jpg)

```
# Files under current directory
$ cat **<TAB>
# Processes
$ kill -9 <TAB>
# Host names
$ ssh **<TAB>
# Environment variables / Aliases
$ export **<TAB> 
```

bash 和 zsh 附带的一个很好的特性是不同命令的模糊补全，在大多数情况下，只需键入`**`并按下`↹` (TAB)就会给你一个模糊补全界面，为命令提供所选择的项目。

在 bash 上，模糊完成仅通过内置的 [`complete`](https://www.gnu.org/software/bash/manual/html_node/Programmable-Completion-Builtins.html#Programmable-Completion-Builtins) 为一组预定义的命令启用，但是您可以使用以下命令为其他命令启用它:

```
complete -F _fzf_path_completion -o default -o bashdefault <COMMAND_NAME> 
```

### 搜索历史

[![fzf-history](../Images/e71618e7d35b6ac5a87d87234af45406.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W1m00ZaN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/39314538-d898951c-494a-11e8-8032-22a84a449106.jpg)

默认情况下，bash 和 zsh 还内置了对反向增量历史搜索的支持。

## 进一步配置

您可以通过环境变量提供其他配置选项:

*   `FZF_COMPLETION_TRIGGER`帮助您定义自定义触发序列，而不是默认的`**`
*   `FZF_COMPLETION_OPTS`允许您定义和提供自定义默认标志
*   `FZF_DEFAULT_COMMAND`输入为 tty 时使用的默认命令

## 来源

*   [`fzf`回购](https://github.com/junegunn/fzf)
*   [vim 插件](https://github.com/junegunn/fzf.vim)