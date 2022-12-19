# git-askpass.exe:没有这样的文件或目录。可视化工作室

> 原文：<https://dev.to/rkosta/git-askpassexe-no-such-file-or-directory-visual-studio-11j>

当我试图用 Visual Studio 2017 同步到我的 Git BitBucket 存储库时，我收到了一个错误:

git-askpass.exe:没有这样的文件或目录。

为了解决这个问题，我必须:

1.  从控制面板中删除 Git>卸载
2.  从控制面板->卸载中删除 Git Windows 凭据管理器
3.  使用 Visual Studio 2017 安装程序，删除 Git for Windows
4.  Install Git
5.  安装 Git Windows 凭据管理器
6.  使用 Visual Studio 2017 安装程序，安装 Git for Windows

post [git-askpass.exe:没有这样的文件或目录。Visual Studio](https://blogit.create.pt/ricardocosta/2018/09/06/git-askpass-no-such-file-visual-studio/) 最早出现在[博客 IT](https://blogit.create.pt) 上。