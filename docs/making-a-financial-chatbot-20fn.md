# 制作一个金融聊天机器人

> 原文：<https://dev.to/devexperts/making-a-financial-chatbot-20fn>

聊天机器人无处不在。但是什么是聊天机器人呢？你怎么能建立自己的？在本文中，我们描述了几个工具和技术来帮助您入门。

## 要求

*   示例是用 Java 编程语言(Java 1.8)编写的
*   Telegram 桌面客户端版本 1.2.1 或 Telegram 移动客户端(Android、iOS)版本 4.6
*   电报机器人 API v 3.5
*   Python 2 版本 2.6.5+或 Python 3 版本 3.3+
*   Python pip 实用程序
*   对现代 Web 协议(包括 REST)的基本理解是有帮助的。

## 介绍

聊天机器人(bot)是任何使用自然语言与人类用户或其他机器人进行交流的程序。协议和 API 现在已经存在，聊天机器人可以开发并集成到社交媒体渠道，如脸书和电报。在本文中，我们将探讨开发一个简单聊天机器人的方法，它可以回答金融领域中的一个基本问题。

## 智能聊天机器人

想象一下设计一个简单的聊天机器人。你认为对话应该如何开始？如果一个机器人看到文本“你好！”然后它可能会回复“嗨！”，或者“你好！”。如果不是“你好！”，用户键入“嗨！”、“你好”、“你好？”甚至是“早上好”？如果用户输入了一个意想不到的词序，变成了“你是机器人吗？”变成“你是个机器人，不是吗？”。我们都对回复“嗯，我没听懂，请再说一遍”的机器人和设备感到沮丧但是，预测所有可能的输入很可能是一项不可能的任务。然而，技术和工具的存在使我们的机器人看起来更聪明。

## 正则表达式

正则表达式(regex)用于查找文本中的模式。虽然正则表达式的细节和用法超出了本文的范围，但是它们可以在很大的文本字符串中识别许多复杂的预定义模式。我们可以使用正则表达式来定义一组我们的机器人能够理解的“命令”。当我们收到来自用户的消息时，我们使用正则表达式来匹配我们预定义的命令。在更复杂的情况下，这些匹配允许我们从消息中提取附加信息。例如，考虑以下消息:

*   “如果我在 20161223 买了苹果会怎么样”
*   “告诉我，如果我在 20170301 购买欧元/美元，我会赚多少钱”
*   “如果我自 20130101 起拥有 100 股微软股票”

我们创建一个名为“whatif”的命令。对“假设”的回答应该是:

*   如果你在某日买入 XYZ，你每股(或每份合约)会赚(或亏)X 美元。

其中“XYZ”、“日期”和“X$”是金融工具符号、日历日期和相应的美元金额。

下面是我们的“简单”正则表达式，它可以捕获上述所有示例。

```
whatif=if\s+(?:[Ii])?\s*(?:bought|purchased|own|had bought)\s+(?:\d+)?\s*(?:share|shares|lot|lots)?s*(?:of)?\s*(?:of)?\s*(<<instrument_symbol>>)\s+(?:on|since)\s+(\d{8}) 
```

而<<instrument_symbol>>是用另一个正则表达式定义的:</instrument_symbol>

```
\<\<instrument\_symbol\>\>=[a-zA-Z0-9#\$/]{1,20} 
```

当匹配时，我们可以使用提取的信息生成模板化的响应，并通过单独的 API 检索证券价格。

## 自然语言处理

正则表达式是一个强大的工具，但是它们的健壮性有一定的限制。如果你想让机器人更自然地与用户交流呢？在这一节中，我们将探讨 IBM (Watson)和 Amazon (AWS)使用的自然语言处理(NLP)方法，这些方法也适用于所有开发人员。然后我们将使用 Apache 的 OpenNLP 库研究另一种 NLP 方法。

### IBM 沃森

IBM 长期致力于开发智能应用程序，并花时间阅读了关于深蓝和 T2 沃森的文章。幸运的是，这项技术的某些方面是免费的。首先，向 [IBM Cloud](https://www.ibm.com/cloud/) 注册，Lite 计划是免费的，应该足以探索它的功能。

#### 沃森对话

Watson Conversation 服务有助于在应用程序和用户之间建立自然的对话。Lite 计划本身将允许每月 10，000 次 API 调用，虽然这对于工业应用程序来说还不够，但对于实验来说已经足够了。

Watson Conversation web designer 应用程序易于使用，带有有用的示例、提示和教程。首先，我们为我们的应用程序创建一个新的会话服务实例(Conversation-traderBot)。每个实例都有通过 API 与服务通信所需的相关凭证。您可以从服务控制面板的管理页面查看您的凭据或将其保存为 JSON 格式。

在服务控制台屏幕上，启动 designer 工具，开始构建对话流并创建新的工作区(Trader Dashboard)。对话流的组成部分是意图和实体。每个意图代表一个机器人需要理解的问题或陈述。您将为每个已识别的意图提供响应，这些响应可以包含实体和上下文变量。实体是可以在响应中使用的用户输入的识别部分。您可以为您的对话域创建新的实体，也可以利用系统实体。可以提取的一些常见的预定义实体包括日期、位置和货币..

一旦定义了意图和实体，您就可以以对话的形式构建对话流了。在对话设计器中，您可以配置从初始意图到最终意图的所有转换状态。当对话准备好了，你可以在设计器里面测试它。最后，完成后，您可以将工作区连接到其他 Watson 服务或外部服务，如脸书或 Slack。

现在，我们可以在工作区与 Watson 进行简单的对话。

```
 Ask watson: hello
 Dec 29, 2017 5:45:53 PM okhttp3.internal.platform.Platform log
 INFO: --> POST https://gateway.watsonplatform.net/conversation/api/v1/workspaces/0b31dca0-49a0-4f41-bcc9-78618bad6dee/message?version=2017-05-26 http/1.1 (307-byte body)
 Dec 29, 2017 5:45:53 PM okhttp3.internal.platform.Platform log
 INFO: <-- 200 OK https://gateway.watsonplatform.net/conversation/api/v1/workspaces/0b31dca0-49a0-4f41-bcc9-78618bad6dee/message?version=2017-05-26 (231ms, unknown-length body)
 Hello! 
```

如果我们问沃森一些它没有被训练理解的东西，它会用我们预先定义的“否定”答案来回答:

```
 Ask watson: How is it going?
 Dec 29, 2017 5:54:39 PM okhttp3.internal.platform.Platform log
 INFO: --> POST https://gateway.watsonplatform.net/conversation/api/v1/workspaces/0b31dca0-49a0-4f41-bcc9-78618bad6dee/message?version=2017-05-26 http/1.1 (388-byte body)
 Dec 29, 2017 5:54:39 PM okhttp3.internal.platform.Platform log
 INFO: <-- 200 OK https://gateway.watsonplatform.net/conversation/api/v1/workspaces/0b31dca0-49a0-4f41-bcc9-78618bad6dee/message?version=2017-05-26 (232ms, unknown-length body)
 I didn't understand. You can try rephrasing. 
```

在 Watson Conversation 服务中创建聊天机器人对话最适合于结构良好的对话，如酒店预订或开设新的交易账户。如果您的目标是理解与您的应用程序的自由形式的用户对话，IBM Watson 的另一项服务可能会有所帮助——**自然语言理解** (NLU)。

#### 自然语言理解

NLU 服务试图从内容中提取元数据。这种元数据可以捕获关键字、语义角色甚至消息的情感。通过 Lite 计划，您可以使用 Watson Knowledge Studio 工具，每月使用多达 30，000 个 NLU 项目。

为了说明 NLU 服务，让我们向 IBM Watson 发送以下问题:

```
What if I invested $1000 in IBM ten years ago? 
```

POST 请求如下所示:

```
https://gateway.watsonplatform.net/natural-language-understanding/api/v1/analyze?version=2017-02-27&text=What if I invested $1000 in IBM ten years ago?&features=semantic_roles,entities 
```

请注意，我们省略了在创建 NLU 实例时获得的自己的 AUTH 参数。我们向 NLU 请求了语义角色和实体特征来分析我们的数据。以下是 IBM Watson 以 JSON 格式给出的回复:

#### **NLU 回应**

```
{
    "usage": {
        "text_units": 1,
        "text_characters": 46,
        "features": 2
    },
    "semantic_roles": [
        {
            "subject": {
                "text": "I"
            },
            "sentence": "What if I invested $1000 in IBM ten years ago?",
            "object": {
                "text": "$1000"
            },
            "action": {
                "verb": {
                    "text": "invest",
                    "tense": "past"
                },
                "text": "invested",
                "normalized": "invest"
            }
        }
    ],
    "language": "en",
    "entities": [
        {
            "type": "Company",
            "text": "IBM",
            "relevance": 0.33,
            "count": 1
        },
        {
            "type": "Quantity",
            "text": "ten years",
            "relevance": 0.33,
            "count": 1
        },
        {
            "type": "Quantity",
            "text": "$1000",
            "relevance": 0.33,
            "count": 1
        }
    ]
} 
```

答案确定了三个 NLU 项目——一个文本单元和两个特征单元。NLU 服务从文本中提取了一个主语、一个宾语和一个动作。实体特征提取了一个公司“IBM”，以及数量“1000 美元”和“十年”。遗憾的是，日期提取目前还不是这项服务的一项功能。尽管如此，沃森 NLU 服务提供的信息可以在我们的应用程序中使用，以了解用户的意图。

### 亚马逊 AWS

对于那些熟悉亚马逊 Alexa 个人助理的人来说，他们在自然语言理解方面也取得了重大进展，这应该不足为奇。一个支持语言理解的亚马逊网络服务(AWS)是 Amazon Lex。注册 AWS 免费层可以免费使用 12 个月的 AWS。第一次注册时，您需要用 1 美元的信用卡费用来验证您的帐户。请注意，如果您超出 AWS 免费等级的限制，您可能会被收取月租费。

#### 亚马逊 Lex

Amazon Lex 为开发人员提供了创建对话聊天机器人的能力。与 IBM Watson 对话类似，您可以在 Amazon Lex 控制台中指定基本的对话流。Amazon Lex 管理对话并动态调整响应。首先，让我们在 Amazon Lex 控制台中创建一个名为 TraderAssistant 的 Lex bot。您可以通过添加意图来构建一个对话框。意图捕捉用户想要的一般动作，并提供标识和存储与该意图相关的用户上下文的槽。让我们为 TraderAssistant bot 创建并配置一个“MakeAnInvestment”意图。此意图的目的是指导用户完成投资过程。为简单起见，我们假设用户选择外汇类型的投资。为了启动这个意愿，我们将支持以下话语:

```
I would like to invest; I would like to trade; I would like to buy; I would like to sell; 
```

为了提取所需的数据以实现意图，我们将创建并添加以下槽:

| 需要 | 名字 | 插槽类型 | 提示 |
| --- | --- | --- | --- |
| 是 | 仪器仪表 | 仪器仪表 | 你想做什么类型的投资？ |
| 是 | 标志 | 外汇符号 | 您想交易哪种货币对？ |
| 是 | 数量 | 亚马逊。数字 | 您想向{Symbol}投资多少钱？ |

仪器类型和外汇符号是我们创建的自定义插槽类型。在配置新插槽时，您可以提供同义词和插槽解析的列表，以限制或扩展插槽值。对于 InstrumentType 槽，我们提供了提示文本“您想进行哪种类型的投资？”以及“我想投资{工具类型}”作为相应的话语之一。如果用户的话语被识别，则填充 InstrumentType 槽，并且对话移动到下一个槽/提示。

在填充了一个 intent 的槽之后，您将需要使用 Amazon Lex 控制台中的 build 按钮来构建它。如果没有错误，您可以在控制台中测试您的意图。如果您满意，您可以发布它，在那里您将被提示为您的构建添加一个别名。发布完成后，你可以从不同的平台连接到 intent，包括脸书、Slack、Twilio SMS 和 Kik。还有 Android 和 iOS 亚马逊 Lex SDKs 可以从移动应用程序连接到一个 bot。我们将使用 Amazon AWS SDK for Java 来连接 TraderAssistant 聊天机器人及其 MakeAnInvestment 意图。

在您的计算机上安装 AWS 服务最简单的方法是安装 AWS 命令行界面(AWS CLI)工具。您将需要 Python v. 2.6.5+或 Python 3 v. 3.3+以及 Python 的 **pip** 实用程序。用 **pip** 安装 AWS CLI 很容易:

```
$ **pip install awscli --upgrade --user** 
```

您可以通过运行 AWS–version 命令来验证 AWS CLI 安装是否正确:

```
$ aws --version aws-cli/1.11.186 Python/2.7.10 Darwin/17.3.0 botocore/1.7.44 
```

您需要提供您在 AWS web 控制台中创建的 AWS 访问密钥 id 和秘密访问密钥。以下是我们机器的默认配置文件配置:

```
$ aws configure AWS Access Key ID [\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*KSHQ]: AWS Secret Access Key [\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*v7XT]: Default region name [us-east-1]: Default output format [json]: 
```

再一次，就像在我们的 IBM Watson 示例中一样，我们为 chatbot 服务实现了简单的接口，它将来自用户的消息作为字符串发送给服务，并将响应作为字符串列表返回。下面是我们实用程序的控制台输出，显示了与 Amazon Lex 的对话:

```
 Ask amazon: I would like to make an investment
 What type of investment would you like to make?
 Ask amazon: forex
 Which currency pair would you like to trade?
 Ask amazon: EURUSD
 Would you like to buy or sell EURUSD?
 Ask amazon: sell
 How much money do you want to invest in EURUSD?
 Ask amazon: 1000 dollars
 Okay, I will Sell EURUSD with 1000 of your money. Does this sound okay?
 Ask amazon: yes 
```

我们上面打印的文本消息响应是 Amazon Lex 发送回应用程序的 PostTextResult 对象的一部分。让我们检查结果对象的内容，它对应于我们对话的最后三个阶段:

```
 Ask amazon: sell
 Result: {IntentName: MakeInvestment,Slots: {InvestmentType=Sell, Symbol=EURUSD, InstrumentType=Forex, InvestmentAmount=null},
 Message: How much money do you want to invest in EURUSD?,DialogState: ElicitSlot,SlotToElicit: InvestmentAmount,}
 How much money do you want to invest in EURUSD?
 Ask amazon: 1000
 Result: {IntentName: MakeInvestment,Slots: {InvestmentType=Sell, Symbol=EURUSD, InstrumentType=Forex, InvestmentAmount=1000},
 Message: Okay, I will Sell EURUSD with 1000 of your money. Does this sound okay?,DialogState: ConfirmIntent,}
 Okay, I will Sell EURUSD with 1000 of your money. Does this sound okay?
 Ask amazon: yes
 Result: {IntentName: MakeInvestment,Slots: {InvestmentType=Sell, Symbol=EURUSD, InstrumentType=Forex, InvestmentAmount=1000},
 DialogState: ReadyForFulfillment,} 
```

当对话状态为“已准备好完成”,我们就拥有了继续投资决策所需的所有信息(在我们的简单示例中)。

除了通过短信与 Amazon Lex 交流，你还可以发送语音命令——录制的音频文件。

### Apache OpenNLP

如果您有兴趣从头开始为您的应用程序添加 NLP 功能，有许多工具和库可供使用。Python 可能拥有最大的现有库集来继续 Java，我们将研究 Apache OpenNLP toolkit 的易用性、功能和社区支持。

Apache OpenNLP 库支持最常见的 NLP 任务，并为几种语言预建了模型。OpenNLP 通过几个组件提供处理管道，包括:

*   语言检测器
*   句子检测器
*   标记器
*   查找器 name
*   文档分类器
*   词性标注器
*   lemmatizer
*   改变者
*   句法分析程序

每个组件从它的前一个组件获取输入，并为它的后一个组件生成输出。

下载并安装 [Apache OpenNLP CLI](http://opennlp.apache.org/download.html) 后，通过定义以下系统变量 JAVA_CMD、JAVA_HOME 和 OPENNLP_HOME，然后将$OPENNLP_HOME/bin 或%OPENNLP_HOME\bin%添加到 PATH 变量，在您的机器上配置 OpenNLP。然后，您可以从命令提示符下启动 CLI 工具。

```
$ opennlp 
```

该脚本将打印当前版本、用法和可用工具列表。

接下来下载[预训英模](http://opennlp.apache.org/models.html)。我们将假设英语是我们的默认语言，但其他选项是可用的。此外，我们将包括其他模型来支持我们特定类型的金融对话。

我们的应用程序的目标是从用英语写的句子中“理解”用户的投资意图。为了“理解”一个意图，我们需要提取一个主题、一个对象、一个动作和相关的其他实体，比如日期、数量和公司。我们应该支持的一个例句是:

```
What if I bought 100 shares of Microsoft 5 years ago 
```

我们希望从这句话中提取主语(“我”)、宾语(“100 股微软”)和动作(“购买”)，同时检测实体公司(“微软”)、日期(“5 年前”)和数量(“100 股”)。

让我们追踪我们的示例问题通过每个模型的过程。

第一个也是最简单的工具是记号赋予器，它把句子分解成记号，记号可以是单词、标点、数字等等。

接下来是命名实体识别工具(NER ),它检测令牌中的名称、日期和组织。在这个例子中，只有微软被识别和标记。通用 NER 模型是有限的，有时会遗漏与金融领域相关的关键术语，我们的结果可能会通过使用更多示例来训练该模型而得到改善。

词性标注器(POS)从 tokenizer 获取输出，并根据标记和标记的上下文用相应的单词类型标记标记。OpenNLP POS Tagger 使用概率模型来预测正确的标签。

组块器将 POS 输出分成单词的句法相关部分，如名词组、动词组。

最后，将所有这些内容和上下文信息发送到决策过程中。我们的 TraderNlpDecision 类需要来自投资域的输入文本。在 NLP 工具的帮助下，我们可以将这些关联到我们投资意图的正确方面。在处理交易者 NLP 决策后，产生以下投资意向:

**投资意向**

完整意图:(得分:96)
问题:什么
subject:I
行动:已购买
OBJ:微软
数量:100 股
日期:2013-01-12
额外:目的:
过去投资

还不错！有了这些信息，我们的应用程序可以用图表、特定日期或时间间隔的报价来响应用户，并计算预期的盈利/亏损(PL)。

## 摘要

在这篇文章中，我们描述了几种帮助聊天机器人与用户交互的技术。我们从正则表达式开始，对于简短的查询和命令，正则表达式是一个强大而可靠的工具。我们研究了 NLP，以更好地理解和处理复杂的查询和更长的句子，以及如何通过 IBM (Watson)和亚马逊(Amazon)基于云的工具来实现这一点。使用 Watson 和 AWS，我们可以创建遵循良好定义的场景或脚本的聊天机器人。最后，我们描述了如何借助 Apache Open NLP 库在 bot 应用程序中实现 NLP 处理。我们希望本文中的信息是有用的，可以帮助您决定在您的应用程序中使用哪种技术。

## 参考和链接

*   电报机器人平台:[https://telegram.org/blog/bot-revolution
    T2】](https://telegram.org/blog/bot-revolution)
*   IBM 沃森:[https://www.ibm.com/watson/](https://www.ibm.com/watson/) 
*   亚马逊 AWS:[https://aws.amazon.com](https://aws.amazon.com)
*   Apache OpenNLP:[https://opennlp.apache.org](https://opennlp.apache.org)

[![Dmitry Tsyganov, senior software engineer at Devexperts](../Images/d291d0276084205e427496a3461e7656.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kjQ2Qe1M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.devexperts.com/wp-content/uploads/2018/05/Tsyganov-282x300.jpg)
*Devexperts*的高级软件工程师 Dmitry Tsyganov