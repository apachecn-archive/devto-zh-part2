# 使用 WebSockets 的 Java API 创建 WebSocket 服务器

> 原文：<https://dev.to/vonagedev/creating-a-websocket-server-with-the-java-api-for-websockets-1o1k>

[WebSocket](https://en.wikipedia.org/wiki/WebSocket) 是一种支持服务器和浏览器之间通信的协议。它比 RESTful HTTP 有优势，因为通信是双向和实时的。这允许服务器随时通知客户端，而不是客户端定期轮询更新。

在这一系列文章中，我将向你展示用 Java 创建 WebSocket 服务器的三种不同方法，分别使用 [Spring Boot](http://spring.io/projects/spring-boot) 、 [Spark 框架](http://sparkjava.com/)和【WebSockets 的 Java API。

## 先决条件

您将使用 [Gradle](https://gradle.org/) 来管理您的依赖项并运行您的应用程序。

此外，您需要确保安装了 JDK 的副本。我将在本教程中使用 JDK 8。

## web sockets 的 Java API

Java EE 7 中引入的用于 WebSockets 的 Java API(T1)或 JSR 356 是一种规范，Java 开发人员可以使用它来将 WebSockets 集成到他们的应用程序中。

它允许开发人员编写完全独立于容器实现的基于 WebSocket 的应用程序。在本指南中，您将使用 [Tomcat](http://tomcat.apache.org/) 。然而，因为您将使用 JSR 356，任何其他支持 Java EE 7 的 [web 容器](https://en.wikipedia.org/wiki/Web_container)都应该可以工作。

### 创建项目

您将使用 Gradle 初始化一个新的 Java 应用程序。您可以使用以下命令为您的项目创建一个目录，导航到该目录，并初始化应用程序:

```
mkdir websocket-java-api
cd websocket-java-api
gradle init --type=java-application 
```

### 添加 Java WebSocket API 依赖

将以下依赖项添加到`build.gradle`的`dependencies`块中:

```
compile 'javax.websocket:javax.websocket-api:1.1' 
```

### 创建 WebSocket 端点

WebSocket 消息可以是文本和二进制的。您将创建一个可以处理这两种消息的端点。

`@ServerEndpoint`注释用于修饰一个类，并将其声明为 WebSocket 端点。您可以为它提供一个参数，该参数表示发布端点的路径。

创建一个名为`WebSocketEndpoint`的新类，并用`@ServerEndpoint("/socket")` :
对其进行注释

```
import javax.websocket.server.ServerEndpoint;

@ServerEndpoint("/socket")
public class WebSocketEndpoint {

} 
```

`WebSocketEndpoint`处理的每个事件都由一个注释定义。您可以使用`@OnMessage`注释来标记用于接收二进制或文本事件的方法。

出于演示目的，您将创建一个 echo 服务器，它将收到的消息回显给发送者。

为了测试二进制消息，您将向 WebSocket 服务器发送图像。默认的最大大小取决于容器，但是您可以使用`maxMessageSize`参数来指定支持多大的消息。

添加以下方法:

```
@OnMessage
public String handleTextMessage(String message) {
    System.out.println("New Text Message Received");
    return message;
}

@OnMessage(maxMessageSize = 1024000)
public byte[] handleBinaryMessage(byte[] buffer) {
    System.out.println("New Binary Message Received");
    return buffer;
} 
```

请注意，方法签名决定了该方法将处理哪种类型的消息。参见 [`OnMessage`注释文档](https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html)获取支持的方法签名列表。

### 创建一个客户端来测试你的应用程序

您需要创建一个客户机来测试您的 WebSocket 服务器。您将需要测试发送文本和二进制消息。这可以用 JavaScript 来完成。

在`src/main`文件夹中创建`webapp`文件夹。

将以下内容添加到`src/main/webapp`文件夹内的`index.html`:

```
<html>
<head>
    <style>
        #messages {
            text-align: left;
            width: 50%;
            padding: 1em;
            border: 1px solid black;
        }
    </style>
    Sample WebSocket Client
</head>
<body>
<div class="container">
    <div id="messages" class="messages"></div>
    <div class="input-fields">
        <p>Type a message and hit send:</p>
        <input id="message"/>
        <button id="send">Send</button>

        <p>Select an image and hit send:</p>
        <input type="file" id="file" accept="image/*"/>

        <button id="sendImage">Send Image</button>
    </div>
</div>
</body>
<script>
    const messageWindow = document.getElementById("messages");

    const sendButton = document.getElementById("send");
    const messageInput = document.getElementById("message");

    const fileInput = document.getElementById("file");
    const sendImageButton = document.getElementById("sendImage");

    const socket = new WebSocket("ws://localhost:8080/socket");
    socket.binaryType = "arraybuffer";

    socket.onopen = function (event) {
        addMessageToWindow("Connected");
    };

    socket.onmessage = function (event) {
        if (event.data instanceof ArrayBuffer) {
            addMessageToWindow('Got Image:');
            addImageToWindow(event.data);
        } else {
            addMessageToWindow(`Got Message: ${event.data}`);
        }
    };

    sendButton.onclick = function (event) {
        sendMessage(messageInput.value);
        messageInput.value = "";
    };

    sendImageButton.onclick = function (event) {
        let file = fileInput.files[0];
        sendMessage(file);
        fileInput.value = null;
    };

    function sendMessage(message) {
        socket.send(message);
        addMessageToWindow("Sent Message: " + message);
    }

    function addMessageToWindow(message) {
        messageWindow.innerHTML += `<div>${message}</div>`
    }

    function addImageToWindow(image) {
        let url = URL.createObjectURL(new Blob([image]));
        messageWindow.innerHTML += `<img src="${url}"/>`
    }
</script>
</html> 
```

### 嵌入并配置 Tomcat

不像[用 Spring Boot](https://www.nexmo.com/blog/2018/10/08/create-websocket-server-spring-boot-dr/) 创建 WebSocket 服务器，或者[用 Spark 框架](https://www.nexmo.com/blog/2018/10/15/create-websocket-server-spark-framework-dr/)创建 WebSocket 服务器，最初没有嵌入式服务器来运行你的应用。

Gradle 的 [Gretty](http://akhikhl.github.io/gretty-doc/Getting-started.html) 插件可以用来嵌入各种容器。

首先，将 Gretty 插件添加到您的`plugins`块:
中，将它应用到`build.gradle`文件中

```
id "org.gretty" version "2.2.0" 
```

其次，为了简单起见，您需要配置 Gretty 使用 Tomcat 作为 servlet 容器，并将上下文路径设置为`/`。

将以下块添加到`build.gradle` :

```
gretty {
    servletContainer = 'tomcat8'
    contextPath = '/'
} 
```

注意，默认情况下，Gretty 将使用 Jetty 作为 servlet 容器。同样的指南也将在 Jetty 中运行，但 Spring Boot 和 Spark 框架嵌入的是同一个容器，我想展示一些不同的东西。

### 启动应用程序

您的 WebSocket 服务器现在已经完成了。使用应用程序目录中的`gradle appRun`命令启动应用程序。

您可以通过 [http://localhost:8080](http://localhost:8080) 访问您的应用程序，您将看到以下页面:

[![Sample JavaScript-enabled client for testing the WebSocket server.](img/ec964e3dc81ba53c8bd303a10c16b735.png)T2】](https://www.nexmo.com/wp-content/uploads/2018/10/2018-10-01-13-38-05.png)

“connected”消息表示 JavaScript 客户端能够建立连接。

尝试通过在输入字段中键入并点击发送按钮来发送文本消息。也可以试着上传一张图片。在这两种情况下，您应该看到相同的消息和图像回显。

[![Sample JavaScript-enabled client showing a text and binary message echoed back.](img/2fefb02b61ba75a7cf2851affdee20c9.png)T2】](https://www.nexmo.com/wp-content/uploads/2018/10/2018-10-01-14-23-40.png)

## 结论

在本教程中，您学习了如何使用 JSR 356 创建一个 WebSocket 服务器，它可以接收二进制和文本消息，并可以在任何 JSR 356 兼容的容器上运行。尝试移除`servletContainer`设置并在 Jetty 中运行，或者使用 [Gradle WAR 插件](https://docs.gradle.org/current/userguide/war_plugin.html)生成一个 WAR 文件并将其部署在不同的容器上。

本教程的最终代码可以在[NEX mo-community/web socket-Java-API](https://github.com/nexmo-community/websocket-java-api)存储库中找到。

您是否知道可以在 [Nexmo 呼叫控制对象](https://developer.nexmo.com/voice/voice-api/ncco-reference#websocket-the-websocket-to-connect-to)中使用 WebSocket 作为端点？查看这篇关于 IBM Watson 的[实时通话转录的帖子，了解如何使用](https://www.nexmo.com/blog/2017/10/03/real-time-call-transcription-ibm-watson-python-dr/) [Nexmo 语音 API](https://developer.nexmo.com/voice/voice-api/overview) 、WebSockets 和 [IBM Watson](https://console.bluemix.net/docs/services/speech-to-text/index.html#about) 来执行实时通话转录。

有兴趣阅读更多关于 WebSockets 的内容吗？查看[上的这篇文章什么是 WebSockets？他们如何增强数字解决方案？](https://www.nexmo.com/blog/2018/09/05/websockets-enhance-digital-solutions/)