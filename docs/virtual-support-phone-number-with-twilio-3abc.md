# 带 Twilio 的虚拟支持电话号码

> 原文：<https://dev.to/josefrousek/virtual-support-phone-number-with-twilio-3abc>

很长一段时间以来，我一直在寻找一种简单、廉价的解决方案，将我的支持电话号码与我的个人号码隔离开来，最近我找到了解决方案。

我喜欢 Twilio 为开发人员简化事情的方式，所以他们是我的第一个也是唯一的赌注。我到底想达到什么目的？我想有一个电话号码，我可以重定向到我的手机上，当流量足够高时，在需要时将来电者发送到语音邮件。我也想用 SIP 电话从那个号码打电话。

首先，创建一个 Twilio 帐户，并获得一个支持语音的电话号码。现在我们需要把这些点联系起来。让我们假设 *+420111111111* 是你的号码， *+420999999999* 是你从 Twilio 那里得到的号码。

### 接听电话

Twilio 使用 TwiML，这是一种标记语言，用来告诉 Twilio 当你收到来电或短信时该做什么。我们存储 TwiML 命令的地方叫做 TwiML Bins。TwiML 库允许你编写 TwiML，Twilio 将为你托管 TwiML——所以我们不需要启动 web 服务器。

运行时>TwiML bin 是我们管理 bin 的地方。

创建一个名为`Forward incoming calls to my phone`的新 bin 并粘贴以下代码。

```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Dial callerId="+420999999999">
    +420111111111
  </Dial>
</Response> 
```

Enter fullscreen mode Exit fullscreen mode

[![Incoming call Bin](../Images/ba68414be19a4693a616fc55553509b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hc6VwAlB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dkjz3ev0pk6j4skk0yem.png)

然后我们需要将这个 bin 与一个电话号码相关联。在[电话号码>管理号码](https://www.twilio.com/console/phone-numbers/incoming)部分选择您的电话号码，并寻找一个名为*的字段，有电话打进来*。选择 *TwiML* ，然后选择我们刚刚创建的箱子。

[![Phone number management](../Images/028ad5d066c871b836398fa596df4ea4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H8eYPMLt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m3grzj15vh8fzx5kcy9d.png)

就这样，你现在应该可以接电话了。尝试拨打您的 Twilio 号码。

### 使用您的 Twilio 号码拨打电话

要使用 Twilio 号码拨打电话，我们需要使用 VoIP 应用程序，如 Android 和 iOS 上可用的 Zoiper。要使用这个应用程序，我们需要设置一个 SIP 域。在[可编程语音> SIP 域](https://www.twilio.com/console/voice/sip/endpoints)创建一个新的域。

[![New SIP Domain](../Images/fb557661b10f14ff731b68985452b617.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i8lE4dsV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tt7sxg3s4qu50iomqq73.png)

确保您关联了用于*语音认证*和 *SIP 注册*的凭证。然后，我们创建另一个 TwiML Bin 来处理呼出呼叫。

```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
  <Dial callerId="+420999999999">
    {{#e164}}{{To}}{{/e164}}
  </Dial>
</Response> 
```

Enter fullscreen mode Exit fullscreen mode

这个命令有趣的部分是`{{#e164}}{{To}}{{/e164}}`。当使用 SIP 接口时，传递下来的`To`值采用下面的形式`sip:+15125551212@freelance.sip.us1.twilio.com`。功能`e164`从该文本中提取电话号码。保存这个 bin 后，我们将复制它的 *URL* ，并将其用作我们创建的 SIP 域中的*请求 URL* 。

### Zoiper 配置

现在 Twilio 已经设置好了，我们可以打电话了。我们将使用之前创建的 SIP 凭据。你可能不得不在域中填入区域`us1`作为没有区域的域，或者填入任何其他对我无效。

[![Zoiper Configuration](../Images/047bc43f3c706ac013c4471c3d14bc09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QAsa8I4h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cuzxr2sjcwy21c9z8h4n.jpeg)

我对这种解决方案的工作方式非常满意，通话和号码本身也很便宜。