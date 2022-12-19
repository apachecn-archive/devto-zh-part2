# 节省你的时间和精力的 Vim 技巧

> 原文：<https://dev.to/jovica/the-vim-trick-which-will-save-your-time-and-nerves-45pg>

无论你是 Vim 的初学者还是高级用户，我相信你至少见过一次:

```
E45: 'readonly' option is set (add ! to override) 
```

Enter fullscreen mode Exit fullscreen mode

通常是这样的:

您用 Vim 打开一个文件并进行一些更改。当您尝试保存文件并看到上面的消息时。

*然后*你意识到你没有和`sudo`一起跑 Vim？！啊，很讨厌是不是？

今天，你将学会如何简单地解决这个问题，一劳永逸。

很简单，你需要做的就是把这一行加到你的`.vimrc` :

```
cnoremap w!! execute 'silent! write !sudo tee % >/dev/null' <bar> edit! 
```

Enter fullscreen mode Exit fullscreen mode

当你再次遇到同样的情况，你不能保存你的文件，只需运行`:w!!`命令，你的更改将被保存！

现在，这是如何工作的:

*   `cnoremap` -告诉 Vim 在命令行模式下使用以下映射
*   `w!!` -映射(快捷方式)本身。
*   `execute '<command>'` -执行引号之间的命令
*   `silent!` -安静地运行它
*   `write !sudo tee % >/dev/null` -这个魔术需要另一个帖子来解释
*   `<bar> edit!` -这调用编辑命令来重新加载缓冲区，然后避免诸如“缓冲区已改变”的消息。

提示:您需要快速键入`:w!!`,这样它会扩展为完整的命令。慢慢打这四串，打`Enter`就不行了。

* * *

我在[快速掌握 Vim 简讯](https://masteringvim.com)上分享了这样的技巧。

我还写了一本书[快速掌握 Vim:立刻从 WTF 到 OMG](http://jovicailic.org/mastering-vim-quickly/)

您还可以通过关注 [@MasteringVim](https://twitter.com/MasteringVim) 获得最佳 Vim 提示