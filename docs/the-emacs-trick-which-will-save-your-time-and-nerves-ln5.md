# 节省你的时间和精力的 Emacs 技巧

> 原文：<https://dev.to/wammkd_64/the-emacs-trick-which-will-save-your-time-and-nerves-ln5>

好吧。对于那些经常阅读 dev.to 的人来说，可能已经很明显我的标题来自于[一篇特定的其他帖子](https://dev.to/jovica/the-vim-trick-which-will-save-your-time-and-nerves-45pg)(更不用说它被列为上周前 7 名帖子之一)。

但这让我想起了我在使用 Emacs 时经常遇到的一个问题(可能对于任何使用文本编辑器的人来说都是如此)，我最近找到了一个非常令人满意的解决方案。

对于那些没有读过这篇文章的人来说，您可能正在对一些文件进行维护；您可能会立即注意到，因为您会发现该文件是只读的，但是，即使您毫不犹豫地这样做，以便您可以编辑该文件，当您去保存时，您会看到

```
File apg.conf is write-protected; try to save anyway? (yes or no) 
```

Enter fullscreen mode Exit fullscreen mode

如果你对自己说，“是的！我当然愿意！”砰的一声喊出`yes`——你会听到

```
Doing chmod: Operation not permitted 
```

Enter fullscreen mode Exit fullscreen mode

你需要`sudo`但是谁想每次都记得这么做呢？

现在，至少在我寻找解决方案的时候，已经有了一个广泛的、即时的解决方案——它涉及到令人难以置信的 [Tramp](https://www.gnu.org/s/tramp/) 包，并在试图保存 Emacs 没有权限的文件时立即调用`sudo`。

最近，我发现，一旦保存，随后的保存会警告用户该文件最近被编辑过(来自您过去的保存)，您确定要保存吗？

不用说，这很快就会变得令人恼火。

## 输入第二个方案！

幸运的是，有一个非常简单的解决方案，它会在您关闭只读模式的一秒钟被调用，而不是一直等到您保存:一个单一的动作表明您希望对该文件负责，当您表示您想这样做时。

首先，为`read-only-mode`定义一些建议(代码来自[这个博客](http://emacsredux.com/blog/2013/04/21/edit-files-as-root/)并由我稍加修改):

```
(defadvice read-only-mode (after read-only-mode-sudo activate)
  "Find file as root if necessary."
  (when (and (buffer-file-name) (not (file-writable-p (buffer-file-name))))
    (let ((p (point)))
      (find-alternate-file (concat "/sudo:root@localhost:" buffer-file-name))
      (goto-char p)))) 
```

Enter fullscreen mode Exit fullscreen mode

这只是告诉`read-only-mode`当被激活时，检查缓冲区是否有名字，如果有，文件是否不能被写入。

如果是这样的话，抓住光标所在的点，打开一个新文件(实际上，是同一个文件，但是带有通过 Tramp 的`sudo`),然后转到我们保存的点，重新打开文件，光标会在文件的开头，否则。

很简单。

然而，我很快发现我遇到了一个新的问题——试图打开一个新文件时，假设我们应该继续使用`sudo` : *当然是*默认情况下，这不是我想对其他文件做的假设。

所以我创建了一个函数来代替`find-file`函数(同时使用它来避免过分背离标准行为):

```
(defun find-file-no-sudo ()
  (interactive)

  (find-file (read-file-name
               "Find file: "
               (if (string-match "^/sudo:root@localhost:" default-directory)
                   (substring default-directory 21)
                 default-directory)))) 
```

Enter fullscreen mode Exit fullscreen mode

基本上，如果一开始就有必要通过 Tramp 使用`sudo`的文本，那就删掉它！

现在，为了确保我们在使用旧函数的地方使用它…

```
(global-set-key (kbd "C-x C-f") 'find-file-no-sudo) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

将以上所有内容放入 init 文件中，就可以开始了。

下次你发现自己有一个你想编辑但没有用`sudo`打开的文件时，只要做`C-x C-q`，你就会被提示输入密码；然后随心所欲地编辑。