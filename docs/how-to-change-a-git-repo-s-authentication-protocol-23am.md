# 如何更改 Git Repo 的身份验证协议

> 原文：<https://dev.to/nickymarino/how-to-change-a-git-repo-s-authentication-protocol-23am>

*本文最初发表于[我的网站](https://nickymarino.com)。查看更多精彩内容！*

## HTTPS 向宋承宪按键

我经常需要更改 git 存储库，使用 SSH 密钥代替我的用户名和密码来验证远程服务器。为此，请在机器上的存储库文件夹中键入以下内容:

```
$ git config remote.origin.url git@github.com:username/repository_name.git 
```

Enter fullscreen mode Exit fullscreen mode

(确保在存储库名称的末尾包含`.git`。)

## HTTPS 的宋承宪键

要进行相反的更改，请键入:

```
$ git config remote.origin.url https://github.com/username/repository 
```

Enter fullscreen mode Exit fullscreen mode