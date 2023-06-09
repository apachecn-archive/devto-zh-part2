# 使用 gopass 存储团队密码

> 原文：<https://dev.to/sirech/storing-team-passwords-with-gopass-4c39>

当你在一个团队中工作时，你往往会有一堆需要共享的密码，比如你的应用程序需要启动的凭证。或者，您可能需要共享某项服务的帐户。

每个人都知道公开自己的密码不是一个好主意。然而，团队密码通常不会受到同样的礼遇。这些是我最近观察到的一些做法:

*   将密码存储在便利贴上，整个办公室的每个人都可以很容易地查看。
*   将密码直接写在源代码上。
*   通过未加密的电子邮件发送密码。

看到这里，我只能想起斯科特·亚当斯的这句话:

> 每个工程师的目标都是退休时不会因为重大灾难而受到指责。

泄露敏感信息是我个人不希望附在简历上的事情，所以我最近一直在鼓吹把它们存放在安全的地方。有一个工具我很喜欢，那就是 [gopass](https://github.com/justwatchcom/gopass) 。我想谈谈如何设置和使用它。

### 你到底在说什么？

引用项目的网站，`gopass`是对 Go 中 pass password manager 的改写。换句话说，它是一个可以被多人轻松使用的密码管理器。它对命令行非常友好，允许您将它包含在脚本中。非常容易安装以及与[酿造](https://brew.sh/)。我们也将安装 [GPG](https://www.gnupg.org/) ，因为这实际上提供了安全:

```
brew install gopass gpg2 
```

Enter fullscreen mode Exit fullscreen mode

## GPG 的钥匙还是皮塔饼

`gopass`本身相当好用，但是加密是用 *GPG* 完成的。每个和它打过交道的人都知道它一点都不友好。一个快捷方式是使用[键盘](https://keybase.io/)。这是一个让创建密钥变得非常容易的工具，正如这里的[所解释的](https://github.com/pstadler/keybase-gpg-github)。

为了让你的 *GPG* 和*键盘*在 OS X 正常运行，你需要遵循以下步骤:

*   安装*键座*:

```
brew cask install keybase 
```

Enter fullscreen mode Exit fullscreen mode

*   打开应用程序并创建一个帐户
*   通过 *CLI* 创建一个密钥:

```
keybase login
keybase pgp gen 
```

Enter fullscreen mode Exit fullscreen mode

系统会询问你是否要将密钥推送到 keybase.io。这样更方便，但你必须意识到这样会将你的密钥放在别人的服务器上。

当密钥导出到 GnuPG 钥匙串时，设置一个密码短语也是一个好主意。

*   默认情况下，您刚刚创建的密钥不受信任。你用`gpg`本身做这件事:

```
gpg --edit-key ${YOUR_EMAIL}
gpg> trust (ultimate level)
gpg> 5
gpg> save 
```

Enter fullscreen mode Exit fullscreen mode

为了让 *GPG* 正常工作，你需要运行这个:

```
export GPG_TTY=$(tty) 
```

Enter fullscreen mode Exit fullscreen mode

把它加到你的`~/.bashrc`里，就能保证你不用每次都手动了。

## 初始化新的密码存储

共享部分是通过一个`git`存储库实现的。每个开发人员都与它同步，以获取秘密。第一步是初始化`gopass`本身:

```
gopass init 
```

Enter fullscreen mode Exit fullscreen mode

它将询问一个我们刚刚创建的密钥。它应该是你为你的 *GPG* 键设置的密码短语。之后就可以为团队设置店铺:

```
gopass setup --remote ssh://git@bitbucket.internal.instance.org/your-team/password-store --alias team-store
gopass sync 
```

Enter fullscreen mode Exit fullscreen mode

## 添加/删除人员

一旦你设置好了所有的东西，你就可以把你的开发伙伴添加到库中了。对于每个开发者来说，你需要导入她的 *GPG* 密钥并信任它，否则当试图添加新的接收者
时会失败

```
curl https://keybase.io/${keybase_user}/pgp_keys.asc | gpg --import
# Trust
gpg --edit-key ${EMAIL_OF_THE_DEV}
gpg> lsign
gpg> trust (level 4)
gpg> save 
```

Enter fullscreen mode Exit fullscreen mode

之后，将某人添加到存储库中是小菜一碟。

```
gopass recipients add --store=team-store ${KEYID} 
```

Enter fullscreen mode Exit fullscreen mode

您的开发伙伴不需要创建新的存储库。相反，一旦密钥被添加到收件人列表中，他们就可以克隆它:

```
gopass clone ssh://git@bitbucket.internal.instance.org/your-team/password-store team-store 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果您没有先将它们添加到收件人列表中，克隆回购将导致错误。

## 从命令行使用密码

你知道最棒的是什么吗？任何 shell 脚本都可以很容易地与它集成。我工作中有一个运行测试的脚本，它每次运行时都会提示您输入两个键。我这样做了十次，然后我厌倦了，重写了脚本，直接从 *gopass* 中读取它们。

```
gopass show team-store/somewebsite/password 
```

Enter fullscreen mode Exit fullscreen mode

那是生活质量还是什么？请注意，如果您在一个`CI`管道上运行脚本，您可以预先注入变量，脚本将在本地和本地都工作，而不必在其他任何地方安装`gopass`，也不必更改脚本。

## 故障排除

倾向于给出晦涩难懂的错误信息。我构建了一个[验证脚本](https://github.com/sirech/gopass-verify)，帮助调试最常见的问题。您可以使用
运行它

```
git clone https://github.com/sirech/gopass-verify.git && cd gopass-verify && ./go verify 
```

Enter fullscreen mode Exit fullscreen mode

否则，您可以设置调试标志。

```
export GOPASS_DEBUG=true 
```

Enter fullscreen mode Exit fullscreen mode

之后运行任何一个`gopass`命令都会产生更多的输出，这些输出会把你引向正确的方向。

## 总结

总之，当这些密码可以安全地存储在像`gopass`这样的地方时，没有理由把它们到处放着。你知道谁有权限，当人们离开团队时可以拿走钥匙。当然，如果有人可以访问密钥，而现在不应该，这并不能解决旋转密钥的需要。

## 替代品

是众多处理密码的工具中的一个。重点不是使用具体的工具，而是要意识到将密码到处乱放会带来的巨大风险。以下是一些可行的替代方案:

*   [KeePassX](https://www.keepassx.org/) :释放一个开源。缺点是每个人只有一个主密码。
*   [1 团队密码](https://1password.com/teams/):我个人没用过，但有些同事用过。它不是免费的，而且密码最终会保存在云中。
*   [Sheesy](https://github.com/share-secrets-safely/cli) :工作同事写的。
*   [hashi corp](https://www.vaultproject.io/)的 Vault:引入了在线依赖，设置和操作相当复杂。`Vault`更好地为应用程序提供秘密。

### 修改版本

*   08/2019:新增*故障排除*章节。