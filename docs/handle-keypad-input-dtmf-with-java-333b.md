# 用 Java 处理键盘输入(DTMF)

> 原文：<https://dev.to/vonagedev/handle-keypad-input-dtmf-with-java-333b>

## 简介

在之前的教程中，我们向您展示了如何用 Java 创建一个能够[接收电话的应用程序。在本教程中，您将创建一个应用程序，使用](https://www.nexmo.com/blog/2018/08/09/receive-a-phone-call-with-java-dr/) [Nexmo Voice API](https://developer.nexmo.com/voice/voice-api/overview) 接收电话并响应用户输入。

## 先决条件

要完成本教程，您需要一个 [Nexmo 帐户](https://dashboard.nexmo.com/sign-up)。如果您还没有帐户，请立即注册。

您将使用 [Gradle](https://gradle.org) 来管理您的依赖项并运行您的应用程序。此外，您需要确保安装了 JDK 的副本。我将在本教程中使用 JDK 8。

最后，您需要安装 [Nexmo CLI](https://github.com/Nexmo/nexmo-cli) 。您将使用它来购买电话号码，并配置您的 Nexmo 帐户以指向您的新应用程序。

## 用 Java 处理用户输入

本教程将引导您完成以下步骤:

1.  使用 [Gradle](https://gradle.org) 设置一个新的 Java 项目。
2.  使用 [Spark](http://sparkjava.com) 框架来控制调用。
3.  购买一个号码并配置您的 Nexmo 帐户，以便在您的应用程序中使用该号码。

### 使用 Gradle 设置一个新的 Java 项目

您将使用 [Gradle](https://gradle.org) 来管理您的依赖项，并创建和运行您的 Java 应用程序。在命令行中，使用以下命令创建一个新的 Java 项目:

```
mkdir handle-user-input
cd handle-user-input
gradle init --type java-application 
```

`gradle init --type java-application`命令将创建您需要的所有文件夹，以及一个您将在其中编写代码的示例类。

### 使用 Spark 框架来控制调用

您将使用 [Spark](http://sparkjava.com) 框架拦截您的号码收到呼叫时 Nexmo 使用的 HTTP 呼叫，以及接收输入时 Nexmo 发送的请求。

#### 添加依赖关系

将以下内容添加到您的`build.gradle`文件中的`dependencies`块:

```
compile 'com.sparkjava:spark-core:2.7.2'
compile 'com.nexmo:client:4.0.1' 
```

您的`dependencies`块应该是这样的:

```
dependencies {
    // This dependency is found on compile classpath of this component and consumers.
    compile 'com.google.guava:guava:23.0'
    compile 'com.sparkjava:spark-core:2.7.2'
    compile 'com.nexmo:client:4.0.1'

    // Use JUnit test framework
    testCompile 'junit:junit:4.12'
} 
```

#### 设置应答路线

[Gradle](https://gradle.org) 将在`src/main/java`文件夹中创建`App`类。这个类中有一个`getGreeting`和一个`main`方法。您不需要`getGreeting`方法，所以可以随意删除它。

用
替换`main`方法的内容，解析任何导入

```
/*
* Route to answer incoming calls.
*/
Route answerRoute = (req, res) -> {
    TalkAction intro = new TalkAction
            .Builder("Hello. Please press any key to continue.")
            .build();

    InputAction input = new InputAction.Builder()
            .eventUrl(String.format("%s://%s/webhooks/dtmf", req.scheme(), req.host()))
            .maxDigits(1)
            .build();

    res.type("application/json");

    return new Ncco(intro, input).toJson();
};

/*
* Route to print out call event info.
*/
Route eventRoute = (req, res) -> {
    System.out.println(req.body());
    return "";
};

Spark.port(3000);
Spark.get("/webhooks/answer", answerRoute);
Spark.post("/webhooks/events", eventRoute); 
```

这段代码将在[http://localhost:3000/web hooks/answer](http://localhost:3000/webhooks/answer)上设置一条路由，该路由将响应以下 [Nexmo 呼叫控制对象(NCCO)](https://developer.nexmo.com/voice/voice-api/ncco-reference) :

```
[  {  "text":  "Hello please press any key to continue.",  "action":  "talk"  },  {  "maxDigits":  1,  "action":  "input",  "eventUrl":  [  "http://localhost:3000/webhooks/dtmf"  ]  }  ] 
```

talk 动作将指示 Nexmo 将`text`属性回传给调用者。input 动作将指示 Nexmo 捕获调用者输入的单个数字，并向`eventUrl`发送一个 POST 请求，其中包含该信息。

还将在[http://localhost:3000/web hooks/events](http://localhost:3000/webhooks/events)上设置一条路由，Nexmo 将使用该路由来传达呼叫状态的变化。

#### 设置 DTMF 路由

当呼叫者在他们的设备上按下一个数字时，就会产生一个双音多频(DTMF)信号。Nexmo 使用这个 DTMF 信号来确定按下了哪一组键。一旦发生这种情况，Nexmo 向在`InputNcco`中定义的`eventUrl`发送一个 POST 请求。

下面是一个包含 JSON 主体的 POST 请求的例子:

```
{  "dtmf":  "5",  "timed_out":  false,  "uuid":  "some-uuid",  "conversation_uuid":  "some-conversation",  "timestamp":  "2018-08-14T19:59:02.528Z"  } 
```

为了在 Java 中读取这些信息，您需要一个将 JSON 属性映射到 Java 属性的类。Nexmo Java 客户端库包含用于处理这种映射的`InputEvent`类。

在`App`类的`main`方法中，在`eventRoute`下面添加以下路线:

```
/*
* Route which returns NCCO saying which DTMF code was received.
*/
Route inputRoute = (req, res) -> {
    InputEvent event = InputEvent.fromJson(req.body());

    TalkAction response = new TalkAction
            .Builder(String.format("You pressed %s, Goodbye.", event.getDtmf()))
            .build();

    res.type("application/json");

    return new Ncco(response).toJson();
}; 
```

接下来，通过在`main`方法的末尾添加以下内容来注册路由:

```
Spark.post("/webhooks/dtmf", inputRoute); 
```

该路由将响应以下 [NCCO](https://developer.nexmo.com/voice/voice-api/ncco-reference) :

```
[  {  "text":  "You pressed 6, Goodbye.",  "action":  "talk"  }  ] 
```

其中 6 是发送给`/webhooks/dtmf`的 json 的`dtmf`属性。

### 采购数量

您需要一个 Nexmo 号码才能接听电话。如果您没有号码，您可以使用 [Nexmo CLI](https://github.com/Nexmo/nexmo-cli) 购买一个:

```
nexmo number:buy --country_code US 
```

记下购买时分配给您的号码。您将需要这个号码来链接您的应用程序和进行测试。

### 公开您的应用程序

为了向应用程序发送 HTTP 请求，Nexmo 需要知道应用程序运行的 URL。

您可以使用 [ngrok](https://ngrok.com/) 安全地将您的应用程序公开到互联网上，而不是配置您的本地网络或在外部服务上托管您的应用程序。

下载 [ngrok](https://ngrok.com/) 并运行以下命令:

```
ngrok http 3000 
```

请记下转发地址，因为在配置您的帐户时会用到它。下图，转发地址是`http://99cad2de.ngrok.io`。

[![Screenshot of ngrok running in terminal with forwarding address http://99cad2de.ngrok.io](img/f007ce721c7a11ab1bae6ad60f578d1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jymgB-Eq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2018/08/ngrok.png)

### 配置您的 Nexmo 账户

如果您没有应用程序，您可以使用 [Nexmo CLI](https://github.com/Nexmo/nexmo-cli) 使用您的 [ngrok](https://ngrok.com/) 转发地址
创建一个

```
nexmo app:create "Receive Call Demo" http://your-ngrok-forwarding-address/webhooks/answer http://your-ngrok-forwarding-address/webhooks/events --keyfile private.key 
```

运行此命令后，您将看到一个应用程序 id。比如:`notreal-1111-2222-3333-appid`。您将需要此应用程序 id 来将您的电话号码链接到该应用程序。

您可以使用 [Nexmo CLI](https://github.com/Nexmo/nexmo-cli) 链接您的电话号码和应用:

```
nexmo link:app your-nexmo-phone-number your-application-id 
```

该命令指示 Nexmo 在您的帐户上创建一个新的应用程序。当收到电话时，应用程序将向第一个 URL 发送请求。当呼叫状态改变时，应用程序将向第二个 URL 发送请求。

### 测试您的应用程序

在您的`handle-user-input`目录中用`gradle run`命令启动您的应用程序。

拨打您的 Nexmo 号码，测试您的应用程序。你会听到这样的信息，“你好，请按任意键继续。”在手机键盘上按一个数字，然后您会听到“您按了 6，再见”的消息，其中 6 是您按的数字。

## 结论

在几行代码中，您已经创建了一个应用程序，该应用程序可以接收电话呼叫，捕获用户输入，并使用该输入进行响应。尝试使用用户输入来控制呼叫的其他方法。

查看我们关于 [Nexmo 开发者](https://developer.nexmo.com)的文档，在那里你可以了解更多关于[调用流](https://developer.nexmo.com/voice/voice-api/guides/call-flow)或 [Nexmo 调用控制对象](https://developer.nexmo.com/voice/voice-api/ncco-reference)的信息。参见我们的 [Nexmo 快速入门 Java 示例](https://github.com/nexmo-community/nexmo-java-quickstart)，获取关于[本教程](https://github.com/nexmo-community/nexmo-java-quickstart/blob/master/src/main/java/com/nexmo/quickstart/voice/DtmfInput.java)及更多内容的完整代码示例。