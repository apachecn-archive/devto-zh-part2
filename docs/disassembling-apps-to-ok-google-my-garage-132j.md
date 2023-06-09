# 拆卸应用程序来“确定谷歌”我的车库

> 原文：<https://dev.to/samthor/disassembling-apps-to-ok-google-my-garage-132j>

2018 年初，我为我的车库门拆卸了 Android 应用程序，以便我可以修补它的内部结构，并将其连接到[谷歌助手](https://en.wikipedia.org/wiki/Google_Assistant) 🗣️.

这很有趣，包括窥探 HTTPS 和摸索安卓字节码。如果你想学习一点逆向工程，请继续阅读！

[![Super low quality GIF of opening my garage](img/1173529a687b1d841b78adda5b09ae9d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GJyGel0w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1qky48mrdl465rtt0b9c.gif)

> ⚠️这个帖子是关于逆向工程的过程，它只是为了教育的目的。我也没有在这篇文章中指明任何供应商或者提供源代码。我不纵容也不赞同这种行为。😅

# 背景故事

我有一个车库门，有通常的接口:两个钥匙圈，一个墙上的按钮，该公司实际上提供了 iOS 和 Android 应用程序。这些应用相当不错，尽管对于一个简单的动作来说相当复杂。🙅

但是让我们面对现实吧:不得不按键是一件麻烦事。有指向，找到按钮，按下它—👉🔘—但是因为我们生活在未来📡🔬我应该可以通过谷歌，通过我的谷歌主页或手机与我的车库通话。

* * *

# 结束

以下是我说 0️⃣语时采取的步骤*好的谷歌，打开车库门*:

1️⃣谷歌使用其服务识别我的声音
2️⃣[if TTT](https://ifttt.com/)服务的小程序匹配字符串“打开车库门”
3️⃣ IFTTT 对我的应用程序引擎后端进行简单的 HTTP 调用
4️⃣我的后端对供应商的 API 进行一系列登录/请求调用
5️⃣这连接到我家的物理互联网连接控制器(来自供应商)
6️⃣控制器在专有频率上广播，让“哑”马达执行动作

[![The seven steps needed to open a garage door](img/457d37b6aa7b987fc123c18c4e0bc5ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mfgJsF4T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ti8kcixc8v9i63d5clbh.png)

这是自动化一个简单任务的许多步骤。但是，从我讲话到门打开或关闭，不超过几秒钟。我也不介意在线部分——我对离线不感兴趣，因为谷歌的识别器不是为离线工作而设计的。

* * *

那么，我是怎么做到的呢？

我的大部分工作是在🛠️对车库门公司的 Android 应用程序进行逆向工程，以及它如何与物理控制器对话——正如我上面提到的那样，工作正常，但对我来说太复杂了。

[![Garage door and controller](img/d1ff27d30658f1cd546f95af2ec4552b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zia-V1tK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1q0iirclyqjkhd8gzbh0.jpg)

<center><small>The controller device from the vendor, which bridges the vendor's API and the dumb motor next to it</small></center>

## 线上 vs 线下

我做的第一步是尝试几个不同的网络环境，找出所有东西是如何相互交流的。

1.  我关闭了我的互联网，同时让我的手机和控制器保持在同一个 WiFi 连接上——供应商的 Android 应用程序*仍然工作*，所以他们有一个局域网后备

2.  我把手机拿到外面，关掉了无线网络——他们仍然可以通话，所以可能在某个地方有一个服务器

## 窥探 HTTPS

对于上下文:公平地说，大多数 API 都是基于 HTTP 的——能够穿透透明代理或在移动应用程序和 web 管理页面上使用相同的 API 是一个巨大的胜利。

在 2018 年，很难想象任何协议会超越纯 HTTP——我必须从 HTTPS 说起。

因此，天真地说，我开始尝试检查供应商的 Android 应用程序产生的 HTTPS 流量——它在做什么？我用我的电脑创建了一个本地 WiFi 网络，然后使用 [mitmproxy](https://mitmproxy.org/) 拦截 HTTPS 连接(根据你的操作系统，设置步骤会有所不同)。

mitmproxy 和其他类似的工具有两种可能的工作方式:

1.  您可以访问供应商的私有 SSL 证书，并可以充当它们的角色(只有当您*是供应商*时才真正有用，用于调试)
2.  该工具生成新的假证书，并且您能够在您正在收听的设备上安装新的根 CA(以便它信任该工具)

由于我不是车库门公司，我需要选择第二种方案。 [mitmproxy 记录了如何](https://docs.mitmproxy.org/stable/concepts-certificates/)向您选择的设备添加新的根 CA——实际上，我有一部备用的 iPhone，我发现在那里添加证书要容易得多。

### SSL 证书锁定

但是这篇文章有一个问题。供应商明智地实现了所谓的 SSL 证书锁定——这意味着 mitmproxy 毫无用处。

在这种情况下，供应商的应用程序需要一个非常具体的证书(基于它的公钥)，而忽略了设备自身的信任概念。并非所有的应用程序都是这样构建的——mitm proxy 实际上可以正确地从我的 iPhone 的其他部分读取大量流量，包括与 iCloud 的通信，如果你对此感兴趣，这很有趣。

牵制工作的细节不值得讨论，但这是一个死胡同，对有问题的公司有好处——这真是一个好主意。我能发现的唯一有趣的事情(实际上是通过一个叫做 Wireshark 的工具)是应用程序进行的 DNS 查找——它调用的 API 的主机名。

* * *

因为我无法拦截供应商的应用程序正在进行的 HTTPS 连接，所以我决定最好的方法是窥探供应商的源代码。

# 拆卸

在 Android 上，这实际上是非常琐碎的，但是产生的源代码很难读懂——并非不可能。在 iOS 上，这要困难得多——大多数 iOS 应用程序都是实际的本机代码(不像 Android，它们是字节码)，所以除非你想整天阅读汇编程序(我不想)，否则你就不走运了。

## 检查一个机器人

分析应用程序的第一步是实际*获得*应用程序。Android 应用程序是作为 apk(实际上只是 zip)发布的。

把这些从你自己的设备上弄下来其实很棘手。然而，如果你在谷歌上搜索“apk 下载名称. of.the.package”，你会找到很多 APK 下载网站。

> **旁白**:我不会评论这个的合法性，但肯定是*容易*。也不能保证这些网站在你下载应用程序之前不会改变它(想想常见的攻击媒介——挖掘加密货币、窃取密码)。

一旦你有了 APK，你将需要 [Apktool](https://ibotpeaches.github.io/Apktool/) 。在 macOS/Homebrew 上，可以用`brew install apktool`安装。

运行这个工具，用这些命令检查一些 [Smali](https://en.wiktionary.org/wiki/smali) 文件 Android 字节码的可读版本:

```
apktool d name-of-apk.apk
ls -R smali/ 
```

Enter fullscreen mode Exit fullscreen mode

Apktool 也可能提取`.so`文件，这些文件是不同平台的实际二进制文件(你通常会在`x86`、`armeabi`路径中看到它们)。你真的不能用它们做任何事情(它们是汇编程序)——在我的例子中，我发现了一些支持库，但是没有应用程序或协议逻辑。

# 分析代码

现在您已经有了应用程序的源代码，打开任何文件看一看。Smali 很难阅读，由于混淆，它可能会使用模糊的变量或类名，如`a`、`b`、`c`等。看一看:

```
.method public constructor <init>(Lcom/example/ViewProxy;)V
    .locals 2
    .param p1, "proxy"    # Lcom/example/ViewProxy;

    .prologue
    .line 39
    invoke-direct {p0, p1}, LLcom/example/UIView;-><init>(Lcom/example/ViewProxy;)V

    .line 41
    new-instance v0, Linfo/whistlr/SquareNavBar;

    invoke-virtual {p1}, Lcom/example/ViewProxy;->getActivity()Landroid/app/Activity;

    move-result-object v1

    invoke-direct {v0, v1}, Linfo/whistlr/SquareNavBar;-><init>(Landroid/content/Context;)V

    .line 43
    .local v0, "hcsb":Linfo/whistlr/SquareNavBar;
    new-instance v1, Linfo/whistlr/View$1;

    # ... 
```

Enter fullscreen mode Exit fullscreen mode

此时，您有两个选择。

1️⃣遍历整个代码库并跟踪代码流
2️⃣添加了大量的日志调用并重新编译了应用程序。

当然，你要做第二种选择。

使用反汇编的 Android 代码的最大好处是对内置方法和类的调用总是有它们的“真实”名称。这是什么意思？这意味着对像安卓上的[标准 HTTPS 客户端`HttpsURLConnection`这样的东西的调用，发生在名字为的**上——所以你可以搜索它们并在它们周围登录。**](https://developer.android.com/reference/javax/net/ssl/HttpsURLConnection.html)

## 寻找矢量

所以我需要寻找对`HttpsURLConnection`的引用——我可以用`grep`来表示这个:

```
grep -r HttpsURLConnection smali/ 
```

Enter fullscreen mode Exit fullscreen mode

在我的供应商应用程序中，所有的引用都局限于几个文件。显然，供应商已经简化了他们的 API，所以它只在几个地方被调用，这使得我们很容易登录。

我打开了那个文件(在我的例子中，它是`smali/com/vendorname/client/comms/c/b.smali`)四处翻看着。例如，这段代码将`User-Agent`设置为调用静态方法
的结果

```
const-string v1, "User-Agent"
invoke-static {}, Lcom/vendorname/client/comms/c/a;->a()Ljava/lang/String;
move-result-object v2
invoke-virtual {v0, v1, v2}, Ljavax/net/ssl/HttpsURLConnection;->setRequestProperty(Ljava/lang/String;Ljava/lang/String;)V 
```

Enter fullscreen mode Exit fullscreen mode

这里有大量类似的代码，包括设置请求 URL 和有效负载——这是完美的！🎉👨‍💻

* * *

# 旁白:字节码 101

Smali 代码是可读的 Android 字节码，它有类、静态方法和实例方法。但是没有变量或声明——相反，每个方法都有一些*寄存器*。

我们在任何方法的顶部都可以看到寄存器的数量:

```
.method public writeToParcel(Landroid/os/Parcel;I)V
    .locals 2  # <-- this has two local registers 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，这个方法实际上有*三个*寄存器——两个本地寄存器(引用为`v0`、`v1`)和一个参数(`Parcel`变量，引用为`p0`)。有两件事要记住:

1.  在实例方法上，`v0`是*总是*的`this`对象。
2.  参数也被引用为变量 no 加上局部变量的数量。所以在我们的例子中，`p0`相当于`v2`(下一个未使用的局部)。

Android 将常规方法转变为这种注册方法的原因是它试图使用尽可能少的变量。如果你有一个使用几十个变量的方法，但是一次只使用一个，Android 编译器可以将这些使用打包到一个寄存器中。你会在整个 Smali:

```
const-string v1, "POST"  # store "POST" in v1
invoke-virtual {v2}, Lcom/app/client/c;->d()Ljavax/net/ssl/SSLSocketFactory;
move-result-object v1  # store the SSLSocketFactory in v1 
```

Enter fullscreen mode Exit fullscreen mode

(重要的是，寄存器的类型可以改变，但这是在编译时检查的。)

* * *

回到我之前打开的文件。🗃️🔙

# 以伐木为乐，以营利为目的

我们基本上想找到任何字符串——HTTP URL、参数名——然后将它们记录到 Android 的控制台。

我们可以调用 [Android 日志方法](https://developer.android.com/reference/android/util/Log.html)来实现这一点。它有两个参数——一个“标签”和一个字符串。这个标签很有用——我可以在那里放一个唯一的字符串，这样我们就可以在 Android 日志中快速找到输出。

要声明一个新的标签，最简单的方法是增加`.locals`的数量，这样我们就可以在高位局部变量中声明一个新的字符串(因为没有限制，字节码只是为了提高效率)。如果您的方法有六个，那么只需再添加一个:

```
.method public run()V
    .locals 7  # was 6, increase this number as much as you need 
```

Enter fullscreen mode Exit fullscreen mode

现在，要记录一个注册——就像我们之前的`User-Agent`一样——你需要做的就是:

```
const-string v1, "User-Agent"
invoke-static {}, Lcom/vendorname/client/comms/c/a;->a()Ljava/lang/String;
move-result-object v2
invoke-virtual {v0, v1, v2}, Ljavax/net/ssl/HttpsURLConnection;->setRequestProperty(Ljava/lang/String;Ljava/lang/String;)V

# now log "User-Agent" and its value
const-string v6, "__XXX__garagedoor__XXX__"
invoke-static {v6, v1}, Landroid/util/Log;->v(Ljava/lang/String;Ljava/lang/String;)I
invoke-static {v6, v2}, Landroid/util/Log;->v(Ljava/lang/String;Ljava/lang/String;)I 
```

Enter fullscreen mode Exit fullscreen mode

我找到了服务调用的 URL，因为应用程序正在构造一个`java/net/URL`，我记录了传递给它的`v4`字符串。

我还发现了 POST 请求的 JSON 有效负载——一座金矿！—通过在`HttpsURLConnection`上找到对`getOutputStream`的调用，然后代码使用这些调用来写一个`String`。该字符串是请求的有效负载:

```
# this code gets OutputStream (v3) and creates a DataOutputStream (v2) around it
invoke-virtual {v0}, Ljavax/net/ssl/HttpsURLConnection;->getOutputStream()Ljava/io/OutputStream;
move-result-object v3
invoke-direct {v2, v3}, Ljava/io/DataOutputStream;-><init>(LLjava/io/OutputStream;)V

# **add this** code to log v1...
const-string v6, "__XXX__garagedoor_payload__XXX__"
invoke-static {v6, v1}, Landroid/util/Log;->v(Ljava/lang/String;Ljava/lang/String;)I

# ... because the app writes the string in v1 to our DataOutputStream (v2)
invoke-virtual {v2, v1}, Ljava/io/DataOutputStream;->writeBytes(Ljava/lang/String;)V 
```

Enter fullscreen mode Exit fullscreen mode

好的，所以我添加了一些日志语句。现在怎么办？🤔

## 回器

我现在可以使用`apktool`和一些其他工具来重新组装我“自己”版本的 APK。如果您在 Smali 中犯了任何错误，`apktool`会在这里抱怨——这基本上是重新编译代码。这是我的心流:

```
# apktool to reassemble APK
apktool b -f smali/ -o replacement.unaligned.apk

# you'll first need to generate a 'keystore' using Android tools
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 \
  -keystore my-key.keystore -storepass YOUR_KEYSTORE_PASSWORD \
  replacement.unaligned.apk YOUR_KEYSTORE_NAME

# required, tool from Android SDK
zipalign -v 4 replacement.unaligned.apk replacement.apk

# finally- install onto your device!
adb install -r replacement.apk 
```

Enter fullscreen mode Exit fullscreen mode

在我安装应用程序之前，值得注意的是我必须使用我自己的*keystore，所以我不能覆盖我通过 Play Store 安装的应用程序。我先卸载那个版本。*

注意，上面的工具(`jarsigner`、`zipalign`和`adb`)都是 [Android 命令行工具](https://developer.android.com/studio/command-line/)的一部分。

## 阅读日志

现在，我只需像往常一样打开应用程序并登录(该应用程序有一个奇怪的帐户系统，但它现在并不真正相关)。

我可以只使用`adb logcat`，grepping 作为之前的标签—我会看到一堆有趣的日志语句:

```
$ adb logcat | grep __XXX_garagedoor
V/__XXX__garagedoor__XXX__(  871): User-Agent
V/__XXX__garagedoor__XXX__(  871): name-of-user-agent; v1.0 
```

Enter fullscreen mode Exit fullscreen mode

太好了！唷！哇，那不算短。我不打算在 Android 方面做更多的介绍——这很有趣，但是现在你已经有了所有你需要的工具去发现应用程序在做什么。

## 有效载荷概率

我要注意的一件事是，我的供应商的应用程序通过 HTTPS 发送的有效载荷实际上是以一种相当奇怪的方式加密的——*尽管*HTTPS 已经加密了有效载荷。所以从表面上看，它们很难解析:但是请记住，加密的有效载荷只是在应用程序中的其他地方*生成的。🤷*

所以当我找到加密的来源和代码时，我自己重复相同的步骤是微不足道的。

* * *

一旦我确定了应用程序使用的协议，剩下的事情实际上就相当简单了。有几个部分。

## 我的应用引擎后端

App Engine 非常适合小型 HTTP 服务器——对小型项目基本免费，支持 Python，Node。JS，Go 等语言。我写了一个小的 Go 服务器，它实现了供应商的协议。

当您用 HTTP POST 点击它时，它会对供应商的 API 进行登录/请求。它看起来有点像:

[![Diagram of HTTP requests](img/499214b418da43999bd7d3d33b7cd043.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jHZ4f1y0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b37352wfkb8ykjdhe0me.png)

我的应用程序引擎后端知道点击供应商的 API 所需的帐户。但是我要求 HTTP 调用方知道“秘密”——所以任何发现这个 API 的人都不能随便打开我的车库门(想象一个@OpenMyGarageDoor Twitter bot)。

## IFTTT 集成

IFTTT 允许你设置自定义的“小程序”在某些[谷歌助手](https://ifttt.com/google_assistant)触发器上运行。这就是我如何使用这扇门的。

[![IFTTT applet](img/48c5c4b21ae2e6f27618adc3ad68ea4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lbnQf1Pw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/81lawdz3u19fnc3c5m36.png)

这个小程序非常简单——我设置了许多语音命令，比如“打开车库”,它们调用一个 HTTP 端点。然而，这种 IFTTT 集成的一个挑战是，IFTTT 将总是说预先设定的响应，例如，“是的，我正在开门！”— *甚至*如果 HTTP 请求失败。似乎没有办法通过 IFTTT 真正传播错误。🚨👎

## 谷歌语音账户

IFTTT 专门为您的账户*服务。在我的家里，我不是唯一一个拥有智能设备的人，这并不理想——它只能从我的手机上打开。*

然而，如果你的 Google Home 设备支持多用户，额外的语音操作——“打开车库门”——似乎是所有本地用户共享的。因此，如果我的伴侣或访客在房子里对一个家庭说“打开车库”，它会很好地工作。不过目前，他们不能从手机上触发同样的事情，除非他们也设置了 IFTTT 小程序。

* * *

# 完成了

感谢阅读！我希望你已经了解了开始做一点软件逆向工程是多么容易，也许会启发你让你的房子为你工作。🏡👩‍🏭

如果我在这方面花更多的时间，我可能会努力删除 IFTT 部分——如果请求失败，不返回失败代码是非常令人沮丧的，这意味着偶尔什么也不会发生，我也不知道为什么。Google 还有其他选项，比如 Google 上的 [Dialogflow 和 Actions](https://developers.google.com/actions/dialogflow/)，我还没有真正看过，但 IFTTT 对于原型制作来说非常棒。

如果我购买产品的公司有助理支持，我永远也不会尝试这个！所以谢谢你给我一个有趣的项目。😂

# 感谢

*   一些来自[Icons8.com](https://icons8.com/icon/set/router/dusk)的图标，用在 CC BY-ND 下。
*   大田经[大田类型](https://emojityper.com)。