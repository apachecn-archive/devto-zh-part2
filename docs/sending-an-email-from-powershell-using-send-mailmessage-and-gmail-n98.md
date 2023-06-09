# 使用 send-mailmessage 和 Gmail 从 Powershell 发送电子邮件

> 原文：<https://dev.to/mrpowerscripts/sending-an-email-from-powershell-using-send-mailmessage-and-gmail-n98>

大约五年前，我制作了这个关于从 Powershell 发送电子邮件的视频。当时我并没有多想，但我在视频的例子中使用了我实际的电子邮件地址。这导致人们复制/粘贴包括我的电子邮件在内的示例，并向我发送他们所有的测试电子邮件。我真的很喜欢它！

下面的脚本将展示如何在 Gmail 中使用 send-mailmessage 从 Powershell 发送电子邮件。

你永远不会想到像从 Powershell 发送电子邮件这样简单的事情会引起这么多人的兴趣。我当时并不这么认为。自从那个视频出现后，我收到了来自世界各地在许多不同公司工作的人的“测试”邮件。大大小小。政府和非营利组织。很难想象分享这么简单的东西会影响这么多人。如果你也想烦我，这里是代码本身:

```
$MyEmail = "PlayingWithPowershell@gmail.com"
$SMTP= "smtp.gmail.com"
$To = "PlayingWithPowershell@gmail.com"
$Subject = "BRO!"
$Body = "WHAT UP MR.POWERSCRIPTS?"
$Creds = (Get-Credential -Credential "$MyEmail")

Start-Sleep 2

Send-MailMessage -To $to -From $MyEmail -Subject $Subject -Body $Body -SmtpServer $SMTP -Credential $Creds -UseSsl -Port 587 -DeliveryNotificationOption never

<#
$PSEmailServer variable can be used to pre-configure the
SMTP server in your Powershell Profile. Then you don't need
to specify -smtpserver paramter. Send-MailMessage will use the
SMTP sever address assigned to $PSEmailServer
Delivery Notification Options:
-- None: No notification.
-- OnSuccess: Notify if the delivery is successful.
-- OnFailure: Notify if the delivery is unsuccessful.
-- Delay: Notify if the delivery is delayed.
-- Never: Never notify.
#> 
```

确保你进入[https://myaccount.google.com/security](https://myaccount.google.com/security)并一直滚动到底部，打开“允许不太安全的应用程序”,如果你想用你的 Gmail 帐户尝试这个，就像视频中的例子。你可以把它切换回来，一旦你完成了鬼混。

一般有时间我会回复的。类似于“祝贺你！成功了！”。99%的时间我都没有听到回复。但偶尔，我会收到进一步的支持请求。我 99%的时间都不会回复。你们都像我一样知道如何使用谷歌。