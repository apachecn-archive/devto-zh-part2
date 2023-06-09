# Spring Boot 听众为 AWS SQS 与春天的云

> 原文：<https://dev.to/wrschneider/-spring-boot-listener-for-aws-sqs-with-spring-cloud-5cjk>

*此文最初出现在[我的个人博客](http://localhost:4000/2018/09/17/spring-boot-sqs.html)*

令我惊讶的是，我只需要很少的代码就能让 Spring Boot 应用程序监听亚马逊 SQS 的队列。

我在 Github 上放了一个[要诀来说明。](https://gist.github.com/wrschneider/42407cc2ea70799362cc5b044ebcfabb)

关键是，当您的 Maven POM 中有正确的依赖项时，您所要做的就是用`@SqsListener` :
注释您的监听器方法

```
@SqsListener("your-queue-name")
public void listen(DataObject message) {
    LOG.info("!!!! received message {} {}", message.getFoo(), message.getBar());
} 
```

Enter fullscreen mode Exit fullscreen mode

对`spring-cloud-starter-aws`的依赖负责初始化一切并扫描带注释的方法。

#### 命令行参数和认证

您可以通过 Spring Boot 属性指定 AWS 凭据和区域。我通过 Eclipse 将这些作为命令行参数传递，在那里我在本地调试/不在 AWS 上调试:

*   将我的地区设置为美国东部 1(北弗吉尼亚州)。
*   `--cloud.aws.credentials.useDefaultAwsCredentialsChain=true`告诉 Spring Boot 使用 AWS `DefaultAWSCredentialsChain`，它将从环境变量或`~/.aws/credentials`文件中提取凭证。
*   我还为默认凭证链设置了环境变量`AWS_PROFILE`,以便在正确的配置文件下找到我的凭证。

如果我在 AWS 本身中运行，EC2 实例元数据可以自动确定区域，并通过实例概要文件提供凭证。

#### 通过 AWS 控制台进行测试

我使用 AWS 控制台发送测试消息。主要的问题是，如果您使用 JSON 消息，并使用 Spring 通过`@JsonProperty`注释自动将 JSON 反序列化到您的对象，您将需要用值`application/json`指定消息
属性(头)`contentType`。否则，转换将失败，并显示一条无用的错误消息，如“无法从[java.lang.String]转换为....对于 GenericMessage ... "没有迹象表明为什么
会失败。

对于[来说，还有另一种选择，重新配置默认的 Spring 消息类](http://cloud.spring.io/spring-cloud-static/spring-cloud-aws/2.0.0.RELEASE/multi/multi__messaging.html#_consuming_aws_event_messages_with_amazon_sqs)来忽略`contentType`头。