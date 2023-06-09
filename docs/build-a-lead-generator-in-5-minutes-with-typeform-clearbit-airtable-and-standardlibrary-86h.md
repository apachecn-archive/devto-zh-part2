# 使用 Typeform、Clearbit、Airtable 和标准库，在 5 分钟内构建一个销售线索生成器

> 原文：<https://dev.to/janeth/build-a-lead-generator-in-5-minutes-with-typeform-clearbit-airtable-and-standardlibrary-86h>

[希望为您的企业创造商机](https://medium.com/@janeth_10755/build-a-lead-generator-in-5-minutes-with-typeform-clearbit-airtable-and-standard-library-2d8847846839)，或者在您从类似 Typeform 的服务中获得一些信息后对其进行鉴定？这个教程是给你的！我们将使用 Typeform 作为销售线索接收表单，然后使用 Standard Library 连接到 Clearbit API 来验证这些销售线索，并将更新后的销售线索信息存储在 AirTable 中。

[![](img/fdae6539a8a7f3246fe5a254f73e01aa.png)T2】](https://www.youtube.com/watch?v=doSjS1SR1Ss)

当销售线索提交一份类型表单时，您的 Airtable 库会立即填充 Clearbit 收集的销售线索数据。

为此，我们将使用[标准库的](https://stdlib.com)简单的以 API 为中心的云平台和 API 的在线编辑器，[code . XYZ](https://code.xyz)——如果你还不熟悉 API，不要着急！我们的意思是您将编写一点您自己的定制业务逻辑。

营销人员、客户经理、销售主管，这是为您准备的！😉

# **事先需要什么**

1x [Typeform](https://www.typeform.com/) 免费帐户-一个用于构建动态在线表单的平台，包括登录页面、投票、测验等。
1x [Clearbit](https://clearbit.com/) 免费账户——clear bit 的 Enrichment API 接收一封电子邮件，返回一个人的职业、头衔、网站、Linkedin 等等。免费账户- AirTable 是一个电子表格数据库的组合。
1x [标准库](https://stdlib.com)免费账户——一个用于构建和部署 API、链接软件工具、为 Slack 应用提供动力、自动化任务等的平台。
节省您 5 分钟(300 秒)的时间——这些工具一起使用，将节省您数小时的工作时间！

# **工作原理**🤓

当一个领导通过您的嵌入式表单提交他/她的电子邮件时，webook 将触发您在标准库上构建的 API，该 API 通过 **HTTP POST** 请求发送通知。请求体(包含来自 Clearbit 的 Enrichment API 的响应数据)将以 **JSON** 格式发送，以填充您的 Airtable 库。

所以让我们开始吧！

# **第一分钟:从你的浏览器启动 code . XYZ**

我们将通过 [Code.xyz](https://code.xyz) -由[标准库](https://stdlib.com)的团队构建的在线 API 编辑器来链接 Typeform、Clearbit 和 Airtable。您将把函数部署到标准库中，无需在本地机器上进行任何设置或安装。

登录 Code.xyz 后，点击“**社区 API 源”。**

[![](img/2864c3407f8f4e3c052276a9040bd717.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--snXdv2_---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AhM5zE24oT2HbZAb9W6iVpA.png)

找到并选择**“typeform-clear bit-air table by @ Janet HL。”**

[![](img/daaab159e912be90bf9eb5354ad7d401.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j_7D67pU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AnSLAmpmSIdmOLHWOgmkJ3g.png)

确保为您的 API 指定一个唯一的名称，例如:

[![](img/15fde5426b1d0a069a5f87abcf3b863b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CmHvXd8x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AC-bNQRRahmG9Yam48ccUYA.png)

加载后，您将被带到位于`functions`目录(文件夹)下的`__main__.js`。这是将您的 Typeform 连接到 Clearbit 的 Enrichment API 和 Airtable 的代码。一旦它被实时部署到标准库，就会生成一个 https url 端点。您需要端点在 Typeform 上设置 webhook。但是我们不要走得太远——首先，我们需要用您独特的 Clearbit 和 Airtable 键完成设置您的标准库 API。

[![](img/1240adb68c0febf72e321f8dd5bdd677.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AOIpEnPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2ApOBjISuRJVILN8NifFWTlg.png)

打开`env.json`文件。你会看到三个不同的变量:

[![](img/73e120b41dc56e4629a0f7a741a4c583.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M3lVVeA1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2A4JLUOz19Jz6inbmtoKQnBw.png)

让我们从找到您的 Clearbit API 密钥开始。

**第 2 分钟:复制并粘贴您的 Clearbit API 密钥**

首先你需要登录，或者在 https://clearbit.com/注册一个免费的 Clearbit 账户。

[![](img/fbaf02c9ca6fa340d4dcc6a0e90b6574.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jjgI71it--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2ABx13Ecsu3PWON6hnP7Uk3Q.png)

*请注意，在您的密钥下面，Clearbit 有抑制设置，将某些联系人从您的浓缩项目中排除，以满足** (GDPR)* *。*

现在返回 Code.xyz 并在`env.json.`中填充`"CLEARBIT_API_KEY":`变量，我们现在还有两个变量需要填充。

**第 3 分钟:在 Airtable 上找到您的 API 密钥**

登录或注册 Airtable 后，选择右上角的帐户图标以查看您的帐户信息。单击 generate API key，将该密钥复制并粘贴到您的`env.json`文件中的`"AIRTABLE_API_KEY": ""`中。

[![](img/1bacba6b0fdd9231783fb52d45892dab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xnTX0hF2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AlNdaApMIRho5IyPZ6X8ttw.png)

现在，对于最后一个变量，单击下面的链接，并在路由到我的 Airtable 基本示例后，选择右上角的**复制基本示例**。现在是你的基地了！

[https://airtable . com/invite/l？invited = invcdobdb 7 ygofx 7 us&intetoken = 540 e 9 AE 9d 02 ff 30 B0 c 089 aab 4 ef8 db6 d 426 CDC 42 c 61 f 67 b25 a 820 fa 98 ca 3 ce](https://airtable.com/invite/l?inviteId=invcdodb7YgOFx7uS&inviteToken=540e9ae9d02ff30b0c089aab4efa8db6d426cdcd42c61f67b25a820fa98ca3ce)

一旦你在你的工作区中添加了我的数据库，进入你的“LeadGenerator”数据库。在右上角点击**帮助**，然后选择 **API 文档。**

[![](img/9fb41569e45ec43d2cf667999c9213f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uFyD3rag--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2ATys9RsG-Yhan_rmMoGMcVg.png)

像我在截图中所做的那样，找到并选择您的 Airtable 基本密钥:

[![](img/14626c03a1af6f6c6c7b2a5f6d2a269b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--txTHNF8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AutQxzm-5_MQgjcwFNthvrA.png)

在您将您的基本键复制并粘贴到您的 env.json 中之后，您的 env.json 文件应该如下所示:

[![](img/a8776418156d464e3b619c29afc8cb46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9bAu2NUD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2A58V8YFdm5r1KIHCumdXwyw.png)

您终于可以将 API 部署到标准库中了。返回到`__main__.js`文件，点击右下角的绿色“运行”按钮。

[![](img/f068420473f0d54266a112e901404e8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8OCHTF4v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1800/1%2AuKg6qMsYPlKpV7tpH4i-8Q.png)

我们看到一个参数错误。别担心，这很正常。这个通知意味着我们期望这个 API / webhook 响应接受我们在这个测试运行中没有提供的参数(event_type 和 form_response)。它们将由 Typeform Webhook 自动提供。

注意，标准库已经在**结果**区域下自动生成了一个 API 端点 url。

[![](img/f93842ffcc57a60a2b3f232da9cec994.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7IPyAKHQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2Anv3udRNPqSF--UyxM0C6Yw.png)

复制 https 端点，我们将需要它在 Typeform 上设置 web-hook。

**第 4 分钟:在字体上设置一个 web hook**

前往 Typeform，让您的线索生成器发挥作用。

[![](img/4ab2a1c4638978800e07dd3d4e72a2c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U-i5uL2t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AQIwEoMWzwYoKhWX6x_bfmw.png)

登录或注册后，从控制面板中选择**“新类型表单”**即可。找到并选择**“联系表”**模板。

现在，让我们按原样使用这个模板；你可以随时回来修改你喜欢的风格和问题。选择顶部菜单上的**“整合”**。

[![](img/ab427df6870fe7799cd2794bd9a0cd96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1PENPQqK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AMHK4AJ3nLg52h0IJDMaIyA.png)

导航到中间上方的选项卡，选择“集成”，然后* *“web hooks”。**

将您的 API 的端点 url 输入到目的地 URL，并打开**“web hooks”**按钮。

[![](img/b0e9b4b77f709f8627bbd6d6dffb14ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kKh5mKsw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AX_yUt2_J_PiT8DdtFfYAUQ.png)

**第 5 分钟:测试你的导联发电机**

您的导联发生器已准备好测试！选择你的字体右上角的“查看”按钮。

输入有效的电子邮件进行测试，并回答示例问题。如果您已经正确链接了 Typeform、Clearbit 和 Airtable，您的 Airtable 库应该会自动填充:

[![](img/fdae6539a8a7f3246fe5a254f73e01aa.png)T2】](https://www.youtube.com/watch?v=doSjS1SR1Ss)

* *就是这样！** 🤗
感谢您花时间阅读并尝试这个！如果你觉得这个教程有帮助，请告诉我！如果您想将 Clearbit、Typeform 和 Airtable 连接到任何其他工具或软件，请联系我们——我很乐意提供帮助。请在下面的表格中留下您的姓名、电子邮件和请求，我会尽快与您联系！

[点击此处转到我的类型表单](https://janeth24.typeform.com/to/JNUy9O)

Janeth 是标准图书馆和加州大学伯克利分校 grad- go bears 的社区经理！🐻她会说西班牙语、英语、阿拉伯语和一点 JavaScript😉