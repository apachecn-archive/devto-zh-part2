# 在 mac 上创建新用户

> 原文：<https://dev.to/aurelkurtula/creating-new-users-on-a-mac--2n24>

就在几天前，我得到了一个新的翻新的 MacBook。虽然每当我们的电脑死机时，它们都会让我们落后一点，特别是如果我们没有备份系统的话，但现在我面前有了新的 MacBook，我有点高兴旧的坏了！

我现在又开始安装配置了，希望能多一点信心。

在我配置东西的时候，我会记下我做了什么和我是如何做的，我会在这里分享它们，以防有人需要它们，以防我有一台新的 MacBook。

当我拿到 MacBook 的时候，一个用户已经被创建了。用户名是商店的名称，所以我知道它必须改变。如果我让它保持原样，它会让我无休止地烦恼。

### 创建新用户的第一种方法

为了创建新用户，作为当前用户，您应该拥有管理员权限。

前往“系统偏好设置”(苹果->系统偏好设置)，选择“用户与群组”。

您需要“点按锁图标以进行更改”(“用户与群组”窗口的左下方)并添加您的密码。

现在，您可以通过单击锁上方的加号按钮并填写信息来添加新用户

[![](img/db1acb3dce40f1744cfb5eb16b838558.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nfLSQjVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wop6vnhkakk5t0bsr6t3.png)

最后只需确保用户拥有管理员权限；通过选择新用户并选择“允许用户管理这台计算机”。

[![](img/c5b30ca1c5417ae37eca36c700e37cf8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZqWPoTW6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xjvys6zge0tr71kxzg5g.png)

现在您需要做的就是注销您当前的用户并进入新创建的用户。

## 第二种方法

当我做上面的事情时，我进一步挖掘了设置，没有意识到我最终有两个标准用户，没有管理员。

如果您遇到这种情况，有一种方法可以创建另一个获得管理员权限的新用户。

您需要重新启动进入单用户模式。

重启你的 mac，当你听到启动完成的时候，按住`cmd+s`直到终端启动。您将看到自动生成的代码出现。键入以下代码

```
mount -uw / 
```

Enter fullscreen mode Exit fullscreen mode

然后按回车键

```
rm /var/db/.AppleSetupDone 
```

Enter fullscreen mode Exit fullscreen mode

然后按回车键

终于重启了

```
reboot 
```

Enter fullscreen mode Exit fullscreen mode

然后回车。

然后会要求您完成创建新用户的(GUI)设置过程。

它会自动成为管理员。

## 删除用户

删除用户非常容易。以管理员权限登录帐户，回到“用户和组”点击锁，获得编辑用户的能力。

选择要删除的用户，然后单击减号按钮。

显然，如果您要删除的用户的内容对您不重要，请选择“删除个人文件夹”单选按钮，然后单击“删除用户”。

然后再把所有东西锁回去。

## 总结性的

我意识到这些用户中的每一个都是独一无二的，相互独立的！

[![](img/13e47ebf4fe1a984130a5239da41b3d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WPezfcT8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qvin012163vq6uyk9x91.png)

有时候你意识不到一些事情，直到它击中你的脸，对不对？

大约一年前，我开始希望我有一台新的笔记本电脑来重新设置东西——为了学习的目的。

通过创建一个新用户，我将有机会重新设置，而不希望它会死机:)