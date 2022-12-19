# 在 Java 应用程序中发送电子邮件

> 原文：<https://dev.to/iamthebryguy94/sending-an-email-within-a-java-application-51o3>

大家好，

我决定拿起旧课本，开始学习更多关于 Java 的知识(我知道，到目前为止我应该理解了大部分:D)。

我关注的是应用程序和发送电子邮件之间的交互。因为我最终想将这段代码移植到 android studio，并使用它来创建 android 的电子邮件应用程序。

下面是代码:

```
public static void main(String[] args) {
        // Recipient's email ID needs to be mentioned.
        String to = "example";

        // Sender's email ID needs to be mentioned
        String from = "example";

        // Setting the host and port
        String host = "smtp.gmail.com";
        String port = "465";

        // Get system properties
        Properties properties = System.getProperties();

        // Setup mail server
        properties.setProperty("mail.smtp.host", host);
        properties.setProperty("mail.smtp.port", port);

        properties.setProperty("mail.user", "example");
        properties.setProperty("mail.password", "example");

        // Get the default Session object.
        Session session = Session.getDefaultInstance(properties);

        try {
            // Create a default MimeMessage object.
            MimeMessage message = new MimeMessage(session);

            // Set From: header field of the header.
            message.setFrom(new InternetAddress(from));

            // Set To: header field of the header.
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));

            // Set Subject: header field
            message.setSubject("This is the Subject Line!");

            // Now set the actual message
            message.setText("This is actual message");

            // Send message
            Transport.send(message);
            System.out.println("Sent message successfully....");
        } catch (MessagingException mex) {
            mex.printStackTrace();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我的代码不再连接失败或超时，它只是继续运行(可能直到我的系统崩溃)。在单步执行代码之后——它似乎在
处挂起

```
Transport.send(message); 
```

Enter fullscreen mode Exit fullscreen mode

因为它从不抛出错误，所以我得不到异常代码或任何详细信息。

如果你们能看看我的代码并指出任何错误，那就太好了。

谢谢！！！