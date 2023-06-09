# 研讨会-创建分散式应用程序

> 原文：<https://dev.to/nikolayangelov/workshop---create-decentralized-application-2b6m>

## 简介

大家好。我的名字是**尼古拉·安杰洛夫**([http://nikolaytech.com](//../))我已经组织了这个**工作室**，在这里我们将使用一些工具创建一个非常简单但是强大的**去中心化应用**。

**应用**的**总体**想法将是使用 **IPFS** 以**分散**方式存储**证书** ( **图像**，并将**图像**的**哈希**保存在**以太坊** **智能合约**中。这样我们就可以确定没有人更改过 T21 的证书，而且它也没有被更改过。

为了创建我们的应用程序，我们需要安装一些工具，我将在这个**文档**中解释如何安装。

工具列表:

1.  **加纳切**(【https://truffleframework.com/ganache】T2)
2.  **星际文件系统**-https://ipfs.io/[IPFS](https://ipfs.io/)
3.  **MetaMask** ( [https://metamask.io/](https://metamask.io/) )
4.  **JavaScript** + **js**

## 安装 Ganache

Ganache 是一个快速的可定制的区块链 T2 模拟器 T3。它允许你调用**区块链**而不需要运行实际**以太坊**节点的开销。

*   立即挖掘**事务。**
*   没有交易成本。
*   账户可以回收，重置，用固定量的乙醚实例化(不需要水龙头或者挖矿)。
*   气价和开采速度可以修改。
*   一个方便的 GUI 为您提供了测试链事件的概览。

现在该安装 **Ganache** 了。

1.  打开[https://github.com/trufflesuite/ganache/releases/tag/v1.2.2](https://github.com/trufflesuite/ganache/releases/tag/v1.2.2)，根据您正在使用的操作系统选择合适的版本。
2.  对于 **Windows** **下载**这个。**exe**T6![](img/693f4ca1ddf9af470c73f03662e177f5.png)T8】

现在**根据**你的 Windows 设置，**Windows Defender smart screen**可以警告你这个安装不安全。忽略它，按照步骤安装 **Ganache** :

[![](img/70158125bbcb67e446cee1b1f2a6c6fe.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/windows-security-1.png)

点击**更多信息**后，会出现一个新的屏幕，显示**安装**按钮。

[![](img/894761e74e1f86b3d322155e852038ba.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/windows-security-2.png)

然后你只需要点击**下一步**和**按照**正常的**安装** **指令**和 Ganache 应该在你的电脑上运行。

1.  现在，您可以允许或禁止 Ganache 分析您的数据。做出选择，点击**继续**。

[![](img/85f66c72413deedebf56cc7876b6d5a4.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/support-ganache.png)

**恭喜**！您成功安装了 **Ganache** ！

1.  运行你的 **Ganache** ，你会看到一个屏幕，上面显示着你所有的账户、硬币和其他一些**信息**。

[![](img/4b7b63cd69fdd7b5c06b67ce5e50dc8c.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/Ganache.png)

让我们暂时离开**加纳切**并且**安装**我们需要的其余东西。

## 使用 IPFS 的 Infura 提供程序

**IPFS** 是**一个点对点** **分布式** **文件** **系统**，它寻求连接所有具有相同文件系统的计算设备。在某些方面， **IPFS** 类似于**万维网**，但是 **IPFS** 可以被视为一个单独的**BitTorrent**T18】群，在一个 **Git** 库内交换对象。换句话说， **IPFS** 提供了一个高吞吐量、内容寻址的块**存储**模型，带有内容寻址的超链接。(来源:[维基百科](https://en.wikipedia.org/wiki/InterPlanetary_File_System))

1.  安装 **IPFS** 对于**初学者**来说可能是一个艰难的过程，所以我决定使用 [https://infura.io](https://infura.io) 提供的 T4 API**注:如果你**有兴趣**自己**安装**这个**只要**跟我说**我会帮助你。****

 **## 安装元掩码插件

**MetaMask** 是一座桥梁，让你今天就能在浏览器中访问未来的分布式网络。它允许你在浏览器中运行**以太坊** **dApps** ，而无需运行完整的**以太坊** **节点**。

**MetaMask** 包括一个**安全**身份**保险库**，提供一个用户界面来管理你在不同网站上的身份并签署**区块链**T8】交易。

1.  打开[https://metamask.io/](https://metamask.io/)，为你的浏览器获取插件。[![](img/a6745edc9873e79feecf105bb7b470e8.png)](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-1.png)[![](img/f789121228eec398b31757eb928b920f.png)](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-2.png)T8】
2.  现在**元掩码**应该出现在你的浏览器**扩展**中。(右上角)

[![](img/70dc0946e6e9ef21506317d214dcd714.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-4.png)

你可能会看到一个屏幕，告诉你尝试新的**版本**。

请使用旧版本并选择“不，谢谢，也许以后吧”,因为新版本现在很痛苦。

**[![](img/3e532bc7307c4ba7e2fd29253b3d5723.png)](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-5.png)T4】**

1.  现在你需要点击**接受**进入接下来的几个屏幕，直到系统提示你**创建**一个**密码**。这是因为**元掩码**实际上是一个需要保护的钱包

    [![](img/c69841f9bce746ecb80fe4de12faf116.png)](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-6.png)

2.  **现在**你有了一个**钱包**，**在下一个屏幕上会出现**你的 12 个关键字叫做**助记符**。你需要把它们存放在一个安全的地方，因为它们以后会让你在密码被破解或被盗/忘记的情况下访问你的钱包。在我们的情况下，我们真的不在乎，因为我们要运行我们自己的**网络**。

    [![](img/76983dbec6c0b649cf6e9e394eb6d406.png)](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-7.png)

3.  现在我们只需要将**元掩码**连接到我们早期开始的**加纳切**上。

这是一个简单的任务——点击**元掩码**的左上角，你会看到“**主网络**和一个**箭头。单击箭头。**

[![](img/febf79296fe4b58d7052c31d34d5a6f5.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-8.png)

1.  在下拉菜单中选择“ **Localhost 8545** ”，这是我们的 **Ganache** 本地地址。 [![](img/96ab578abce742de679b67d3175555aa.png)](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-9.png)
2.  现在我们连接到了我们的**网络**，我们只需要导入一个装有硬币的**钱包**。为此，我们需要进入我们正在运行的 **Ganache** 和**选择列表中第一个** **地址**右侧的按键图标( " **显示按键**"**)。 [![](img/c1bb614bed9fa1550f0454e8a8030681.png)](http://nikolaytech.com/wp-content/uploads/2018/09/ganache-account.png)**
3.  之后只需复制**私钥**。【T2![](img/e8306d7a555a69ee0db366f6fd4a3903.png)
4.  然后我们返回到**元掩码**将这个**密钥**导入到我们的钱包中。

**导入**与我们在右侧选择的网络位于同一行。

[![](img/5a8eb759e81ca46c14868c405360fe85.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-10.png)

点击此处，然后选择“**导入账户**

[![](img/2461afcfa812b3f2747c0bd69c5b2ddf.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-11.png)

1.  将我们从 **Ganache** 复制的**私钥**粘贴到框中，并将* ***导入*** * [![](img/d93bbfe6af8d0a4db56b86915e624f2a.png)](http://nikolaytech.com/wp-content/uploads/2018/09/metamask-12.png)
2.  现在我们有钱了，我们可以开始写代码了。【T2![](img/e18fdcf016d6adf21ee18747d41620e6.png)

## 创建智能合同

为了创建我们的**智能契约**，我们将使用一个名为 **Remix** 的在线编辑器。

**在更高级的层面上，Remix** 实际上支持在**契约**上运行和部署，但我们并不真正需要它的所有基本功能。

1.  去打开 https://remix.ethereum.org 的****，你会看到一个文本编辑器。****

 **[![](img/e648c43f76af4659715301f964b91214.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/remix-1.png)

1.  在左上角有一个“**加**的图标，用它我们可以开始我们的第一个**合同**。

**[![](img/ac23260084a1a306e50f0d6020711198.png)](http://nikolaytech.com/wp-content/uploads/2018/09/remix-2.png)T4】**

单击它，然后我们需要键入新的**合同**的名称。姑且称之为**证书注册**

[![](img/9faa750ed9bfae65350c713f298f74fb.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/remix-3.png)

1.  现在是开始**编码的时候了**这里是我们的第一行代码，基本上定义了我们正在使用的**可靠性**的**版本**和实际的**契约**。 [![](img/a8723e3a33f69bb32d5d15ebfb7cf3d5.png)](http://nikolaytech.com/wp-content/uploads/2018/09/contract-1.png)
2.  我们想要存储**证书**并为它们保留一些**数据**。

首先，我们将**证书**的图像存储在 **IPFS** 上，因此我们需要保存它的**哈希**。

我建议保留**证书**添加的时间信息。

让我们看看这个东西在**可靠性** **代码**中是什么样子，并把它添加到我们的代码中。

[![](img/252f26a39ca58beb1a15df20df000b5e.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/contract-2.png)

1.  现在我们有了一个**结构**，在那里我们可以存储我们需要的**数据**，但是我们希望在我们的网页上保留多个**证书**，所以我们应该有一个**证书**的**数组**。 [![](img/0b05fcdcd81e439c67e92986c08c268a.png)](http://nikolaytech.com/wp-content/uploads/2018/09/contract-3.png)
2.  所以我们可以把所有的**数据**存储在**世界**里，但是我们**没有**有办法**添加**新的**证书**。也许是时候让我们创建一个**函数**来为我们做这件事了。 [![](img/df6c764543cd72b7e1142ee6157542b9.png)](http://nikolaytech.com/wp-content/uploads/2018/09/contract-4.png)
3.  在我们的**合同**中，我们需要的最后一件事是获得我们所有的**证书**的方法。在 **Solidity** 中，你不能返回**数组**，所以我们需要一个**函数**，它将给出我们拥有的**证书**的**计数**，然后我们需要**另一个**函数，它将给出**特定** **证书**的**信息**。 [![](img/e3849a65fc6a89c5822ed09054caef07.png)](http://nikolaytech.com/wp-content/uploads/2018/09/contract-5.png)
4.  这是我们将要使用的最基本的**合同**。
5.  要使用它，我们需要将它部署在我们的**加纳切**本地**网络**中。我们如何做到这一点？嗯，这就是我们使用**混音**的原因……因为**混音**只需几次**点击**就能完成。
6.  在编辑器的右侧，我们可以看到更多信息。那里我们有几个**按钮**。其中有【编译】**【运行】**【分析】【测试】【调试器】和一些设置。我们现在只关心“**运行**，所以直接选择“**运行**”

[![](img/3de6670480cf60cb07f298b9d14c74bb.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/remix-run.png)

1.  之后，我们还有几件事。"**环境**、"账号"、"气限"等等。目前，我们的目光投向了**环境**。在下拉菜单中我们有几个选项，基本上是****选项**让我们告诉**混音**去了哪里**玩**和**部署**我们的**合同**。我们想使用我们的 **Ganache** ，所以我们应该选择第三个选项“**web 3 Provider**”[![](img/f4cd29d9bde1ef29bffd41aa611bb4f7.png)](http://nikolaytech.com/wp-content/uploads/2018/09/remix-web3-provider.png)**

 **然后我们看到一个屏幕，询问我们是否确定要连接到**以太坊** **节点**。请选择“**确定**，然后在下一个**屏幕** **上再次点击**“**确定**”，而不改变框中的任何内容。

[![](img/4b6b8f472dab76359c8716aed64e7920.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/remix-ok.png)

这样我们将 **Remix** 连接到我们的 **Ganache** ，当我们这样做的时候 **Remix** **自动**用来自 **Ganache** 的钱填充**账户**，所以我们实际上有**钱**。(不像**元掩码**)

1.  现在我们需要**部署****合同**。事情发生在* *部署
    [![](img/e9a9b6b10299093d373360468be4941e.png)](http://nikolaytech.com/wp-content/uploads/2018/09/remix-deploy.png) 
    **

之后在**部署的** **合同** **部分**，我们会看到关于我们**合同**的信息。

从那里最重要的是我们**合同**所在的**地址**，但是现在，让它保持原样我们几分钟后就会返回。**(请不要关闭混音)**

## [![](img/f2d090b21346718029ea47b662d19862.png)](http://nikolaytech.com/wp-content/uploads/2018/09/remix-deployed.png) 创建我们的 JavaScript 前端

嗯，我们已经有了合同，一切都准备好了。现在我们需要一种方法来与它交互并上传**证书**。出于这个原因，我们将创建 **JavaScript** 页面。

我已经准备了一些**代码** **骨架**作为**资源**，其中保存了 **CSS** 、 **HTML** 和一些我们可能需要的初始**助手**、**函数**并且还有**不**需要**代码**它们。

1.  从[证书-注册表-框架](http://nikolaytech.com/wp-content/uploads/2018/09/CertificatesRegistry.zip)下载代码框架
2.  解压压缩**档案**，在那里你会找到我们需要的所有**文件**。如果你写合同有困难，我们甚至有合同

我们要**编辑**的主要是 **certificates.js**

1.  在您选择的**编辑器**中打开**文件夹**或 **js** 文件。
2.  现在，在我们进入 **JavaScript** 和**完成**这个**工作坊**之前，我们只需要最后一个**安装**，那就是我们的 **HTTP-server**
    1.  要安装 **HTTP-server** ，您需要打开您的**终端** ( **命令提示符)**，并在那里键入以下内容:

> npm 安装–g HTTP-server

1.  然后转到项目目录，在你的**终端** ( **命令提示符**)中运行

> HTTP-服务器

让我们回到 **JavaScript** 编辑器和我们的 **certificates.js** 文件。在那里你可以看到一些准备好的东西，还有两个**函数**，它们有 **TODO** ，我们实际上需要对它们进行编码。

[![](img/838def8830b4096cd1c377bc5df09a22.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/Javascript-todo-1.png)

因为实际的**框架**已经准备好了，我们已经有了我们的**HTTP-服务器** **正在运行**，我们可以打开并先看一下**页面**。(**不是**一切都会**起作用**但是我们可以看到我们所拥有的)

1.  我们去看看。打开[http://localhost:8080](http://localhost:8080)T2![](img/3445f6974875678c5ee71bf7e8ab6f01.png)T4】

这里是我们的**初始**版本。当然，**按钮**现在不起作用，是时候让它们做些事情了。先说“**提交证书**

1.  在开始我们的" **TODO** 项之前，我们需要设置我们的**到**合同**的连接**。Web3 将会出现这种情况，它是由**元掩码**提供的，所以我们需要做的第一件事是检查用户是否安装了**元掩码**，如果没有，我们需要告诉他们安装它。 [![](img/af9b526ebf8630f0edb6f1e60d812235.png)](http://nikolaytech.com/wp-content/uploads/2018/09/javascript-code-1.png)
2.  之后，我们需要**将**连接到**契约**。这就是为什么我们在顶部有一个**空** **变量**，它是**合同地址**。我们需要写下我们的**合同地址**，我们从我们的 **Remix** 中获得该信息。(**希望是**，你没关。我警告过你 J)回到 **Remix** ，在**部署** **契约**中点击小图标，它将复制**契约**的**地址**，然后将其传递给这个变量。

[![](img/935c52cbca0f413631d93eaf80b5fd48.png)](http://nikolaytech.com/wp-content/uploads/2018/09/javascript-remix-contract.png) 

[![](img/a21d10fab6503888971b812979fb98a1.png)](http://nikolaytech.com/wp-content/uploads/2018/09/javascript-contract-address.png)

**注意**:对于**每个人**，**合同**，**地址**会有所不同，所以不要从我的屏幕上复制这个，只做**步骤**。

1.  现在真正的**连接**。为此，我们需要一个名为 **ABI** 的东西，但是我已经让**在我们的**配置**文件夹中为你准备好了**它，所以我们将从我们刚刚编写的**框架**和这个**地址**中使用它。看起来是这样的。

[![](img/4fed71acb1ab06dd3e605d4570983e52.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/javascript-connecting.png)

基本上是说…嘿， **Web3** 这是这份**合同**，它位于那个**地址**。

这样，我们的 **JavaScript** 知道如何**与**契约**通信**以及在哪里找到它。

1.  我们要编写的第一个“ **TODO** 来自第一个函数“ **uploadCertificate** ，它与 **IPFS** 交互并上传文件，然后将信息添加到我们的**合同**中。

在骨架中，我已经准备好了 **IPFS** 并将其连接到**Infura**T4】提供者。

从前两行可以看出。

[![](img/f6cdf0b01cfd829372f771b78ef99aa3.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/ipfs-settings.png)

现在我们把代码留给**实际上传。**

1.  首先，我们选择**文件**表单**所在的 **Div** 元素**，我们想要检查**用户**在**执行**之前是否选择了**任何** **文件**，如果他没有选择文件，我们使用**助手**函数 **showError** 显示一个**错误**。 [![](img/28a8dd8f1a453ba1173d02342b66499f.png)](http://nikolaytech.com/wp-content/uploads/2018/09/upload-1.png)
2.  现在，如果**用户**选择了一个文件，我们就该把它上传到 **IPFS** 并获取它的散列。

[![](img/59e35e0ecd7cfa866b6bcf5c4b82db8c.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/upload-2.png)

1.  我们有了**证书**的**哈希**，现在我们可以将它添加到我们的**智能合约**中。这是通过我们的变量"**契约**来完成的，我们用它来连接我们的* ***智能契约。*** * [![](img/7f84498e28471b3e37cdfd4cf8995dd1.png)](http://nikolaytech.com/wp-content/uploads/2018/09/upload-3.png)
2.  这样，我们就有了上传**证书**的**功能，我们可以进入我们的**页面**进行检查。只需再次访问 [http://localhost:8080](http://localhost:8080) 并尝试**提交**一个**证书**。**

[![](img/eff2631f509547ea8c597056a47ec2dc.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/view-upload-1.png)

现在当你点击**提交，MetaMask** 会**弹出**询问你是否要做**交易**。这是因为我们试图在**区块链**中**记录**的**信息**，而**元掩码**请求**允许**这样做。只需**点击**元蒙版**中的****提交**，神奇的事情就发生了。

**[![](img/11ca305dd261a85188a4171b3094cba8.png)](http://nikolaytech.com/wp-content/uploads/2018/09/view-upload-2.png)T4】**

如果你做的每一件事都是正确的，你将会得到这个美好的 T2 成功 T4 消息。

[![](img/be43a1d67787fe9e4a8da089ee244a7f.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/view-upload-3.png)

1.  一切都好。我们可以将证书图像上传到 **IPFS** ，可以将**信息**存储在**区块链**中，但仍然无法查看。也许是时候开始**编码**我们的第二个**TODO**(**功能**)。
2.  好了，让我们跳转并编码我们的**视图获取证书**

[![](img/b58117f1e40797710599599f63b08227.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/view-1.png)

1.  这里我们首先需要获得我们已经存储了多少个**证书**的**号**。那是因为我们想在**实度**内的 **JavaScript** 和**而不是**上**迭代**，因为**实度**中的**循环**的**相当昂贵。**

[![](img/3ac8118dfc5d1b9b8cc667ef34131344.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/view-2.png)

1.  现在我们有了**个证书**的数量，我们想要一个接一个地获取它们的信息，并使用 **jQuery** 将它们附加到我们的 **HTML** 中。

[![](img/b7e868276f03f8e9c1054d414f878e76.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/view-3.png)

至此，我们现在应该也能看到证书的工作情况了。

1.  让我们在 [http://localhost:8080](http://localhost:8080) 再次打开我们的页面，点击**查看证书**，看看我们之前上传的**证书**是否在那里。

[![](img/bdfd5a6ed942930f2d0ba56cd04a2ead.png)T2】](http://nikolaytech.com/wp-content/uploads/2018/09/certificate.png)

**现在，我们与 IPFS 合作开发了一款完全可用的分散式应用，这就是分散式存储。**

## 您可以向我提问或关注我:

[**【http://nikolaytech.com】**](http://nikolaytech.com)
[**https://linkedin.com/in/thedi**](https://linkedin.com/in/thedi)
**邮箱:**[**nikolay@nikolaytech.com**](//mailto:nikolay@nikolaytech.com)******