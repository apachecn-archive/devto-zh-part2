# 高级 git 技巧

> 原文：<https://dev.to/aravindballa/advanced-git-tips-b98>

Git 很神奇，你只有使用它才能学到东西。试着去学习命令，如果你不用它们，你会忘记的。GUI 也是如此(比如塔或者源码树)。您可能不需要学习命令，但是您会忘记过程。

解决方法很简单。了解你需要什么。谷歌你不知道的。试着回忆一下你是否经常用谷歌搜索同样的东西。😁

在使用 Git 超过 2 年并把它教给人们之后，我想分享一些技巧。

(我使用命令行，而不是 GUI。我觉得这样更舒服。)

# 别名

Git 允许您为命令使用别名，我们称之为长别名或常用别名。将会有一个`~/.gitconfig`文件，您可以在其中指定它们。(如果没有找到文件，我觉得可以在 HOME path 下创建)。

这些是我的化名。

```
[alias]
        pl = pull --rebase
        cm = commit -m 
```

Enter fullscreen mode Exit fullscreen mode

最常用的，你可以猜到是`git cm`。

# Rebase

这就引出了我的另一个别名`pull —rebase`。这在您处理开源项目或者项目中有很多分支时非常有用。因此，您创建了一个分支，添加了一个新特性，或者修复了一个 bug 并发出了一个拉请求。当你这样做的时候，你发现`master`已经被更新，并且有一堆提交不在你的分支中。

在这种情况下，rebase 允许您在 master 上重放您的工作，就像您现在已经创建了分支一样。这样处理合并冲突也更容易。如果有，你可以修复它们，然后`git merge —continue`继续`—abort`的 rebase 来停止它。我使用 VS 代码来解决冲突。这很容易。

# 多个 SSH 密钥

当你把工作和私人物品分开时，你会怎么做？理想情况下，你不应该。反正工作上的事都是隐私。所以只维护一个 Github 账号。但是想象你有两个。

我就这样过了一年。如果你频繁地转换账户，会很费时间和痛苦。宋承宪键拯救！✌🏻

您可以使用不同的邮件 id(`id_rsa`和`id_rsa_work`)生成多个 SSH 密钥，并将它们注册到 Github 帐户。当你访问 Github 时，你可以给遥控器定制 URL。举个例子，

```
git clone git@github.com-me:aravindballa/preact-ssr.git
git clone git@github.com-work:sencha/extjs-reactor.git 
```

Enter fullscreen mode Exit fullscreen mode

(P.S .是的，我为 Sencha 工作了很短一段时间，在他们在 ExtJS 6.6 中发布的[开源工具](http://docs.sencha.com/extjs/6.6.0/guides/getting_started/open_tooling.html)上。我不再为他们工作了。)

注意`github.com-me`和`github.com-work`。这些是我们在 ssh 配置中提供的主机名。

~/.ssh/config

```
Host github.com-me
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa

Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_work 
```

Enter fullscreen mode Exit fullscreen mode

你必须记住的一点是**作者**。

对于工作回复，您需要手动将 [user.email](http://user.email) 设置为工作邮箱。

```
git config user.email "aravind@awesome-company.com" 
```

Enter fullscreen mode Exit fullscreen mode

否则，你在工作中的行为将由你个人负责。

保重！