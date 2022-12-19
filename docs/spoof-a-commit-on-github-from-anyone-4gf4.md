# 在 GitHub 上恶搞一个 commit。任何人。

> 原文：<https://dev.to/agrinman/spoof-a-commit-on-github-from-anyone-4gf4>

[![](../Images/4206aa78c4f0bd2b72d9854c269fee8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1fjH1Apo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/VFQqkEo.png)

你知道在 GitHub 上任何人都可以像你一样提交吗？如果你不相信我，就浏览这个库的[伪造提交](https://github.com/git-forge/fraudulent/commits)或[用我们的工具为自己伪造一个提交](https://spoof.krypt.co)。

## 自己试试:【spoof.krypt.co】T2

[![](../Images/f671cc1554af2be26fed0adf546a1a2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QCC3tiso--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2y6no7q9zave83ay12p.png)
[![](../Images/b2ee4445cb233b6c14921762fc8510cd.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--ClLonBt5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xj1k0213nn9nnujn3q7a.png)

## 它是如何工作的？

打开你的`~/.gitconfig`

```
[user]
    name  = Ben Bitdiddle
    email = bbitdiddle@mit.edu 
```

Enter fullscreen mode Exit fullscreen mode

将`name`和`email`更改为您想要的任何值。

如果`email`与另一个 GitHub 用户的电子邮件相匹配，该用户的照片将显示在提交旁边，当你点击
时，它将带你到他们的**真实** GitHub 个人资料。

下次你在 GitHub 上看到 Ben 提交的东西，不要相信是 Ben 写的。

## 怎么能证明我的提交是*真的*我的呢？

任何人都可以将 Git 提交的“作者”设置为任何值。为了证明你是提交人，你必须附上数字签名。
有人知道这真的是你提交的唯一方法是验证提交的签名。

## GitHub 支持验证&签名 Git 提交

看看这个签名的提交: [kryptco/kr@0cca333](https://github.com/kryptco/kr/commit/0cca333) 。

[![](../Images/548d7992f6719040fce5e2220ff61ef8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VJfoCdtt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/S7S3HW6.png)

如果提交没有绿色的“已验证”徽章，那么它可能是由任何人创作的！

GitHub 验证签名提交，而 Krypton 使签名提交变得容易。
获得您的绿色认证徽章，[https://krypt.co](https://krypt.co)。

# 让我们来看看一些众所周知的赝品...

“我爱 windows，也爱颠覆！”-[**@托瓦尔兹**](https://github.com/torvalds) 上 [#1eb0d8](https://github.com/git-forge/fraudulent/commit/1eb0d85b5017ced016cdb37897e58ab2c9e5d294)

"你真的应该用 gitlab.com，它要好得多."-[**@ schacon**](https://github.com/schacon)on[# 730 c7e](https://github.com/git-forge/fraudulent/commit/730c7eebc37c92115a5f886c7cafbb44489b2c97)

# 匿名伪造作案

在这里浏览社区中所有伪造的提交！[https://github.com/git-forge/fraudulent/commits/master](https://github.com/git-forge/fraudulent/commits/master)