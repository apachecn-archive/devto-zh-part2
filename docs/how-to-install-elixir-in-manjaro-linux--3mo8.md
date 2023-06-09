# 如何在 Manjaro Linux 中安装 Elixir

> 原文：<https://dev.to/lobo_tuerto/how-to-install-elixir-in-manjaro-linux--3mo8>

* * *

你可以在 [lobotuerto 的笔记中查看这篇文章的最新更新版本——如何在 Manjaro Linux 中安装 Elixir。](https://lobotuerto.com/blog/how-to-install-elixir-in-manjaro-linux/)

* * *

# 安装过程

我非常喜欢我的开发语言的版本管理器。

我一直在用 [asdf](https://github.com/asdf-vm/asdf) 安装[二郎](https://www.erlang.org/)和[仙丹](https://elixir-lang.org/)带 **OTP** 支持。

开始吧！

克隆 **GitHub** 库:

```
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.4.2 
```

Enter fullscreen mode Exit fullscreen mode

向您的`.bashrc`文件添加一些行:

```
echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bashrc
echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

使对`.bashrc`的更改生效，或者关闭并打开您的终端:

```
exec $SHELL 
```

Enter fullscreen mode Exit fullscreen mode

最后安装**二郎**和**仙丹:**

```
asdf plugin-add erlang
asdf list-all erlang
asdf install erlang 20.2.4
asdf global erlang 20.2.4

asdf plugin-add elixir
asdf list-all elixir
asdf install elixir 1.6.2-otp-20
asdf global elixir 1.6.2-otp-20 
```

Enter fullscreen mode Exit fullscreen mode

要激活`iex` REPL 历史，请将其添加到您的`.bashrc` :

```
export ERL_AFLAGS="-kernel shell_history enabled" 
```

Enter fullscreen mode Exit fullscreen mode

如果你用的是这个[牛逼的编辑器](https://code.visualstudio.com/)(提示: **Visual Studio 代码**，你应该试试 [ElixirLS](https://github.com/JakeBecker/vscode-elixir-ls) 扩展。

# 链接

*   [Elixir School——关于 Elixir 编程语言的课程](https://elixirschool.com/en/)
*   [使用仙丹前我希望知道的十件事](https://ropig.com/blog/ten-things-wish-knew-using-elixir/)