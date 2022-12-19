# 毫微；纤（10 的负九次方）

> 原文：<https://dev.to/ferreirarocha/nano-14l9>

nano 是一个因此而看重简便性和易用性的编辑器，因此可能经常被低估和利用不足。

下面是您可能不知道的 10 项功能，请查看。

备份已编辑的文件

举个例子

```
nano -B /etc/fstab 
```

Enter fullscreen mode Exit fullscreen mode

当我们更改任何文件时，此功能会自动保存备份副本，这非常有趣，因为我们正在 linux 上对配置文件进行一些修改

保存的文件显示~ front。

在特定行中打开文件

举个例子

```
nano +123  /etc/fstab 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，此命令看起来并不那么有用，但当我们希望最大限度地优化文档和整合流程时，它会改变主意。

多缓冲器

与大多数图形用户界面(GUI)应用程序一样，nano 具有在内存中处理多个文件的功能，这在我们侦听由大量配置文件组成的任务时非常重要。

举个例子

```
nano -F /etc/fstab 
```

Enter fullscreen mode Exit fullscreen mode

打开文件而不读取内容

有时我们需要打开文件而不需要像文件这样的任何内容

/etc/fstab with nano-n 选项，我们可以按如下所示随意执行此操作；

举个例子

```
nano -n /etc/fstab 
```

Enter fullscreen mode Exit fullscreen mode

读取模式

与-n 模式相反，nano 通过启用-v 选项启用文件的只读模式

举个例子

```
nano -v /etc/fstab 
```

Enter fullscreen mode Exit fullscreen mode

在文本前面显示行号

这是一项简单的功能，但在整合流程的各个阶段时，它会起到一定的作用。

```
nano -l /etc/fstab 
```

Enter fullscreen mode Exit fullscreen mode

平滑调整长直线

如果您喜欢在 CLI 模式下使用光标，并且知道可以在 nano 编辑器中执行此操作，则只需启用应用程序的-g 选项

```
ctrl + shit + 4 
```

Enter fullscreen mode Exit fullscreen mode

注释和断开当前行(或标记行)的链接

我们可以通过运行以下命令来注释和取消注释一定范围内的行(标记行或未标记行)

要选择行，请执行以下操作

```
Alt + A 
```

Enter fullscreen mode Exit fullscreen mode

注释和断开线连接的步骤

```
Alt + 3 
```

Enter fullscreen mode Exit fullscreen mode

转到右括号/括号/键

```
Alt + ] 
```

Enter fullscreen mode Exit fullscreen mode

当我们需要知道特定功能何时关闭时，此功能非常有用，该功能基于关闭亲属、小汽车和钥匙。

将另一个文件插入当前文件

```
Ctrl + R 
```

Enter fullscreen mode Exit fullscreen mode

通过激活 CRTL + R 或按 F5 键，我们可以在当前作业中插入一个文件，这在某些作业或内容已经编写完毕时很有趣，我们只剩下补充了。

结束了

此处显示的所有功能都可以通过打开 Nano 编辑器本身的帮助菜单来访问，尽管许多 nano 编辑器并不将此作为 linux 中的维护工具来认真对待，但恰恰相反。因为有了这种简单的第一眼看到的功能和一套诱人的功能，nano 就能在一个充满好的或更好的选择的世界中走来走去，留下自己的印记。

如果此解决方案对您有用，请在下面提供反馈。

我会留在这里直到有机会