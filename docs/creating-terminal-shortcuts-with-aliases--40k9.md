# 使用替身创建终端快捷方式

> 原文：<https://dev.to/cadellsinghh_25/creating-terminal-shortcuts-with-aliases--40k9>

如果你像我一样住在终端，为经常访问的目录创建快捷命令别名可以节省你很多时间。您在您的**中创建这些别名。bash_profile** 。让我告诉你怎么做。

对我来说，在我创建别名之前，访问包含我所有学校文档的目录是一件苦差事...这是它看起来的样子。

```
cd ~/Documents/Documents\ -\ Cadell’s\ MacBook\ Air/UTT\ year\ 1\ semester3 
```

Enter fullscreen mode Exit fullscreen mode

为了方便起见，我在 bash 概要中创建了一个别名。首先，我使用
打开它

```
open ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

现在用我的**。bash_profile** 文件打开后，我可以插入 alias 命令。

```
alias school="cd ~/Documents/Documents\ -\ Cadell’s\ MacBook\ Air/UTT\ year\ 1\ semester3/" 
```

Enter fullscreen mode Exit fullscreen mode

从这里，我需要做的就是保存文件并重新启动终端。现在，要访问我的学校文件，我需要做的就是像这样启动**学校**快捷方式:

```
cadellsingh$ school 
```

Enter fullscreen mode Exit fullscreen mode