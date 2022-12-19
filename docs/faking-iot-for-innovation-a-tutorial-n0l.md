# 为创新伪造物联网(教程)

> 原文：<https://dev.to/pofigster/faking-iot-for-innovation-a-tutorial-n0l>

# 这个为什么

在过去一周的工作中，我们举办了一场名为“创新周”的黑客马拉松式的活动。我和一个朋友决定对客户系统进行一些预测性维护。主要问题？我们没有他们的数据。我们甚至没有用我们的数据做这件事所需的一切。此外，我们如何获得他们的数据？当然是物联网(IoT)测量系统。因此，在一周内，我需要编写一个传热系统模拟器，然后让测量点与仪表板进行通信。这就是伪造物联网的原因——我不想购买实际的物联网测量系统，并试图将它们连接到实际的传热系统。即使我可以，我也不能足够快地获得数据来进行预测性维护，所以我需要一种方法来模拟设备，这样我就可以演示一个工作原型。我选择使用 node.js 是因为我试图学习更多的 js，我认为更多的是服务器端而不是客户端。

为什么有这样的教程？事实证明，对我这样的人来说，整个过程其实很难。有一些教程是关于如何连接一个 Raspberry Pi 的，但是大多数都太老了，AWS 甚至 SDK 中的一些步骤都是不相关的和过时的，所以我必须弄清楚它，我想为未来的我和其他像我一样的人记录下来。

# 假设

*   您已经有一个 AWS 帐户
*   你可以使用 node.js
*   你并不担心 AWS 中有过于宽松的 IAM 角色
*   您至少对从提供商(DigitalOcean、AWS 等)那里提供服务器感到相当满意

# 该如何如何

## 步骤 1 -开始使用 AWS IoT

您需要做的第一件事是登录 AWS 控制台并导航到物联网核心资源。如果你是第一次，你的屏幕应该看起来不一样，但我们的目标是*创建一个‘东西’*，或者，注册一个物联网设备。不要担心，你不一定要有一个设备来实现这一点，这只是做好准备。

您需要进入“管理”>“事物”部分，然后单击“创建”。

[![step 1](../Images/0c2ddf5ed38cd1883afbde68d2280e56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qXbgAZOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4x0mp86xp0be15s9yqmu.png)

我们只是想做一个单一的 AWS 物联网东西，所以这就是我们要点击的。

[![step 2](../Images/df95e88a2feed559f24f81e5a8285350.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v1Nr19x---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yvkt80rou29stjhy3fp4.png)

在下一个屏幕上，有许多可能要填写的内容。除了顶部的名字，你可以安全地忽略所有的名字。我把这个设备命名为“开发教程”。你可能想知道，“所有其他的东西是做什么的？”我能告诉你的最好的答案是，它们是帮助你组织东西的字段。我工作的公司使用 AWS，有这么多的人在做这么多不同的事情，标签和群组等是必不可少的。我做一些概念验证的事情，做完后就把它们都扔了，所以我忽略了这一切。

[![step 3](../Images/0508331ed3fa734e0281bdc0e1ad3f02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OunAr-5g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/smdcgf2i23idb3gwguw5.png)

下一步很重要，我们需要创建证书，允许我们尚不存在的物联网设备向 AWS 标识自己。你不会想弄糟接下来的步骤的。您需要点击“创建证书”来生成 3 个不同的文件，我们需要下载并复制到我们的服务器。

[![step 4](../Images/3f43c94d3050eb0c13f2a1209f192733.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vPlgkKHI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/klsh18a7uo8ks17bxbxh.png)

您可能会看到一个方框闪烁，告诉您设备已经创建，但不要认为您已经完成了。您需要下载表中的三个文件*和*下载一个根 CA(证书颁发机构)文件。前三个链接都将下载实际文件，你*必须*现在下载它们，否则它们将永远丢失。这是你获得这些证书的唯一机会。别搞砸了。最后一个链接并不直接下载文件。

[![step 5](../Images/30f68fdd232ea88e96b53aaf466d9dce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HNjJbH_u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jghjjq819njkij04s8r2.png)

加载的新页面有许多链接。你想要的是 Amazon 根 CA 1 文件，一个 RSA 2048 位密钥。继续点击链接。

[![step 6](../Images/063062f9ff4555f09de0784f30ab98ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UwGbawlY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u44ngdmpi33pasfgrp54.png)

下载完所有四个文件后，请务必点击物联网屏幕上显示“证书已创建”的“激活”。激活后，单击“附加策略”。

还记得我说过我认为你可以接受宽容的角色之类的吗？嗯，在这里我只是选择了全局策略，允许这个东西在*任何*资源上对物联网做*任何*事情。从长远来看，这可能不是一个好主意，但这是教程告诉你要做的:)

[![step 7](../Images/5717e82dc7f9f5d2aa76757d9f92fdc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G0PEodsw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ugd4p6mfr7t6y7ws213k.png)

恭喜你。你在 IoT 注册了一个东西！

[![step 8](../Images/cc9d3061da72ab26f8efcbe030322309.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I2T0cZos--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ypm9jz493h3a8vmkcbvj.png)

## 第二步——让一台服务器伪装成物联网设备

首先，给自己买一台运行 Linux 的服务器。我使用 [DigitalOcean](https://m.do.co/c/92b8439f5af7) (注意，那是我的个人推荐链接)，因为 5 美元/月的像样的小盒子很棒。我也选择了用 Ubuntu。

接下来，连接到盒子上，我们就可以安装东西了。

*   [安装 node.js](https://nodejs.org/en/download/package-manager/)
*   [安装 AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/awscli-install-linux.html) (在 Ubuntu 18.10 上我用`apt install awscli`没有问题)
*   为 JS 安装 AWS 物联网设备 SDK，`npm i aws-iot-device-sdk`
*   配置 AWS 凭证`aws configure`

我选择制作一个名为“iweek”的项目文件夹。无论你想在哪里工作，创建一个名为“certs”的目录，并上传我们之前下载的 4 个证书。为了便于复制/粘贴，将文件重命名为:

*   ...-certificate . PEM . CRT > certificate . PEM . CRT
*   ...-private . PEM . key > private . PEM . key
*   ...-public.pem.key > public.pem.key
*   AmazonRootCA1.pem > root-CA.crt

我们需要确定的最后一件事是我们将连接到的自定义主机端点。这是通过 AWS CLI `aws iot describe-endpoint --endpoint-type 'iot:Data-ATS' --region us-east-1`完成的，确保将区域更新到您设置物联网的任何区域。复制响应中端点地址的内容，我们马上就需要它。

现在我们准备创建 JavaScript 文件，以确保一切都可以正常连接。如果你的目录结构和我的一样，文件应该保存到`~/iweek/test_iot.js`中，由`~/iweek/certs/`持有证书。

[![folder structure](../Images/06d1d0c4b72f52f63588ac43171be69c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8RRmiV1G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x88vu5qk4d3xe82mh1zi.png)

在文件的顶部，我们需要加载物联网设备 SDK，然后初始化我们的设备。我们还没有对这个设备做任何事情，只是定义它看起来像什么。“clientId”是一个字符串，用于标识正在连接的内容。它不一定要和你的事物名称相匹配，所以它可以是愚蠢的或者有意义的。

```
var awsIot = require('aws-iot-device-sdk');

var device = awsIot.device({
   keyPath: './certs/private.pem.key',
  certPath: './certs/certificate.pem.crt',
    caPath: './certs/root-CA.crt',
  clientId: 'first-try',
      host: 'CUSTOM HOST ENDPOINT'
}); 
```

在文件底部添加一些说明，以便设备在连接到物联网核心时遵循。

```
device
  .on('connect', function() {
    console.log('connect');
  }); 
```

在这一点上，我们将启动一个终端`cd iweek`和`node test_iot.js`,在我们点击 enter 键后，我们应该在我们的 STDOUT 中看到单词“connect ”,而没有新的提示。这是因为我们的代码没有尽头，设备是连接的，只是不做任何事情。所以你需要发送一个取消代码到你的终端。

现在我们可以试着发送信息。

我们现在将修改代码的“on connect”部分，以订阅主题并发布到该主题。当我们订阅和发布时，我们这样做是为了一个主题。主题只是一个名字，可以是你想要的任何东西。记住我们发布到的名称很重要，因为这是我们以后检索数据的方式。

```
device
  .on('connect', function() {
    console.log('connect');
    device.subscribe('dev_to_test', function(error, result) {
      console.log(result);
     });
    device.publish('dev_to_test', JSON.stringify({ 'message': 'hi there!', 'points': 168}));
  }); 
```

我们还想添加一个代码块，以便每当有消息添加到主题中时发出警告。现在，当设备接收到消息时，我们将把消息内容打印到 STDOUT。

```
device
  .on('message', function(topic, payload) {
    console.log('message', topic, payload.toString());
  }); 
```

回到我们的终端，我们运行`node test_iot.js`并得到一些消息。首先，我们的“连接”告诉我们已经成功连接。接下来，我们得到信息，告诉我们已经订阅了主题“dev_to_test ”,最后我们看到将我们的消息发布到该主题的结果。

```
connect
[ { topic: 'dev_to_test', qos: 0 } ]
message dev_to_test {"message":"hi there!","points":168} 
```

## 步骤 3 -收集通过物联网发送的数据

这对我来说是最难的一步，这是我找到的大多数教程崩溃的地方，我不得不独自解决它。我们要尝试和做的是让物联网核心将某个主题的传入数据推送到 Kinesis Firehose，后者应该会将结果发送到 S3。

首先我们需要安装 Kinesis 消防水管。因此，我们将导航到该服务，单击“Data Firehose”选项卡和“Create delivery stream”

[![step10](../Images/98a1e28bccf82db25d47892f6c4eb099.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5n3OwEd_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u5u3yaoetc9hmrbxfsiz.png)

在“创建”菜单中，我们需要给出一个名称，最重要的是，确保您选择了“直接上传或其他来源”。这是从 IoT 到 Kinesis 的最简单的接口，而不是通过数据流。单击页面底部的下一步。

[![step11](../Images/9c75ecb188747c3e34605e133a1e3e2c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fhtfPtam--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bh9hbgobifk0mbpr4tji.png)

下一页有几个选项。如果你想处理物联网提交的数据，你可以触发 AWS Lambda。如果愿意，还可以将记录格式转换成类似 Apache 的 parquet 的格式。我选择禁用这两个特性，因为我只提交简单的数据。

最后，我们需要选择目的地。Firehose 将向 S3、红移、Elasticsearch 或 Splunk 传输数据。在这个演示中，我们将东西存储到 S3，因为存储简单，而且在 S3 数据上应用 Athena 很容易。S3 是默认选择，因此向下滚动以选择您想要用于存储的 S3 存储桶(或者，单击“新建”以创建新存储桶)，然后可选地为文件指定前缀(S3 的 psuedo 文件夹结构)。完成后，单击“下一步”。

[![step12](../Images/b3599a2a40862623328b40bc9e194320.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XZaN80P2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ai79046n0n3spwdi7mdr.png)

设置消防软管的最后一步是配置它。这一页最重要的部分是 S3 缓冲条件。Firehose 将接收并存储数据，直到满足缓冲条件，然后将数据推送到 S3。这里的默认值是 5MB 或 5 分钟，我将我的设置为 1MB 或 1 分钟(最小值),因为我们不打算在本教程中发送大量数据，我也不想永远等待它的到来。我们发送的数据不是很大，所以我们不需要压缩，数据也不敏感，所以我们不需要加密，但这些选项是存在的。我们需要一个具有正确权限的 IAM 角色。

[![step13](../Images/1d1949713ce6196252835d1316e54370.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M0mi9Kat--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gm4v59c61fwl111zrhkq.png)

我有一个名为 *firehose_delivery_role* 的 IAM 角色，我在这里选择了它，并创建了一个新的角色策略，以确保它可以访问我新创建的 S3 存储桶。完成此屏幕中的操作后，单击下一步。

[![iamstuff](../Images/f221211ec221c2738d8ad79eb0dd120f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xNX2IXy7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/anvnwkubi9oh03n0lia3.png)

在最后一个屏幕上，确保您的选择看起来不错，然后单击“创建交付流”。在创建交付流的同时，我们需要返回到我们的物联网核心页面(现在充满了成功的连接和圆环图！)并点击“行动”选项卡，然后点击“创建”按钮。

[![step14](../Images/1223624599ec27ebde24cb68bdc34708.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UIOtBXV0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ypahp6hyc6drszh3o2n0.png)

在 create 屏幕上，我们需要给我们的规则起一个名字，最好是一个描述。我已经将我的规则命名为 *dev_to_rule* 。接下来，我们需要编写一个 SQL 查询来声明我们希望通过规则传递哪些数据。这就像一个基本的 SQL 查询，你可以使用‘where’语句等等。如果我们传递复杂的数据，我们甚至可以使用' select '语句来过滤要保留的列。但是在这里，我们只是想传递它，所以查询如下所示。注意，表名是我们向其传递消息的主题。

```
select * from 'dev_to_test' 
```

[![step15](../Images/208b12050a77df807516fb5b8d23a454.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LTGogeem--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vjvexvfnz7ruv0my7bh4.png)

现在我们需要添加一个动作。我们点击“添加操作”按钮，弹出一个巨大的选项列表。我们希望将数据传递给我们的 Kinesis Firehose 流。你可能会看到我们可以直接将消息存储在 S3 桶中，这是真的，但是通过利用 Firehose，我们有了更多的选择(lambda 处理，其他目的地，等等。).因此，我们做出适当的选择，然后单击“配置操作”

[![step16](../Images/1f01bcce85ad7831772af30772846674.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mLb16ycs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zuppyvz0536oust9qetg.png)

在配置时，我们希望选择我们创建的流， *dev_to_firehose* ，并为收到的消息选择分隔符。因为 Firehose 会建立一个缓冲区，所以多个消息会在同一个文件中。我选择了一个新的行来提高可读性。最后，我们想要创建一个新的 IAM 角色，并为其命名，在本例中为 *dev_to_iot_to_firehose* 。创建角色后，您需要点击刷新按钮，并从下拉列表中选择它。最后，点击“更新角色”以确保它已应用，然后单击“添加操作”。

[![step17](../Images/47d97dca19fdc6995d09865dc86a4f29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dnS9OKje--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8nu7rs4w1jidulg4v1jc.png)

这将带我们回到创建规则屏幕，因此我们将按“创建规则”。
随着规则的创建，我们现在回到我们的服务器，从终端运行`node test_iot.js`来触发消息被发送到我们的通道。我们现在需要耐心等待(1 分钟),因为消防软管中的缓冲液正在增加。一分钟后，我们可以转到`S3 > dev-to-tutorial / iot / year / month / day / hour`并看到我们已经创建了一个文件！

[![success](../Images/0a2408e8d0cdcbffdcd3e177bacb343c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fYSkSTe6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bsu45kvk09y5vhhl9jvn.png)

我们可以下载该文件，并看到我们在 JSON 结构中有包含我们发送的消息的文本。成功！

[![contents](../Images/e562d421b1df6e465e2ab5eea68d8b56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2XMshIDO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gq5lmnayqf2vzkxe28jo.png)

# 创新周成果

希望本教程能够帮助您开始使用 node.js 支持的物联网设备与 AWS 服务进行通信。我在这里的努力加上机器学习算法和实时仪表板的结果为我的团队赢得了第一名，这非常令人兴奋。谢谢你让我与你分享这个！