# 真人网络认证，第二部分

> 原文：<https://dev.to/ghardin137/web-authentication-for-actual-humans-part-two-ea6>

亲爱的读者，欢迎回来！你会记得[上次](https://dev.to/ghardin137/web-authentication-for-actual-humans-part-one-36ho)我们讨论了处理认证和授权的基本概念。今天，我们将更深入地讨论身份验证的概念，看看这些特定概念的一些常见实现，以及可能遇到的陷阱。我们还将描述针对每种类型的一些攻击。

## 认证类型

上次我们将认证定义为*您的用户证明他们就是他们所说的*。您可以将这些方法分成几种不同的类型。大多数专业人士会声称有三种主要的认证类型。

### 你知道的事情

第一种也是最常见的认证方法是使用您知道的东西。一些常见的例子如下:

*   用户名
*   密码
*   安全问题
*   钉
*   社会安全号码

这种方法非常普遍，因为开发人员很容易实现。作为作者，您不必担心拥有硬件或使用外部软件来验证您的用户。

十有八九，你已经从你的用户那里收集了一些数据。因此，这是一个收集多一点的问题，这样你就可以验证他们是谁。

用户通常也熟悉这种形式的身份验证，不需要太多关于其工作原理的培训。

让我们回头看看上一篇文章中的夜总会例子。

海豹俱乐部的保镖花了太多时间来验证每一个游客，看他们是否是会员。一天晚上，你的保镖想出了一个绝妙的主意，他要向每个会员询问他昨晚想出的密码。

一切都进行得很顺利，直到有人路过，这时你的一个成员给了保镖密码。

Shady Johnson 听到后决定尝试一下。他等了几分钟，然后走到门口，说出了他无意中听到的密码。你的保镖是个聪明的家伙，他心里想“我不认识这个人，但他有密码，所以他一定是会员。”他让 Shady 进入俱乐部，现在你的俱乐部里有了一个未经授权的客人。

如你所见，这种方法有一些缺点。人们可以共享他们的密码，或者让他们被盗。

我们都是人，很少人有完美的记忆力。因此，您会发现用户将在许多应用程序中重用相同的身份验证数据。这进一步暴露了使用这种方法的弱点，因为它不要求您的应用程序直接受到损害，攻击者就可以非法访问它。

这种形式的另一个缺点是妥协有点微不足道。攻击者需要做的只是通过某种方法收集您用于身份验证的数据。Shady Johnson 有很多方法可以做到这一点。他可以使用复杂的方法，比如在你的电脑上安装一个键盘记录器，在你的用户登录时进行肩窥，甚至简单地向用户询问信息。

甚至有研究表明，很大一部分用户会为了一点巧克力而放弃他们的登录凭证。

由于这些弱点，技术世界已经开始转向其他形式的认证。

### 你拥有的东西

这种身份验证方法通过使用您实际拥有的东西来验证您。常见的例子有:

*   物理钥匙
*   你的手机
*   RFID 徽章
*   磁条，如信用卡上的磁条。
*   一种基于共享算法生成随机字符串的设备，通常称为“令牌”或“智能卡”

如果你还记得以前的帖子，海豹俱乐部开始分发 RFID 手镯，用来识别任何特定的成员。这加快了会员的进入速度，让你的酒保更容易核实会员是否可以购买饮料，并为你的经理腾出时间来管理俱乐部的其他部分。不幸的是，这些对员工工作流程和安全感的改善是有代价的。这些手镯所需的硬件相当昂贵，每个接入点大约 500-1000 美元，每个手镯的价格可能超过 10 美元，除非你一次购买几千或几万个。如果你刚刚起步，这可能是一笔不小的开支。

你的会员现在也不得不担心跟踪一个新的手镯。如果他们忘记他们的手镯，他们不能进入你的俱乐部。如果他们把它弄丢了，那么你现在就有了一个手镯，任何人都可以用它进入你的俱乐部，在他们不知情的情况下进入合法会员的酒吧。

一般来说，这些类型的身份验证都需要一些额外的硬件，这些硬件可能是您的用户已经拥有的，也可能不是，一般来说，还需要一些额外的硬件和软件。

它们也不是完全安全的，除了恶意用户窃取实际的物理密钥之外，在 RFID 或其他类似设备的情况下，有人可能会在短距离内触发您的密钥，并将其数据复制到新的密钥上，然后以这种方式使用它。

### 你是什么东西

这种类型的认证使用的是你作为一个人所独有的东西。常见的例子有:

*   指纹扫描仪
*   视网膜扫描仪
*   虹膜识别
*   面部识别
*   声音识别

最后一种主流类型的身份验证依赖于根据用户的身体特征对您进行身份验证。这些通常更加难以模仿，但并非不可能。它们还需要更昂贵的硬件和软件来使用。

作为一个俱乐部老板，你厌倦了保镖要求所有顾客出示身份证，所以你决定只提前检查身份证，并使用消费级扫描仪记录顾客的指纹。这无疑加快了你的员工的进程。你觉得你买了一件很棒的东西，并让你的保镖去做其他的工作。一切都很顺利，直到你开始收到顾客的投诉，他们被收取晚上的饮料费，他们可以证明他们不在俱乐部。经过调查，你确定一些坏演员一直在做指纹拷贝，这是一项不可能完成的任务。你禁止那些客人并让你的保镖小心他们。几周后，你又收到了更多的投诉，这次的罪魁祸首是无意的。经过一些研究，您确定消费级扫描仪只对指纹进行部分匹配。这意味着很有可能出现假阳性比较。你还要感谢你的幸运星，没有人被切掉手指直接使用。

一般来说，这些类型的身份验证是相当安全的，您永远不必担心您的用户丢失或被盗。但是您可以看到，也有可能对它们进行折衷。

所涉及的硬件在现场实施起来也相当昂贵。你可能会问如今智能手机内置了过多的指纹和面部识别系统。使用这些系统可以将一些成本转移给用户，但是他们仍然需要为访问相关的 API 付费。他们也不一定很强壮。

* * *

许多安全专业人员会停止列出不同类型的身份验证。我喜欢记住还有第四种身份认证，它和所有这些认证一样有用。

### 你做的事情

这种身份验证方式在含义和实现方式上有点模糊。有很多潜在的方法来思考这个问题。例如，您可能必须去特定的地方才能使用特定的系统，或者您可能必须同时按下两个按钮才能开始一个过程。

如果你看过原版电影《碟中谍》，伊森·亨特必须潜入中情局总部才能进入一个数据库，而这个数据库只能通过一个安全房间里的一台电脑来访问。这部电影中的中情局采用了这种认证方式来保护这些数据。

我个人认为 oAuth 也是这种认证的一种实现。你可能会想“但是我必须输入用户名和密码？”这肯定是对的，但是您没有直接登录到应用程序。您正在使用您的用户名和密码向您和应用程序都信任的第三方进行验证。您向信任方验证的应用程序证明您是您所说的那个人，因为它信任的第三方说您是。

你可能会问自己“那我说的对吗？我是该第三方的用户”但请记住，这种形式的认证依赖于你的身体特征，而你的身体特征几乎不可能被模仿。此外，您可能会使用这些东西来验证第三方，但这种方法的重要部分是您使用第三方。这就是为什么我认为 OAuth 是一种“您做的事情”的认证形式。

## 所以他们都没有安全感？

没错，所有这些方法都存在一定程度的易受攻击性。你知道什么是最脆弱的，什么是最不脆弱的。任何身份认证工作流的目标都是为应用程序选择适当的风险量及其数据的敏感程度。如果你在想“我的数据非常敏感。我不喜欢我的应用程序有这么多漏洞。”你不会孤单。为了克服这一点，你可以用下一个话题来减轻你的恐惧。

### 双/多因素认证

降低用户受到危害的可能性的一种方法是，您可以在单个身份验证工作流程中结合多种方法。你总是希望它们是不同类型的认证，比如你知道的和你有的。在该示例中，这可以表现为与物理令牌相结合用户名/密码。如果你更加偏执，你可以在等式中加入更多的因素。在单个工作流中使用多种身份认证方法降低了所有方法同时受到攻击的可能性。

也就是说，有两种常见形式的双因素身份认证没有人们想象的那么安全。许多地方会给你发电子邮件或短信(SMS ),以确认你就是你所说的那个人。与你拥有的其他形式的东西相比，这两种方法都很容易妥协。

## 丢了怎么办？

我们都是人，大多数人都没有完美的记忆力。因为这个事实，用户名和密码很容易被记错或完全忘记。物理令牌和其他设备可能会丢失或损坏。

在我们建造电子系统的许多年里，我们已经用许多方法解决了这些故障。对于用户名和密码，我们已经为您的用户构建了使用其他形式的身份验证来重置其密码的功能。如今，后备身份验证通常包括发送一封带有临时可信密钥或密码的电子邮件。

对于物理令牌、移动电话问题和生物识别故障，我们主要依赖面对面、可信电子邮件和其他手动身份验证方法。

## 其他顾虑

选择身份验证方法时要考虑的最后一件事是用户体验。根据您的应用程序对用户的重要程度，使用生物特征、物理令牌和用户名/密码的多因素身份验证可能会矫枉过正，导致许多用户根本不使用您的应用程序。

您还可以通过只要求一种形式的身份验证来降低应用程序的安全性，如果主题相对敏感，这可能会使用户失去兴趣。

我们将在本系列的下一篇文章中进一步讨论这个问题，我将给出一些在设计身份验证方案时需要考虑的问题。

## 闭幕式

在这一期的真人身份验证中，我们讨论了处理用户初始身份验证的不同方法、实现这些方法时的常见陷阱以及一些常见的漏洞。在下一期文章中，我将向您展示处理正在进行的身份验证、服务器到服务器的身份验证和常见授权方案的不同方法。

在那之前，亲爱的读者！

#### 真人网络认证:

1.  第一部分
2.  这个帖子
3.  即将推出