# 使用 flatpak+vscode

> 原文：<https://dev.to/satimewallin/go--mess-with-flatpakvscode-ja6>

今天在我的 macbook pro 上安装了新的 fedora 28(是的，我讨厌 osx ),并决定通过 flatpak 安装 vscode(我也从 emacs 转到了 vs code)——我还没有尝试过。

现在，vscode 的基本安装是一行程序。简单地做`flatpak install flathub com.visualstudio.code`就大功告成了。当我安装 go 扩展时，问题出现了。

通常这也是一步到位的事情，但是这次我无法找到 go 二进制文件。我使用 fish 作为我的 shell，并开始怀疑它没有正确地找到环境变量(因为 vscode 启动了自己的 sh-shell)。所以，也许是不经意地，我开始设置我的路径，GOPATH 和 GOROOT。bash_profile 也是如此。这实际上产生了影响。我现在可以在 vscode 终端中看到正确的路径。奇怪的是，即使路径是正确的，当我试图从 vscode 内部运行它时，也找不到二进制文件。

我的调查还在继续。我很快意识到，是我对 flatpak 的缺乏了解让我犯了个错误。由于我的 GOROOT 指向`/usr/lib/golang`(是的，我通常使用 GVM，但今天不能被打扰)并且 flatpak 是沙箱化的，它不能找到 GOROOT 的内容。

因此，简单的解决方案是(如果可能不是最好的)

1)下载 Go linux 二进制文件

2)将它们拆包到`$HOME/go`

3)将此添加到`$HOME/.bash_profile`和

```
PATH=$HOME/src/go/bin:$HOME/go/bin:$PATH
export PATH
GOROOT=$HOME/go
export GOROOT
GOPATH=$HOME/src/go
export GOPATH 
```

Enter fullscreen mode Exit fullscreen mode

4)然后将其添加到我的 vscode settings.json

```
 "terminal.integrated.shellArgs.linux": ["-l"],
    "terminal.integrated.shell.linux": "/bin/bash",
    "go.gopath": "/home/dsw/src/go",
    "go.goroot": "/home/dsw/go",
    "go.inferGopath": true, 
```

Enter fullscreen mode Exit fullscreen mode

所以，叫我新手吧，不过已经解决了。我很乐意听到任何解决这个问题的替代方法或者类似的 flatpak 故事。

EOF！