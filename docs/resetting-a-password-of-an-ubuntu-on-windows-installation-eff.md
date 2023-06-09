# 在 Windows 系统上重置 Ubuntu 的密码

> 原文：<https://dev.to/an3223/resetting-a-password-of-an-ubuntu-on-windows-installation-eff>

前阵子在 Windows 上安装了 Ubuntu，今天发现自己不记得用过的密码了。它发生了，考虑到这不是一个银行账户或核导弹，我想重置密码应该不会太难。好消息是，事实并非如此。

## 重置您的密码

1.  记下你在 Ubuntu 上的用户名。你以后会需要这个的。

2.  在 Windows (admin)命令行输入:
    `ubuntu config --default-user root`

3.  用`ubuntu`命令加载 Ubuntu。现在应该显示您是根用户。现在在 Ubuntu 内输入:
    `passwd <your ubuntu username>`

4.  系统会提示您更改密码。浏览提示符并设置你想要的密码，然后用`exit`命令退出 Ubuntu。

恭喜，你成功了。现在，您可能希望将您的用户重新设置为默认用户。只需在 Windows 命令行中运行此命令:
`ubuntu config --default-user <your ubuntu username>`