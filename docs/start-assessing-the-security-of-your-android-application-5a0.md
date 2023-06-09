# 开始评估您的 Android 应用程序的安全性

> 原文：<https://dev.to/shostarsson/start-assessing-the-security-of-your-android-application-5a0>

让我们假设你已经阅读了之前关于 android 安全性的文章，并且你已经准备好了一个可调试的应用程序，并安装在你的 Android 手机上。

## 文章不再可用

根据描述，你看了一下 [OWASP](https://www.owasp.org/index.php/Main_Page) ，你理解了软件中最“经典”的缺陷。

首先，重要的是要明白，所有这些缺陷都可以很容易地通过适当的审查过程来防止，并让软件开发人员意识到这一点，并向他们解释什么是安全的编码实践。
此外，非常重要的一点是，在开发软件服务的过程中，每个人都要意识到这一点，理解安全编码的价值(就节省的资金而言)，并允许开发人员花更多的时间来开发一个功能。但对未来会非常有利。

这个视频是一个很好的例子，展示了花时间为未来做正确和可持续的事情。

[https://www.youtube.com/embed/_NzrTb9SNdA](https://www.youtube.com/embed/_NzrTb9SNdA)

安全性作为创建软件过程的一部分，是其中的一部分。

* * *

让我们重点关注 Play Store 上的 Android 应用程序的安全评估(如果您的安全必须是软件创建过程的一部分，则为事件)，特别是所有的 [OWASP Mobile Top 10](https://www.owasp.org/index.php/Mobile_Top_10_2016-Top_10) 静态缺陷，如不安全的日志记录、不安全的存储、逆向工程或不充分的加密。

为了解释这一点，我将使用一个名为 **InsecureBank** 的故意易受攻击的 android 应用程序。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ dineshshetty ](https://github.com/dineshshetty) / [安卓-安全银行 v2](https://github.com/dineshshetty/Android-InsecureBankv2)

### 易受攻击的 Android 应用程序，供开发人员和安全爱好者了解 Android 的不安全性

<article class="markdown-body entry-content container-lg" itemprop="text">

# InsecureBankv2 自述文件

[![Black Hat Arsenal](img/222c276bce642dff7f41fe40b1e8e013.png)T2】](https://www.blackhat.com/eu-15/arsenal.html/)

[![Black Hat Arsenal](img/80161ce8c904d27550c184ee19924306.png)T2】](https://www.blackhat.com/us-16/arsenal.html/)

这是对我以前的一个项目——“不安全银行”的重大更新。这个易受攻击的 Android 应用程序被命名为“InsecureBankv2”，旨在让安全爱好者和开发人员通过测试这个易受攻击的应用程序来了解 Android 的不安全性。它的后端服务器组件是用 python 写的。它与 Python2 兼容。客户端组件(即 Android InsecureBank.apk)可以与源代码一起下载。此版本中当前包含的漏洞列表包括:

*   有缺陷的广播接收机
*   意图嗅探和注入
*   弱授权机制
*   本地加密问题
*   易受攻击的活动组件
*   根检测和绕过
*   仿真器检测和旁路
*   不安全的内容提供商访问
*   不安全的 Webview 实现
*   弱加密实现
*   应用程序修补
*   内存中的敏感信息
*   不安全的日志机制
*   Android 粘贴板漏洞
*   应用程序可调试
*   Android 键盘缓存问题
*   Android 备份漏洞
*   运行时操作
*   不安全的 SD 卡存储
*   不安全的 HTTP 连接
*   参数操作
*   核心秘密
*   用户名枚举问题
*   开发者…

</article>

[View on GitHub](https://github.com/dineshshetty/Android-InsecureBankv2)

* * *

如果你想在其他应用上做进一步的安全测试，你可以看看 Github 仓库，里面有很多 android 故意安装的易受攻击的 Android 应用

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ xtiankisutsa ](https://github.com/xtiankisutsa) / [牛逼-手机-CTF](https://github.com/xtiankisutsa/awesome-mobile-CTF)

### 这是一个基于移动的 CTF、报道和易受攻击的应用程序的精选列表。由于平台的流行，大多数都是基于 android 的。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 真棒-手机-CTF

这是一个基于移动的 CTF、报道和易受攻击的移动应用的精选列表。由于平台的流行，大多数都是基于 android 的。

灵感来源于 [android-security-awesome](https://github.com/ashishb/android-security-awesome) 、[OS x-and-IOs-security-awesome](https://github.com/ashishb/osx-and-ios-security-awesome)以及 [@github](https://github.com/search?utf8=%E2%9C%93&q=awesome+security&type=Repositories&ref=searchresults) 上所有其他令人敬畏的安全列表。

## 移动 CTF 挑战

*   [克格勃信使](https://github.com/tlamb96/kgb_messenger)
*   [ASIS·CTF—沙瑞尔漫游](https://medium.com/bugbountywriteup/asis-ctf-sharel-walkthrough-da32f3533b40?)
*   [安卓倒车挑战](https://github.com/kiyadesu/android-reversing-challenges)
*   [IOT CTF 的安卓应用](https://github.com/atekippe/SecDSM_April_2019_IOT_CTF_Android_APP)
*   【2019 年赛博卡车挑战赛(美国底特律)
*   [俄罗斯套娃式的安卓倒车挑战](https://github.com/o-o-overflow/dc2019q-vitor-public)
*   网络赛车挑战赛 19
*   [你不能通过 2019 年堪培拉奥运会](https://gitlab.com/cybears/fall-of-cybeartron/tree/master/challenges/rev/youshallnotpass)
*   [手机挑战赛合集](https://drive.google.com/folderview?id=0B7rtSe_PH_fTWDQ0RC1DeWVoVUE&usp=sharing)
*   [bside SF 2018 CTF](https://github.com/antojoseph/androidCTF)
*   [h1-702-2018-CTF-吴](https://github.com/luc10/h1-702-2018-ctf-wu)
*   [THC CTF 2018 -反转-安卓连载](https://github.com/ToulouseHackingConvention/bestpig-reverse-android-serial)
*   [安卓破解我挑战](https://github.com/reoky/android-crackme-challenge)
*   [OWASP 破解我](https://github.com/OWASP/owasp-mstg/tree/master/Crackmes)
*   [Rednaga 挑战赛](https://github.com/rednaga/training/tree/master/DEFCON23/challenges)
*   [iOS CTF](https://www.ivrodriguez.com/mobile-ctf)
*   [安卓黑客事件 2017: AES-Decrypt](https://team-sik.org/wp-content/uploads/2017/06/AES-Decrypt.apk_.zip)
*   【2017 年安卓黑客事件:令牌生成器
*   [安卓黑客事件 2017: Flag-Validator](https://team-sik.org/wp-content/uploads/2017/06/FlagValidator.apk_.zip)
*   [安卓黑客事件 2017:你可以躲——但不能跑](https://team-sik.org/wp-content/uploads/2017/06/YouCanHideButYouCannotRun.apk_.zip)
*   [安卓黑客事件 2017:我为什么要付费？](https://team-sik.org/wp-content/uploads/2017/06/WhyShouldIPay.apk_.zip)
*   【2017 年安卓黑客事件:深奥
*   [安卓黑客事件 2016: StrangeCalculator](https://team-sik.org/wp-content/uploads/2016/06/strangecalculator.apk_.zip)
*   …

</article>

[View on GitHub](https://github.com/xtiankisutsa/awesome-mobile-CTF)

* * *

1.  克隆安全库 Github 库:

```
git clone https://github.com/dineshshetty/Android-InsecureBankv2 
```

Enter fullscreen mode Exit fullscreen mode

1.  将 InsecureBankv2.apk 安装到您的 Android 手机上

```
adb install InsecureBankv2.apk 
```

Enter fullscreen mode Exit fullscreen mode

1.  开始使用应用程序来了解它是如何工作的。

    1.  确保 androlib 服务器正在运行
    2.  使用凭据 dinesh/Dinesh@123$或 jack/Jack@123$并开始使用该应用程序

* * *

现在，您已经快速了解了应用程序的关键特性以及应用程序的工作方式，我们可以将重点放在测试应用程序的静态漏洞上了。

静态漏洞可以很容易地工业化，并添加到您的开发过程中，因为它是在应用程序上完成的，不需要用户采取行动。

稍后，我将讨论如何在 Android 应用程序上进行工业化安全测试。

* * *

#### 安卓备份漏洞

Android 应用程序的数据可以备份，以备将来使用，例如，如果你要重新安装一个应用程序，你想拿回你的数据。

有一个简单的 adb 命令可以从应用程序中获取数据，并将其存储在您的计算机上。

```
adb backup 
```

Enter fullscreen mode Exit fullscreen mode

它将备份存储为一个。ab”文件。你可以用一些工具或者一个 shell 命令来解压这种文件。

##### 1。工具

我的首选工具是 [Android Backup Extractor](https://sourceforge.net/projects/adbextractor/) ，这是一款适用于 Windows、Linux 和 Mac OS 的免费 Java 工具。

```
# Usage
java -jar abe.jar [-debug] [-useenv=yourenv] unpack <backup.ab> <backup.tar> [password] 
```

Enter fullscreen mode Exit fullscreen mode

示例:

比如说，你有一个文件“ *data.ab* ”，它没有密码保护，你使用的是 Windows，你想要得到结果。焦油档案将被称为 data.tar*。那么你的命令应该是:* 

```
java.exe -jar abe.jar unpack data.ab data.tar "" 
```

Enter fullscreen mode Exit fullscreen mode

##### 2。命令

如果不想安装新工具，可以使用这些 bash 命令解压 Android 备份。

```
dd if=data.ab bs=1 skip=24 | openssl zlib -d | tar -xvf - 
```

Enter fullscreen mode Exit fullscreen mode

如果 zlib 有问题，请尝试下面的命令:

```
dd if=data.ab bs=1 skip=24 | python -c "import zlib,sys;sys.stdout.write(zlib.decompress(sys.stdin.read()))" | tar -xvf - 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 不安全存储

当您使用 Android 应用程序时，开发人员可能希望在设备上存储一些数据以进行持久存储。这可能是你在应用程序中的偏好或与应用程序相关的东西。它以各种方式出现。

因为您已经安装了一个可调试的应用程序，所以查看应用程序存储是非常容易的。

```
adb shell

run-as package-name 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过目录树中的“cd”和“ls”来查看文件，通过“更多”来查看文件内容。如果有相关的东西，你就能找到。
但这可能需要相当长的时间。

所以我使用一个工具来从一个应用程序中获取所有文件，然后在我的电脑上舒适地查看它们。

这个工具很笨。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [像素度](https://github.com/Pixplicity) / [矮胖子机器人](https://github.com/Pixplicity/humpty-dumpty-android)

### Android 的简单文件转储实用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# 一个安卓文件转储器

一个简单的 Android 文件转储工具。

请注意，这只适用于可调试的应用程序。

用法:

```
humpty.sh [--list-files <package-name>] [--dump <package-name> <db-file>] [--all <package-name>] [...] 
```

例如，要查询应用程序的所有文件:

```
$ humpty.sh -l com.pixplicity.example

Listing of /data/data/com.pixplicity.example/:

    /data/data/com.pixplicity.example:
    cache
    databases

    /data/data/com.pixplicity.example/cache:
    com.android.opengl.shaders_cache

    /data/data/com.pixplicity.example/databases:
    example.db
    example.db-journal 
```

要转储数据库`example.db`:

```
$ humpty.sh -d com.pixplicity.example databases/example.db

Dumping com.pixplicity.example/databases/example.db to dumps/com.pixplicity.example/databases/example.db...
Success! 
```

要转储应用程序的所有文件:

```
$ humpty.sh -a com.pixplicity.example

Dumping com.pixplicity.example/databases/example.db to dumps/com.pixplicity.example/databases/example.db...
Success!
...
... 
```

所有数据库转储都位于子目录`dumps`中。

## 许可证

根据 Apache 许可证获得许可。

</article>

[View on GitHub](https://github.com/Pixplicity/humpty-dumpty-android)

只需下载“humpty.sh”并使用适当的参数运行它，就可以从 Android 应用程序中检索所有文件。

```
humpty.sh -a com.android.insecurebankv2 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经有了应用程序存储在设备上的文件，你必须寻找一些可能包含一些有趣信息的文件名或者文件中的字符串。

我使用 bash 命令来做这件事。您可以广泛使用`find`和`grep`来执行这些研究。
当你学会如何正确使用这些命令时，它们会发挥巨大的作用。

```
man find 
```

Enter fullscreen mode Exit fullscreen mode

```
man grep 
```

Enter fullscreen mode Exit fullscreen mode

当然，你也应该看看你从我们刚才检索的 Android 应用程序备份中检索到的文件。

* * *

#### 硬编码的秘密

之前，我认为您已经完成了要评估的应用程序的设置，以便检索应用程序的源代码。

## 文章不再可用

尽管如此，使用 InsecureBankv2，您已经拥有了[代码库](https://github.com/dineshshetty/Android-InsecureBankv2/tree/master/InsecureBankv2/app)。

就像我们在“*不安全存储*评估期间所做的一样，我们将在代码库中寻找应该感兴趣的特定字符串。

网上有很多安全评估字符串的列表。只需选择一个与您相关的，并用 bash 脚本自动完成。

让我们对字符串的一个非常具体的子集这样做。

1.  **凭证**通常存储在名为“ **pass** ”、“ **passwd** ”或“ **password** ”的变量中。
2.  **秘密**通常存储在名为“**密钥**或“**秘密**的变量中
3.  **Admin** 或 **development** 配置经常存储在名为“ **admin** ”、“ **adm** ”或“ **dev** ”的变量中。

有了所有这些值，我们就有了寻找一些硬编码秘密的良好起点。
让我们通过一个简单的`grep`命令为上面确定的“*管理员*做这件事。

```
grep -i -r "admin" decompiledPackage/ 
```

Enter fullscreen mode Exit fullscreen mode

如果“ *decompiledPackage/* ”文件夹下的每个文件中有字符串“ *admin* ”，该命令将递归查找其中的每个文件(“`-r`”)，而不考虑大小写(“【T1”)。

它将显示在您的终端 stdout 上，并且会很快变得非常大。因此，通常的做法是将结果放在一个文件中。

这非常简单，因为 bash 本身就提供了这种功能。

```
grep -i -r "admin" decompiledPackage/ > grep-admin.txt 
```

Enter fullscreen mode Exit fullscreen mode

结果将保存在一个" *grep-admin.txt* "文件中。

> 你注意到你的档案里有什么有趣的东西吗？你能进一步利用它吗？

欢迎在评论里告诉我你发现了什么。

* * *

#### 数据库不安全

数据通常存储在数据库中，对于非常复杂的系统来说是这样，对于移动应用程序来说也是这样。

在不安全存储步骤中检索的文件中，您检索了一些数据库。你可以通过它们来识别它们”。db”扩展名。

要读取 Sqlite 数据库，可以使用终端。

假设您有一个 data.db 文件。要打开数据库，只需输入以下命令:

```
sqlite3 data.db
sqlite>.help 
```

Enter fullscreen mode Exit fullscreen mode

然后使用 help 命令，您可以列出表格、读取数据等...
你在 [Sqlite3 网站](https://sqlite.org/cli.html)上有关于 Sqlite3 命令行界面的所有信息。

但是，和往常一样，有一个工具可以用图形化的方式打开和读取数据库。
是 SQLite 的 [DB 浏览器。
DB Browser for SQLite 是一款高质量、可视化的开源工具，用于创建、设计和编辑与 SQLite 兼容的数据库文件。](http://sqlitebrowser.org/) [![DB Browser for SQLite](img/41539c6ce720811931090cdbb2f5d535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iT4KRjzi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/sqlitebrowser/sqlitebrowser/mastimg/sqlitebrowser.png)

对 mydb 文件使用 DB Browser SQLite。现在您可以看到数据库的表格和值。

[![InsecureBankv2-DBBrowser.png](img/e538342e5fbe008b9eae4db4d9e85313.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gNZ2b1Un--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AZOS3sC9K0gBYpHsdyE82sw.png)

如果您查看“names”表，可以看到什么叫做“用户枚举”。我可以看到连接到应用程序的用户的登录信息。
[![InsecureBankv2-UserEnumeration.png](img/04d9ab7f2e3e57444b7270ee28465c2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gFP9TiXC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AKw08UmlHD8L0NjJ5ByvXXw.png)

> 但这并不重要！！！我的意思是，每个人都知道我的登录，但他们不知道我的密码，所以这不是那种问题。

确实如此。如果你对登录和密码管理有所谓的数字理性。不幸的是，目前这种情况非常罕见。

让我们首先考虑你的密码。你认为它真的足够强大吗？

对照那种服务检查一下。
[我的密码有多安全？](https://howsecureismypassword.net/)

例如，如果我们检查 2017 年最常见的密码(从那个[列表](https://interestingengineering.com/the-25-most-common-passwords-of-2017))，它是“123456”(确实很常见)。
可以通过一种叫做[蛮力](https://en.wikipedia.org/wiki/Brute-force_attack)的方法瞬间破解。

这叫做密码的复杂性。为了抵御暴力攻击，您的密码必须很复杂。

尽管如此，即使您的密码非常复杂。

> 比如这个:LNA0'^F<0]s|

如果你使用密码的服务被泄露(比如最近雅虎或 LinkedIn 有大量密码泄露)，那么密码就不再安全。
因为它可以用于我们所说的字典攻击或者[彩虹表攻击](https://en.wikipedia.org/wiki/Rainbow_table)。

你可以在一个非常有名的服务上测试你的账户，这个服务叫做[我是从](https://haveibeenpwned.com/)[特洛伊·亨特那里得到的](https://www.troyhunt.com/)吗

所以你必须使用**复杂的**密码，这些密码对于你正在使用的任何服务来说都是**唯一的**。
这可能很难处理。

1.  但是[密码管理器](https://en.wikipedia.org/wiki/Password_manager)是数字健全的一个很好的起点。
2.  U2F 安全密钥，如 [Yubiko](https://www.yubico.com/solutions/fido-u2f/) ，是账户安全的第二步。

所有这些与数字健全性有关的题外话，是为了让您理解为什么数据库中的公开登录是一个可以被利用的漏洞。

让我们从数据库中获取登录信息，并尝试暴力破解它们，以便访问该帐户。用户枚举是一个漏洞，应该避免。

对此漏洞的快速修复是删除用户枚举或加密它，如果它对服务绝对重要的话。

* * *

这就是不安全银行 v2 的静态缺陷。

可以想象，这些并不是移动应用程序中唯一的漏洞。许多漏洞实际上是动态的。这意味着当您使用应用程序时，它们是可利用的。

我将在以后的文章中介绍它们。

* * *

感谢您花时间阅读本文。
我希望你在 Android 平台上学习新的安全知识。
了解最基本的漏洞是什么，以及利用这些漏洞是多么容易，以避免出现这种情况，这一点很重要。

在评论中说说你对那篇文章的看法。