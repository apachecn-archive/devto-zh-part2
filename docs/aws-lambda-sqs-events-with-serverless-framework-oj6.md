# 使用无服务器框架的 AWS Lambda SQS 事件

> 原文：<https://dev.to/piczmar_0/aws-lambda-sqs-events-with-serverless-framework-oj6>

[![drawn with [cloudcraft.co](http://cloudcraft.co)](img/b5352c4ea3c2bb96d08ae0509dd046d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hf8cFAtD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rpe0jnb3fp10tzm4s1db.png)

这篇文章最初发表于 [Cloudly。技术](https://cloudly.tech/blog/aws-lambda-sqs-trigger/)

* * *

2018 年 6 月 28 日，亚马逊宣布增加对 Lambda SQS 活动的支持。这极大地简化了无服务器的消息驱动系统的开发。
以前，如果我们想监听 Lambda 中的 SQS 事件，我们需要每隔一段时间轮询一次消息。
Lambda 必须由 cron 调度程序触发，以检查是否有新消息出现在 SQS 队列中。如果没有新东西，那当然是浪费资源和金钱。

现在，我们不再需要调度程序，当新消息出现在 SQS 队列中时，Lambda 函数将被自动调用。

在这篇文章中，我将展示我们如何在[无服务器框架](https://serverless.com/)中使用这个新特性。

如果你还没有听说过这个框架，那么你可以在这里查看非常好的文档。

* * *

附注:
在写这篇文章的时候，已经发布的无服务器框架版本还不支持 SQS 事件。1.27.3 但是有一个 [PR ready](https://github.com/serverless/serverless/pull/5074) ，所以这应该被添加到下一个版本中。
如果你和我一样不耐烦，可以从 GitHub 拉分支，自己建:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

然后您可以使用`bin/serverless`脚本来创建和部署项目。

* * *

我可以想象消息驱动架构的许多用例，例如，在订单处理系统中，在客户下订单后，消息可以被发送到另一个服务。客户将立即得到订单提交的通知，而订单的整个处理可以异步进行(以消息驱动的方式)。

我试图让这个例子尽可能简单，不要深入到任何特定的业务场景中。

我们将只有两个功能:

*   发送方——将被 REST API 触发，并向 SQS 队列提交新消息
*   接收方-将处理来自 SQS 队列的消息。

让我们从 aws-nodejs 模板创建一个新项目:

```
serverless create --template aws-nodejs 
```

Enter fullscreen mode Exit fullscreen mode

这将生成两个文件:

*   yml——AWS 上新服务堆栈的定义(使用底层的 CloudFromation)
*   handler.js -一个演示函数

我们可以把`handler.js`改名为`receiver.js`，稍微更新一下。

为您的演示更新生成的源代码后，它们将如下所示:

### serverless.yml

```
service: sqs-triggers-demo

provider:
  name: aws
  runtime: nodejs6.10
  profile: sls
  region: us-east-1
  iamRoleStatements:
    - Effect: "Allow"
      Action:
        - "sqs:SendMessage"
        - "sqs:GetQueueUrl"
      Resource: "arn:aws:sqs:${self:provider.region}:811338114632:MyQueue"
    - Effect: "Allow"
      Action:
        - "sqs:ListQueues"
      Resource: "arn:aws:sqs:${self:provider.region}:811338114632:*"

functions:
  sender:
    handler: sender.handler
    events:
      - http:
          path: v1/sender
          method: post
  receiver:
    handler: receiver.handler
    events:
      - sqs:
          arn:
            Fn::GetAtt:
              - MyQueue
              - Arn

resources:
  Resources:
    MyQueue:
      Type: "AWS::SQS::Queue"
      Properties:
        QueueName: "MyQueue" 
```

Enter fullscreen mode Exit fullscreen mode

让我给你解释一下这里发生了什么。
首先，我们定义了我们的服务名`sqs-triggers-demo`，并指定我们要在哪个区域创建它(`us-east-1`)。
接下来，我们需要给 SQS 队列发送消息的权限，这将由`sender`函数使用。

```
 iamRoleStatements:
    - Effect: "Allow"
      Action:
        - "sqs:SendMessage"
        - "sqs:GetQueueUrl"
      Resource: "arn:aws:sqs:${self:provider.region}:811338114632:MyQueue"
    - Effect: "Allow"
      Action:
        - "sqs:ListQueues"
      Resource: "arn:aws:sqs:${self:provider.region}:811338114632:*" 
```

Enter fullscreen mode Exit fullscreen mode

队列名`MyQueue`在这里是硬编码的。在生产代码中，我们可能希望从环境属性中传递它。为了简单起见，我还硬编码了一个帐户 ID，但是我们也应该将它作为一个属性来传递。然而，这种情况下的语法有点难看，会分散读者对主题的注意力。你可以看看[这里](https://forum.serverless.com/t/getting-handle-accountid-in-serverless-config/946/4)是如何参数化的。

然后我们指定两个函数:`sender`和`receiver`以及它们的句柄，它们将被编码在文件`sender.js`和`receiver.js`中。
他们每个人都有一个名为`handler`的功能。

```
functions:
  sender:
    handler: sender.handler
    events:
      - http:
          path: v1/sender
          method: post
  receiver:
    handler: receiver.handler
    events:
      - sqs:
          arn:
            Fn::GetAtt:
              - MyQueue
              - Arn 
```

Enter fullscreen mode Exit fullscreen mode

`receiver`函数定义了一个 SQS 事件。从`MyQueue`开始触发。
它需要定义队列的 arn。在这种情况下，它由逻辑 ID 指定，但也可以这样做:

```
 receiver:
    handler: receiver.handler
    events:
      - sqs:
          arn: "arn:aws:sqs:${self:provider.region}:811338114632:MyQueue"
          batchSize: 1 
```

Enter fullscreen mode Exit fullscreen mode

可选地，我们可以定义一个批处理大小，即 Lambda 函数一次应该处理多少 SQS 消息(默认和最大。值为 10)。

sqs 事件将把您现有的 SQS 队列连接到一个 Lambda 函数。无服务器不会创建新队列。这就是为什么我们在最后有一个`resources`部分，它将为我们创建一个新队列。

```
resources:
  Resources:
    MyQueue:
      Type: "AWS::SQS::Queue"
      Properties:
        QueueName: "MyQueue" 
```

Enter fullscreen mode Exit fullscreen mode

### sender.js

```
var AWS = require('aws-sdk');
var sqs = new AWS.SQS({
    region: 'us-east-1'
});

exports.handler = function(event, context, callback) {
    var accountId = context.invokedFunctionArn.split(":")[4];
    var queueUrl = 'https://sqs.us-east-1.amazonaws.com/' + accountId + '/MyQueue';

    // response and status of HTTP endpoint
    var responseBody = {
        message: ''
    };
    var responseCode = 200;

    // SQS message parameters
    var params = {
        MessageBody: event.body,
        QueueUrl: queueUrl
    };

    sqs.sendMessage(params, function(err, data) {
        if (err) {
            console.log('error:', "failed to send message" + err);
            var responseCode = 500;
        } else {
            console.log('data:', data.MessageId);
            responseBody.message = 'Sent to ' + queueUrl;
            responseBody.messageId = data.MessageId;
        }
        var response = {
            statusCode: responseCode,
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(responseBody)
        };

        callback(null, response);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

在文件的顶部，我们导入了用于发送到 SQS 的 AWS SDK API。
因为我们想从 API 网关使用这个函数，所以我们需要返回如下形式的符合响应:

```
{
    statusCode: <HTTP status code>,
    headers: {
        <a map of HTTP headers>
    },
    body: <response body as string>
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们发送给 SQS 的是一个 HTTP 请求体。

### receiver.js

```
exports.handler = (event, context, callback) => {
    const response = {
        statusCode: 200,
        body: JSON.stringify({
            message: 'SQS event processed.',
            input: event,
        }),
    };

    console.log('event: ', JSON.stringify(event));

    var body = event.Records[0].body;
    console.log("text: ", JSON.parse(body).text);

    callback(null, response);
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们只是解析从 SQS 收到的事件，并提取从`sender`函数:
传递的 HTTP 主体

```
var body = event.Records[0].body; 
```

Enter fullscreen mode Exit fullscreen mode

该事件包含一个数组`Records`，它是一个 SQS 消息数组。大小取决于为 Lambda 函数上的 SQS 事件指定的批处理大小(`serverless.yml`中的`batchSize`)和队列中的事件数量。

然后我希望 HTTP 主体有`text`参数并打印出来:

```
console.log("text: ", JSON.parse(body).text); 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果没有这样的参数，它将会失败，但是我们将用正确的数据来测试它:)

现在，我们可以部署应用程序并打印信息以获得端点 URL:

```
serverless deploy
serverless info 
```

Enter fullscreen mode Exit fullscreen mode

它会打印出如下内容:

```
Service Information
service: sqs-triggers-demo
stage: dev
region: us-east-1
stack: sqs-triggers-demo-dev
api keys:
  None
endpoints:
  POST - https://fochwxb6gh.execute-api.us-east-1.amazonaws.com/dev/v1/sender
functions:
  sender: sqs-triggers-demo-dev-sender
  receiver: sqs-triggers-demo-dev-receiver 
```

Enter fullscreen mode Exit fullscreen mode

然后用命令结尾日志:

```
serverless logs -f receiver 
```

Enter fullscreen mode Exit fullscreen mode

在另一个控制台向`sender`端点发送 HTTP 请求，比如:

```
curl -d '{"text" : "Hello world!"}' https://fochwxb6gh.execute-api.us-east-1.amazonaws.com/dev/v1/sender 
```

Enter fullscreen mode Exit fullscreen mode

来自`receiver`的日志看起来类似于这些:

```
START RequestId: 384a3c5c-5689-59ba-a04a-6fef0588bae9 Version: $LATEST
2018-07-02 00:08:21.571 (+02:00)    384a3c5c-5689-59ba-a04a-6fef0588bae9    event:  {"Records":[{"messageId":"df38c9a3-6562-4dd4-b978-98ac4dce42d0","receiptHandle":"AQEBF/CZlWiLwMpUr8GqE4a2ns7nIBv1nAlD/MJw4HCxYlyzNoSPJxjc+J7Rn34IdKq8wAH+I1e8eUW6ZuVEAat6pD4hZ2WYO+oCgnVFTQMF59zTkT7Miw3F36pR8SxywXAnKUtmCdpeVv40Zz+KYjwlP6VooSIrtCFRuLFFocfjKhBVdKb/9B7Rs5ZgB+QOYgB1bBEfaLfzWNQdFk1bBnDx9BSwQ+9mTMe2wgDIIwUpbSy8NxFZlMW2PzqUHm5JU7wz+dN7bKGVvfnh/URlXS+JNfs2IlXkpZIb+4kU5IG3ItBtQo3y1SzmsXlqczKShI9pgEmL4xtuC94tdNbX0+EgTz2NyX2vN8k4/2aafgw5JIefuiriqCmteesqFvT+awPV","body":"{\"text\" : \"Hello world!\"}","attributes":{"ApproximateReceiveCount":"1","SentTimestamp":"1530482900766","SenderId":"AROAINV2MGHNVAM5STGJY:sqs-triggers-demo-dev-sender","ApproximateFirstReceiveTimestamp":"1530482900780"},"messageAttributes":{},"md5OfBody":"63c34d70a5a1b50dba746843a0cefd5b","eventSource":"aws:sqs","eventSourceARN":"arn:aws:sqs:us-east-1:811338114632:MyQueue","awsRegion":"us-east-1"}]}
2018-07-02 00:08:21.571 (+02:00)    384a3c5c-5689-59ba-a04a-6fef0588bae9    text:  Hello world!
END RequestId: 384a3c5c-5689-59ba-a04a-6fef0588bae9
REPORT RequestId: 384a3c5c-5689-59ba-a04a-6fef0588bae9  Duration: 0.56 ms   Billed Duration: 100 ms     Memory Size: 1024 MB    Max Memory Used: 20 MB 
```

Enter fullscreen mode Exit fullscreen mode

你可能会想，当我们用信息卸载 SQS 时，Lambda 缩放是什么情况。
[亚马逊文档](https://docs.aws.amazon.com/lambda/latest/dg/scaling.html)解释说:

* * *

*对于处理亚马逊 SQS 队列的 Lambda 函数，AWS Lambda 将自动调整队列上的轮询，直到达到最大并发级别，其中每个消息批次都可以被视为一个并发单元。AWS Lambda 的自动伸缩行为旨在当队列为空时保持低轮询成本，同时使您能够在队列被大量使用时实现高吞吐量。*
*事情是这样的:*

*   *当亚马逊 SQS 事件源映射最初启用时，或者当消息在一段时间没有流量后首次出现时，Lambda 开始使用五个客户端轮询亚马逊 SQS 队列，每个客户端并行发出长时间的轮询请求。*
*   *Lambda 监控正在进行的消息的数量，当它检测到这个数量正在增加时，它会将轮询频率增加每分钟 20 个 ReceiveMessage 请求，并将函数并发性增加每分钟 60 个调用。只要队列保持忙碌，就继续扩大规模，直到至少出现以下情况之一:*
*   轮询频率达到 100 个同时接收消息请求，函数调用并发达到 1000 个。已达到帐户并发最大值。
*   *已达到附加到 SQS 队列(如果有)的函数的每函数并发限制。*

*注*

*帐户级别的限制受帐户中其他函数的影响，每个函数的并发性适用于发送到一个函数的所有事件。有关更多信息，请参见管理并发。*
*当 AWS Lambda 检测到正在运行的消息数量正在减少时，它会将轮询频率每分钟减少 10 个 ReceiveMessage 请求，并将用于调用您的函数的并发性每分钟减少 30 个调用。*

* * *

幸运的是，有一种方法可以用`reservedConcurrency`属性来限制每个 lambda 函数的并发性。
例如，如果我们希望每个`receiver`函数只允许一个线程，那么函数定义应该是这样的:

```
 receiver:
    handler: receiver.handler
    events:
      - sqs:
          arn:
            Fn::GetAtt:
              - MyQueue
              - Arn
    reservedConcurrency: 1 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。你可以在 GitHub 找到这个演示[的源文件。
在你使用完服务栈之后，不要忘记从 AWS 中移除它:](https://github.com/piczmar/sls-sqs-triggers-demo) 

```
serverless remove 
```

Enter fullscreen mode Exit fullscreen mode

我很高兴你能读到这篇文章的结尾。如果你觉得有趣，别忘了喜欢这篇文章，关注我，我会在未来通知你类似的文章:)