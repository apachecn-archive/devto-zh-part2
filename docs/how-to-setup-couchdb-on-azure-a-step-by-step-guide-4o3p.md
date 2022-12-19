# 如何在 Azure 上设置 CouchDb:分步指南

> 原文：<https://dev.to/gate3/how-to-setup-couchdb-on-azure-a-step-by-step-guide-4o3p>

[![Main Image](../Images/e6745ff39075a139dbab8406c109ae68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wCaKqHnx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7oizyztedtjmsav7a0wf.jpg)

在过去的一个月里，我参与了一个需要离线优先能力的项目。我过去从事自动同步的工作，做起来真的很痛苦，因为一切都是手动的，很烦人。我基本上必须编写使所有移动部分工作的代码，从本地保存数据，监听网络存在，然后移动数据。

## 输入 [PouchDb](https://pouchdb.com) 和 [CouchDb](http://couchdb.apache.org/)

从那以后，我了解了小袋和沙发 db 组合，生活变得轻松多了。Pouchdb 有助于离线存储部分，并与 couchdb 有无缝、快速和可靠的关系。您需要做的就是使用您喜欢的复制连接两个数据库，然后保存到 pouchdb，剩下的工作会自动完成。

所以让我们开始吧..

## 你需要什么

你需要以下内容:
*有效的微软 azure 订阅
*一些 SSH 的知识
*一些 couchdb 如何工作的知识

## 安装 Couchdb

你可能已经知道微软 azure 是一个平台即服务(PaaS)。它可以为你提供一个专用的环境，基本上包括你需要托管的任何东西，嗯，基本上包括你想要的任何东西(是不是只有我，或者这种说法听起来很奇怪，反正继续前进)。其中一个环境是虚拟机(VM)。

要使用 Couchdb，你需要一个 VM，但是不要害怕，Bitnami 和往常一样是救命稻草。

### 第一步(去市场)

转到新的 [azure 门户](https://manage.windowsazure.com)并登录(如果尚未登录的话)。在仪表板上找到市场。如果您在仪表板上找不到它，请单击旁边的所有服务，然后查找市场并单击它。
T3![Screen Shot 2018-09-24 at 8.48.42 AM.png](../Images/2a169e3fb1bc437f49be5032e4a55e40.png)T5】

### 第二步(搜索 Couchdb)

[![Screen Shot 2018-09-24 at 8.49.49 AM.png](../Images/789acefc099c4e92a856b2fd37d5aad1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NnzeyB4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/oLWF1lflTm6bW6uyIHUv)

只需输入“Couchdb”或更直接的“CouchDB Certified by Bitnami”你应该得到如图所示的市场应用程序，并在下一个屏幕上选择它。你应该会看到一些关于什么是 couchbase 的解释，滚动到底部，然后单击下面的创建按钮。

### 步骤 3(配置您的虚拟机实例)

下一个屏幕显示了虚拟机的一些配置选项。如果你已经熟悉 Azure，那么大多数选项应该是显而易见的。资源组、区域和可用性集等选项。它们旁边都有信息图标，您可以悬停该图标以获取有关该领域的信息，但我将解释任务关键型领域(wink :-)，现在想说一会儿)。

[![Screen Shot 2018-09-24 at 8.57.10 AM.png](../Images/78a457aa71ff62ba6869e3c66a25fb20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rN7PFJsr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/P7vLQVQPRXCNljJXp2K7)

**虚拟机名称**
这是用来识别您的虚拟机的名称，也是主机名。在这里使用一个好的解释性的名字，不要太长，但要有解释性。

可用性选项
这很重要，但不是绝对必须的，因为如果没有服务器，你的应用不会失败。但我决定谈谈这件事(嗯...感觉像 MGK，但他很烂:-()，因为它对一些应用程序很重要。例如，如果您的业务逻辑规定您必须每 X 个周期进行一次备份。如果你的服务器停机了，那就有问题了，不是吗？

可用性选项只是通过在其他地方提供镜像或复制服务器来确保您的服务器不会宕机，只要您的真实服务器出于某种原因宕机，这些镜像或复制服务器就会启动。

**Image**
让这个选项保持原样，除非你改变了部署 couchdb 的想法。

**Size**
另一个选项你或许应该保持原样。默认选项应该是标准 A1 v2。这是大多数情况下的标准设置。我建议您顺其自然，随着时间的推移按需扩展。

**管理员账号**
这绝对是至关重要的。管理员是您登录虚拟机时将使用的用户名和密码。至于我，我现在使用用户名和密码组合，基本上是因为我不是唯一一个访问 vm 的人，而且我不希望将 SSH 密钥发送给每个需要访问的人。但我打算最终改变这一点，当我们更加确定谁将得到王国的钥匙时。

填写完所有字段后，您可以继续并单击“查看和创建”。除非你绝对知道你在做什么，否则不要乱用其他标签。一旦你点击预览和创建，它会在创建虚拟机时加载一段时间，只要拿起你最喜欢的饮料，看一集《瑞克和莫蒂》就行了。

现在应该完成了，恭喜你有了一个安装了 couchbase 的新虚拟机。这些选项一开始可能看起来令人困惑，但对我有用。

## 设置 Couchdb

Bitnami 在这里提供了一个官方的文档。仅供参考。

### 获取服务器凭证(针对使用密码注册的用户)

#### 方法 1:开机诊断法

默认情况下，您的用户名是 admin，但您也需要获得您的密码。为此，请访问您刚刚创建的虚拟机的仪表板，并搜索**引导诊断**。选择该选项，您将看到另一个包含屏幕截图和串行日志的菜单。

第一个菜单项“屏幕截图”为您提供 cli 中当前系统显示的屏幕截图，而第二个菜单项包含您系统的日志文件。准备好，你将要挖掘一些日志。我建议你把日志下载到你的电脑上，在本地浏览一遍。你要找的是“设置 bitnami 应用程序密码为”这几个字。

你可以看这个 youtube 视频了解更多信息
@ [关于比特纳米](https://youtu.be/SB6ZDws0m3E)的 Youtube

注意:这种方法只在你第一次创建虚拟机时有效。否则密码会被新日志覆盖。

#### 方法二:SSH

您还可以通过使用 SSH 连接到您的虚拟机来获取凭据。要获得 azure SSH 的凭证，您可以遵循以下步骤
[如何获得 SSH 凭证](https://docs.bitnami.com/azure/faq/get-started/connect-ssh/)。

一旦您登录到 SSH，执行下面的命令 **cat。/bitnami_credentials** 。如果上面的文件中没有显示内容，运行下面的命令**sudo cat/opt/bitnami/var/data/bitnami _ credentials/credentials**。

如果您使用 SSH 密钥，您将不得不使用不同的方法登录。有关更多信息，请访问上面指定的链接。

### 连接到福克斯顿

Fauxton 是一个 web 界面，它使得使用 couchdb 变得非常容易。如果您不知道它或者以前没有使用过它，请使用它，它会让您的生活非常轻松。但是要使用 Fauxton，您需要端口 5984，但是出于安全原因，该端口在默认情况下被阻止。

我将描述两种方法，一种是安全的，但使用 tunelling 限制一次只能访问一台计算机，另一种是公开访问，但当然有密码保护。如果您希望公开暴露端口，请确保您知道您在做什么，并且您绝对必须这样做。绝对必须，我的意思是你应该考虑的唯一原因是因为你希望为你的团队/公司的其他成员提供访问权限。

#### 方法 1(SSH tunneling)

默认情况下，Couchdb 只监听本地接口，因此需要一个 SSH 隧道来访问它。SSH 隧道两端都需要一个端口，一个源端口和一个目的端口。为了建立这种连接，您需要在隧道的两端使用 5984。

要连接，请在 MAC 上使用以下命令

ssh -N -L 5984:127.0.0.1:5984 用户名@服务器 IP

其中 username 是您在创建虚拟机时设置的虚拟机用户名，服务器 IP 可在您的控制面板上找到。系统将提示您输入密码，输入密码后，将会建立一个连接。

但是有一个警告，确保您的本地 couchdb 实例当前没有运行，否则您将得到一个关于无法绑定到端口或类似的错误。我花了一段时间才弄明白这一点，因为我一直试图用我的远程 couchdb 的凭证登录，你可以猜到它不起作用。我最终不得不在进程工作之前使用端口 5984 终止它。

另一个警告是，一旦连接，你不会得到一个成功连接的消息。所以你需要做的是访问 fauxton 的网址。它将是与您的本地 couchdb 实例相同的 url，因为您是使用您的本地主机进行隧道传输的。**[http://127 . 0 . 0 . 1:5984/_ utils/](http://127.0.0.1:5984/_utils/)**您现在应该看到界面了。登录并享受。

#### 方法二(公共访问)

要解锁端口，您需要允许端口 5984 通过您的防火墙。为此，您需要添加一个入站规则，幸运的是这很容易做到。只需在侧面菜单中搜索网络，并在刀片启动时单击入站规则按钮。默认情况下，新入站规则的设置将被设置为高级，将其更改为基本，您应该会看到下面的屏幕

[![Screen Shot 2018-09-25 at 9.08.32 AM.png](../Images/5810c836f1b5215376ac942a0d453f80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CxhNT0WH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/J5tB1RnJSmHzS7skAYi8)

单击服务下拉菜单并从选项中选择 couchdb。如果你愿意，你可以把名字改成更容易记住的名字。然后保存，需要一点时间。

添加入站规则后，使用 SSH 连接到您的虚拟机，然后运行下面的命令**vim/opt/bitnami/couch db/etc/local . ini**。该命令将使用 vim 打开路径中的文件，在编辑之前，您需要进入插入模式，因此请按键盘上的“I”。向下滚动并查找

[chttpd]
port = 5984
bind _ address = 0 . 0 . 0 . 0
...

[httpd]
bind _ address = 0 . 0 . 0 . 0
...

在我自己的例子中，我只找到了第一个。将 0.0.0.0 更改为 127.0.0.1。通过退出插入模式保存文件，所以按 esc 键。然后按:wq，回车保存。再次打开文件以确认规则已保存。然后重启你的 couchdb 实例**sudo/opt/bitnami/CTL script . sh 重启 couchdb** 。您的 Fauxton 实例现在已经准备好了。

要连接到它，请使用**[您的虚拟机公共 Ip]:5984/_utils/** 。您现在应该看到您的 fauxton 实例了。

我希望你能成功地完成这项工作，如果没有，请给我留言，我们可以一起调试。请关注下一篇关于如何将我们刚刚创建的实例连接到 pouchdb 并执行数据同步的文章。