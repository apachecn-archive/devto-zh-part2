# 使用流畅接口重构服务等级配置

> 原文：<https://dev.to/makingloops/refactoring-service-class-configuration-using-fluent-interfaces-304k>

对我们许多人来说，界面已经是旧闻了。

你知道，每当我们有一个`Duck`和一个`Eagle`都需要`Fly()`时，我们就会在这里和那里使用它。

或者，用一个更实际的例子，当我们想通过一个`IPaymentService.ProcessPayment()`方法同时支持 GooglePay 和 ApplePay 时。

这很好，但是一个熟练的开发人员可以从接口中获得比简单的多态更多的能力。

> “一个放置得当的接口就像是服务代码的肮脏的实现细节和一个干净的组织良好的客户端之间的防火墙。”-加里·麦克林·霍尔

在本文中，我们通过简要总结第 3 章来继续我们在 Gary Mclean Hall 所著的《通过 C#编写自适应代码》一书中的跋涉。如果您错过了它们，请参见[第 1 部分](https://purple.pizza/adaptive-code-via-csharp-opening-summary/)、[第 2 部分](https://purple.pizza/dependency-slob/)和[第 3 部分](https://purple.pizza/the-stairway-pattern/)。

第三章介绍了几种设计模式，帮助我们展示了一个界面的全部功能。

最后，我们将更深入地了解一个*流畅的*接口的概念，以及它如何通过对实际产品代码的重构来提高我们代码的可读性和易用性。

## 变得明确

第三章首先给出了接口的标准定义:描述类行为的机制，而没有描述实现。

就像我说的，旧闻了。

在 C#中，我们当然会在创建本质上无实现的契约时使用`interface`声明，这些契约使我们的客户端和服务代码保持分离和可更改。

我在书中不知道的是，你可以*显式地*实现接口方法。代替编写匹配的方法签名来实现接口的通常方式，您可以在方法签名命名冲突的情况下使用点标记法。例如，如果你有一个船的界面，它有一个驾驶船和汽车的方法:

```
 public interface IBoat
    {
        void Steer(int degrees);
    }

    public interface ICar
    {
        void Steer(int degrees);
    } 
```

幸运的是，您有一个业务需求，需要支持那些外观古怪、也可以在公路上行驶的旅游船。由于您的`ICar`接口也定义了一个`Steer()`方法，代码如何知道在下面的例子中使用哪个方法实现呢？

```
 public class LandShark : ICar, IBoat
    {
        public void Steer(int degrees) {
            //Steer with the car rack-and-pinion or with the boat rudder?
        }
    } 
```

为了避免这种命名冲突，您可以像这样明确地定义它:

```
 public class LandShark : ICar, IBoat
    {
        public void ICar.Steer(int degrees) {
            //Steer with rack-and-pinion...
        }

        public void IBoat.Steer(int degrees) {
            //Steer with rudder...
        }
    } 
```

## 三种模式

接下来，作者着眼于利用接口的三种设计模式:空对象模式、策略模式和适配器模式。我不会在这篇文章中涉及它们，但你可以在未来的文章中通过下面我的列表找到它们，当它们准备好被享用时，我会给你发一封电子邮件。

如果你是一个渴望变得更好的开发人员，我强烈推荐它。我不发垃圾邮件，我只是每周给你发一次简单易用的 dev kool-aid，只是为了帮助你提高你的手艺。

开发。给读者点击这里！

## Mixins 和鸭子打字，什么鸭子…

接着，作者带我们进入了一个奇怪的领域，鸭子打字和 mixins。

我不会告诉你细节(并鼓励你自己去研究)，但是主要的思想是，如果你有实现某些方法签名的类，你可以回溯性地对它们编程，就好像它们实现了具有相同方法签名的接口一样。

因为我从来没有需要使用这样的东西，这一切对我来说听起来很蹩脚。

如果我有某种第三方代码，我想放入我自己的界面，我可能会写一个适配器围绕它，但这只是我。

我想象对这些技术有真正的需求，所以把我的意见当成…一个意见。

## 流畅的界面

作者在本章结束时简要地谈到了*流畅界面*的主题。如果一个接口返回自身的一个实例，那么它可以被定义为 *fluent* ，允许调用被链接在一起。

这个简单的技术可以为您的代码的可读性和易用性创造奇迹，为了证明这一点，我想用一些实际产品代码的快速重构来结束这篇文章。

作为背景，我目前正在开发的一个应用程序利用了电子签名，允许用户签署文档并将其发送给其他用户进行额外的签署。为了实现这一功能，我们正在利用第三方供应商 DocuSign。

在 DocuSign 世界中，使用的基本顶级模型是一个模拟真实世界邮件信封的*信封*，其中包含收件人、主题、消息，当然还有需要签名的文档。

在我们当前的实现中，我们的 DocuSign API 服务实现了一个`IESignatureService`接口，它提供了一组创建和发送信封的方法。你可以在这里看到它的简短版本:

```
 public interface IESignatureService
    {
        public string SendSignatureRequest(IESignatureEnvelope envelope);

        public void VoidRequest(string envelopeID, string voidReason);

        public EnvelopeStatusInfo GetEnvelopeStatus(string envelopeID);

        public IEnumerable<RecipientStatusInfo> GetRecipientStatuses(string envelopeID);
    } 
```

在这个例子中，发送电子签名请求的主要任务是通过调用`SendSignatureRequest(IESignatureEnvelope envelope)`来完成的。它要求客户端向它传递一个完整构造的信封对象，并返回一个字符串，在我们的示例中，该字符串用于返回信封的 ID，因为这是 DocuSign 在成功请求后返回给我们的内容。

这里的问题在于细节，因为信封的制作成了客户沉重而复杂的负担。先来看看现在的`IESignatureEnvelope`界面:

```
 public interface IESignatureEnvelope
    {
        string EmailMessage { get; set; }
        string EmailSubject { get; set; }
        IEnumerable<IESignatureEnvelopeRecipient> Recipients { get; }
        byte[] Document { get; set; }
        string DocumentName { get; set; }
    } 
```

这没什么大不了的，但是您会注意到,`Recipients`属性没有 setter，完全由具体的实现来想出填充它的方法。

在更低的层次上，围绕实现`IESignatureEnvelopeRecipient`的对象的创建有额外的复杂性。下面是一个示例，说明您当前需要如何从头到尾创建和发送电子签名请求:

```
 public class ESignatureClient
    {
        private readonly IESignatureService eSignatureService;

        public ESignatureClient(IESignatureService eSignatureService) 
        {
            this.eSignatureService = eSignatureService
        }

        public void SendESignatureRequest(byte[] documentToSign)
        {
            var envelope = new ESignatureEnvelope();

            envelope.EmailSubject = "Your signature is needed";
            envelope.EmailMessage = "Hi there, please review this document and sign it.";

            envelope.DocumentName = "Quote #405";
            envelope.Document = documentToSign;

            var firstRecipient = new ESignatureEnvelopeRecipient();
            firstRecipient.Name = "Joe";
            firstRecipient.Email = "joe@example.com";
            firstRecipient.Type = RecipientType.Signer;

            var secondRecipient = new ESignatureEnvelopeRecipient();
            secondRecipient.Name = "Abby";
            secondRecipient.Email = "Abby@example.com";
            secondRecipient.Type = RecipientType.Signer;

            var thirdRecipient = new ESignatureEnvelopeRecipient();
            thirdRecipient.Name = "Susan";
            thirdRecipient.Email = "Susan@example.com";
            thirdRecipient.Type = RecipientType.CarbonCopy;

            envelope.Recipients.Add(firstRecipient);
            envelope.Recipients.Add(secondRecipient);
            envelope.Recipients.Add(thirdRecipient);

            this.eSignatureService.SendSignatureRequest(envelope);
        }
    } 
```

如您所见，我们使用了相当多的代码来配置一个基本的电子签名请求，该请求包含两个签名人、一个副本和一个要签名的文档。

现在，我们可以简单地通过使用构造函数参数来设置每个对象所需的属性来改进这一点，但是让我们更进一步，在我们的 envelope 接口上添加一些 fluent 接口方法。

```
 public interface IESignatureEnvelope
    {
        //fluent!

        IESignatureEnvelope AddSubjectAndMessage(string subject, string message);
        IESignatureEnvelope AddSigner(string name, string email);
        IESignatureEnvelope AddCarbonCopy(string name, string email);
        IESignatureEnvelope AddDocument(string documentName, byte[] document);

        string EmailMessage { get; set; }
        string EmailSubject { get; set; }
        IEnumerable<IESignatureEnvelopeRecipient> Recipients { get; }
        byte[] Document { get; set; }
        string DocumentName { get; set; }
    } 
```

现在看看我们的客户端类:

```
 public class ESignatureClient
    {
        private readonly IESignatureService eSignatureService;

        public ESignatureClient(IESignatureService eSignatureService) 
        {
            this.eSignatureService = eSignatureService
        }

        public void SendESignatureRequest(byte[] documentToSign)
        {
            var envelope = new ESignatureEnvelope();

            //such clean, wow!

            envelope.AddSubjectAndMessage("Your signature is needed", "Hi there, please review this document and sign it.")
                    .AddSigner("Joe", "joe@example.com")
                    .AddSigner("Abby", "abby@example.com")
                    .AddCarbonCopy("Susan", "susan@example.com")
                    .AddDocument("Quote #405", documentToSign);

            this.eSignatureService.SendSignatureRequest(envelope);
        }
    } 
```

通过添加一些流畅的方法来配置信封，我们大大减少了发出请求所需的代码量。

我们还抽象出了像`RecipientType`这样的细节，让未来的开发者生活更轻松。

流畅的界面是我在实践中看到的一种策略(并且喜欢使用),但是直到现在我还没有实施过。下次在代码库中做这样的重构时，请花些时间。你的团队和未来的开发者会感谢你的。