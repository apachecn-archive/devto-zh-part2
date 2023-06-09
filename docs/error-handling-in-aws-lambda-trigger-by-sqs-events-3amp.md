# SQS 事件触发的 AWS Lambda 中的错误处理

> 原文：<https://dev.to/piczmar_0/error-handling-in-aws-lambda-trigger-by-sqs-events-3amp>

[![](img/e8e982b0b9b0ceaf4c25b83cb47c78ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pYXtSEwt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7kio19hdomfivfkqugea.jpg)

这篇文章最初发表在[我的博客](https://cloudly.tech/blog/aws-lambda-sqs-trigger-error-handling/)

在最近的帖子中，我描述了通过 SQS 事件触发 Lambda 函数的新特性。

使用此功能的一个含义是，您不需要删除 Lambda 函数中的 SQS 消息，因为 AWS 会为您删除，但只有在 Lambda 成功完成时。

如果您的 Lambda 失败，消息将保留在队列中，并在“可见性超时”时间后再次对 Lambda 函数可见，可见性超时是亚马逊 SQS 阻止其他消费者接收和处理消息的时间段(默认为 30 秒。)

您可以在 SQS 队列上(当使用`VisibilityTimeout`属性创建时)或为特定消息(使用 [ChangeMessageVisibility 调用](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html#changing-message-visibility-timeout))配置该超时

如果您不处理这种情况，可能会发生这样的情况，当您的 Lambda 运行一段时间后，消息失败，您将浪费 AWS 的计算能力，浪费金钱，并且您的 SQS 队列将随着悬空的未处理消息而增长。

最终，每条消息都将在到期时间后从 SQS 队列中删除(您可以在创建 SQS 队列时指定属性`MessageRetentionPeriod`，默认为 4 天),但是您不应该让它发生。你应该自己控制局面。

我们如何控制它？我想到了至少两种可能性。如果你知道其他的，请在评论中分享。

## 死信队列或 DLQ

DLQ 是专门用于保存失败消息的队列的名称。从技术角度来看，它与任何 SQS 队列没有区别。这只是关于队列的目的。当消息不能在 Lambda 函数中被正确处理时，它可以被传递到 DLQ，并且从那里一个单独的函数可以处理它，例如，再次发送到主要的 Lambda 函数，发送通知给管理员或者只是删除。

这里我将向您展示一个使用 DLQ 的应用程序示例。

我希望它尽可能简单——根据无服务器文档，你应该只在 Lambda 函数上指定一个`onError`属性，就像这样:

```
functions:
  receiver:
    handler: receiver.handler
    events:
      - sqs:
          arn:
            Fn::GetAtt:
              - MyQueue
              - Arn
    onError: arn:aws:sqs:us-east-1:811338114639:ReceiverDLQ 
```

其中您将 ARN 指定给现有的 SQS 队列。

不幸的是，目前它失败了，错误:

```
onError currently only supports SNS topic arns due to a race condition when using SQS queue arns and updating the IAM role. Please check the docs for more info. 
```

在[文档](https://serverless.com/framework/docs/providers/aws/guide/functions#dlq-with-sqs)中也提到了这一点。

幸运的是，有一个解决办法——不使用`onError`属性，而是用传统的 CloudFormation 风格来配置它。

首先，如果您希望无服务器在部署期间创建新的 SQS 队列，您需要在您的`serverless.yaml`的`resources`部分中定义它。

```
resources:
  Resources:
    ReceiverDeadLetterQueue:
      Type: "AWS::SQS::Queue"
      Properties:
        QueueName: "receiverDLQ"
        MessageRetentionPeriod: 1209600 # 14 days in seconds 
```

然后，您需要添加错误处理配置。这里你有两个选择:

1.  用λ函数上的`DeadLetterConfig`配置 DLQ
2.  在源队列上配置`RedrivePolicy`。

我尝试了第一种选择，但我不能让它工作。我失败的试验项目，连同所有的解释，也在 Github 上。

在这里，我将描述第二个选项，它工作得很好。

首先，我们将定义在创建新的应用堆栈时将创建的资源:

```
 resources:
  Resources:
    MyQueue:
      Type: "AWS::SQS::Queue"
      Properties:
        QueueName: "MyQueue"
        VisibilityTimeout: 30
        MessageRetentionPeriod: 60
        RedrivePolicy:
          deadLetterTargetArn:
            "Fn::GetAtt":
              - ReceiverDeadLetterQueue
              - Arn
          maxReceiveCount: 1
    ReceiverDeadLetterQueue:
      Type: "AWS::SQS::Queue"
      Properties:
        QueueName: "receiverDLQ"
        MessageRetentionPeriod: 1209600 # 14 days in seconds 
```

我们定义了两个队列:`MyQueue`和`ReceiverDeadLetterQueue`，并在源队列(`MyQueue`)上配置了重新驱动策略。
这样，当消息被源队列接收超过`maxReceiveCount`次时，它将被重定向到`ReceiverDeadLetterQueue`。

重要的是要记住，如果你试图从 AWS 控制台查看`MyQueue`中的消息，它也将被算作“接收消息”,所以你的 Lambda 函数将没有机会得到它，因为它将在你关闭查看器屏幕后被直接发送到 DLQ。

现在，当我们将应用程序堆栈部署到 AWS 时，我们可以进行测试。

源代码可以在[这里](https://github.com/piczmar/sls-sqs-triggers-dlq/tree/redrive-policy)的`redrive-policy`分支获得。

我们首先可以通过向`MyQueue`提交 JSON 消息并检查日志:
来测试函数是否按预期工作

```
export QUEUE_URL=`aws sqs get-queue-url --queue-name MyQueue --query 'QueueUrl' --output text --profile=sls`
aws sqs send-message --queue-url ${QUEUE_URL} --message-body "{ \"text\" : \"test2\"}" --profile=sls
sls logs -f receiver -t 
```

它应该记录如下内容:

```
2018-07-19 20:40:19.789 (+02:00)    b0a7c784-eeea-5901-8786-944e33b92c18    event:  {"Records":[{"messageId":"3a0a90f5-8fb5-4cbb-8b46-017039272637","receiptHandle":"AQEBfT4rwo5KGoqL1RGBVp4WlsSumH5ToYEENfhDGPXUv2RPdLNDsUTJ/3QjeevDqK5YPrQ1xoSixvACIYlMD7HtXNh0iRRS8VBqutR7tx8ZMkucagRweZ3WyYxXImrVcsD33NIPFlUeldQjRTTnPKfjo+BCZ5TZCs1ndnSKVrqwMzMi7MwTDtI4a1b0IxEJdhiWot7eJ7EwaPmdfVpgK6K/vUQeW+jep3M1cUhNGwoz7H/2bL2FVX54uzfjoShLY7JymmkAuPzqQ/3KQPPvOyMvuragXmzE8VxA+phHHl66hWBj0nlOSWwYbXAFI765/Ik6zPk+nwKm2PoQs3hvPl1aDzAU1FHtqYqaH6GmJ7AAaHwNnufv83ez2hoZGv2AhhDE","body":"{ \"text\" : \"test2\"}","attributes":{"ApproximateReceiveCount":"1","SentTimestamp":"1532025619465","SenderId":"AIDAJLGFXMWT34E5GOTAS","ApproximateFirstReceiveTimestamp":"1532025619496"},"messageAttributes":{},"md5OfBody":"9be3787fa6959a8ed52b3fd5be1aa95a","eventSource":"aws:sqs","eventSourceARN":"arn:aws:sqs:us-east-1:811338114639:MyQueue","awsRegion":"us-east-1"}]}
2018-07-19 20:40:19.790 (+02:00)    b0a7c784-eeea-5901-8786-944e33b92c18    text:  test2 
```

那么让我们用错误的 JSON 主体发送一条消息，在 Lambda 中造成一个错误，例如:

```
aws sqs send-message --queue-url ${QUEUE_URL} --message-body "test" --profile=sls 
```

观察应用程序日志:

```
sls logs -f receiver -t 
```

您会看到如下错误:

```
SyntaxError: Unexpected token e in JSON at position 1
    at Object.parse (native)
    at exports.handler (/var/task/receiver.js:15:29) 
```

Lambda 没有成功，所以消息被返回给`MyQueue`，但这已经是第二次了(第一次是你发送它的时候),它应该被重定向到 DLQ。

您可以在 DLQ 中列出消息，并看到新消息出现

```
export DLQ_QUEUE_URL=`aws sqs get-queue-url --queue-name receiverDLQ --query 'QueueUrl' --output text --profile=sls`
aws sqs receive-message --queue-url ${DLQ_QUEUE_URL} --visibility-timeout 0 --profile=sls 
```

注意:通过将`--visibility-timeout`设置为 0，您可以在每次执行命令时查询相同的消息，而不必等待消息再次出现。

现在，您可以在 DLQ 对失败的消息做您想做的事情，例如，您可以处理它们并决定您是否应该重试并将其发送回主应用程序队列`MyQueue`或删除它。

## Lambda 函数中的错误处理

假设您不想设置 DLQ，而是希望自己处理错误。这个函数看起来会是什么样子？

下面是一个例子:

```
// receiverErrorHandling.js

'use strict';

var AWS = require('aws-sdk');
var sqs = new AWS.SQS({
    region: 'us-east-1'
});

exports.handler = (event, context, callback) => {

    const NUM_OF_RETRIES = 3;
    try {
        console.log('event: ', JSON.stringify(event));

        throw new Error("simulated error");

        // this will never be reached in our demo
        callback(null, "How did you get here??");

    } catch (e) {
        console.log('Handled error', e);

        // we will send new message with incremented count, if below retry limit, otherwise exit with status code 200
        // to allow AWS to remove SQS message, but return status message.
        var message = JSON.parse(event.Records[0].body); // message boody arrives as string JSON
        var retried =  message.retryCount | 0; // we've set batchSize=1 in sls config so it's save to get by index.
        if (retried > NUM_OF_RETRIES-1) {
            const response = "Failed after retries";
            console.log(response);
            callback(null, response);
        } else {
            retried++;
            message.retryCount = retried;

            // send a new message which is a copy of received message but with incremender retry counter.
            var accountId = context.invokedFunctionArn.split(":")[4];
            var queueUrl = 'https://sqs.us-east-1.amazonaws.com/' + accountId + '/MyQueue';

            var params = {
                MessageBody: JSON.stringify(message),
                QueueUrl: queueUrl,
                DelaySeconds: 10
            };

            sqs.sendMessage(params, function (err, data) {
                if (err) {
                    console.log(err);
                    callback( "Failed to retry after error" );
                } else {
                    const response =  "Failed, but will retry " + retried + " time";
                    console.log(response);
                    callback(null,response);
                }

            });
        }
    }
}; 
```

该函数处理以下主要错误:

1.  如果错误在`try`块中抛出，它会被捕获，我们不会让它传播到外面，否则消息会留在队列中，并再次到达 Lambda。
2.  在`catch`块中，我们想决定如何处理错误。在这种情况下，我们让 Lambda 用相同的消息重试两次以上。我们必须向队列发送一条包含重试次数的新消息。在消息中，我们设置了`DelaySeconds`属性，因为我们不想立即调用这个函数。例如，如果在`try`块中，我们将连接到一些外部服务，并且由此导致错误，我们可以预期，在延迟时间之后，它们可能会自我修复，并且重试会成功。
3.  我们可能无法发送消息，例如，因为 SQS 暂时不可用。在这种情况下，我们在 Lambda 回调中返回错误，相同的消息将返回到队列。Lambda 会再处理一次。如果我们不能一次又一次地发送消息，那么 Lambda 将会重试，直到消息在队列中过期，我们在`serverless.yaml`中将其定义为 60 秒。

```
resources:
  Resources:
    MyQueue:
      Type: "AWS::SQS::Queue"
      Properties:
        QueueName: "MyQueue"
        VisibilityTimeout: 30 # 30 sec.
        MessageRetentionPeriod: 60 # 60 sec. 
```

注意，即使我们发送 JSON，消息体也是以文本的形式来自事件，所以我们需要自己解析它来表示对象。

```
 var message = JSON.parse(event.Records[0].body); 
```

您还可以看到，Lambda 可以同时处理多个 SQS 消息，我们参考第一个消息:

```
event.Records[0] 
```

在其他情况下，这可能是不安全的，但是我们定义 Lambda 在配置(`batchSize`)中只轮询单个消息，比如:

```
 receiver:
    handler: receiverErrorHandling.handler
    events:
      - sqs:
          arn:
            Fn::GetAtt:
              - MyQueue
              - Arn
      - batchSize: 1 
```

你可以从 [Github](https://github.com/piczmar/sls-sqs-triggers-dlq/tree/error-handling-without-dlq) 中抓取代码，自己测试。

您可以发送 JSON 消息

```
curl <sender url> -d '{"text" : "Hello"}' 
```

还有观看日志:

```
sls logs -f receiver -t 
```

您将看到以下日志序列:

```
START RequestId: 72ddf8e2-a835-5d10-9088-ab37291615f8 Version: $LATEST
2018-07-27 22:22:10.532 (+02:00)    72ddf8e2-a835-5d10-9088-ab37291615f8    event:  {"Records":[{"messageId":"5d6caf38-23ff-4408-bba0-85b538fad092","receiptHandle":"AQEBV5+zoh9qYCqoKGye8D4gYjpXp1cXTUzm2gNLyAvpEV3XBRsDtyPn81C0G4LaMm3urF6yOUi8or6Zg29V+c7MjRq1nvw4XBk9rGEADdkVqd4/YGo2eGdfbuSOoPVQUbfN2Qk36VG/rjHmQHAYNTudfC4dD6upMfLHNsQEvaiUCUViF7ONa7H0ZGqcvEqNSfv6wH992PpRsRHkgJ52KhqVGVkn2wGUzO7djUv4zMfWWtD7ZGI0N6DB15OpeTtZTYtW8qbzcINuj51/B3Ty5oKh864sidQddm4VuqQQkOEOKVu4n+j/fAW+yOX20RfxJno+mCoh04gD7eEvGI/XgpAKAYsInKEpC8Meu6rjl9Icy2GFUp14e3Za1M8u9VlzsOZV","body":"{\"text\" : \"Hello\"}","attributes":{"ApproximateReceiveCount":"1","SentTimestamp":"1532722929910","SenderId":"AROAJ77G56ARJABY7JWOY:sqs-triggers-error-handling-dev-sender","ApproximateFirstReceiveTimestamp":"1532722929911"},"messageAttributes":{},"md5OfBody":"35efbdc1c909ad760d1b85d56e450139","eventSource":"aws:sqs","eventSourceARN":"arn:aws:sqs:us-east-1:811338114639:MyQueue","awsRegion":"us-east-1"}]}
2018-07-27 22:22:10.532 (+02:00)    72ddf8e2-a835-5d10-9088-ab37291615f8    Handled error Error: simulated error
    at exports.handler.e (/var/task/receiverErrorHandling.js:17:9)
2018-07-27 22:22:10.702 (+02:00)    72ddf8e2-a835-5d10-9088-ab37291615f8    Failed, but will retry 1 time
END RequestId: 72ddf8e2-a835-5d10-9088-ab37291615f8
REPORT RequestId: 72ddf8e2-a835-5d10-9088-ab37291615f8  Duration: 174.19 ms Billed Duration: 200 ms     Memory Size: 1024 MB    Max Memory Used: 34 MB  
...
2018-07-27 22:22:20.768 (+02:00)    08200246-1f91-5f43-983f-51939ea57388    Failed, but will retry 2 time
...
2018-07-27 22:22:30.830 (+02:00)    4313584b-9c88-5da8-a5ac-0b9e1db69a3a    Failed, but will retry 3 time
...
2018-07-27 22:22:40.876 (+02:00)    7c1ab10d-66ab-58d1-ac12-e2d8e7ed3f43    Failed after retries
... 
```

在日志中，您还可以看到每 10 秒重试一次的时间戳。这就是我们通过在发送给 SQS 的信息上设置`DelaySeconds`想要达到的目的。

## 外卖:

1.  处理 SQS 事件可能很棘手。您需要记住一些设置，如消息延迟、可见时间、保留时间、处理的批量大小
2.  Lambda 中的错误处理可以通过多种方式实现，要么通过 tr-catch 手动处理，要么使用死信队列，或者两者兼而有之
3.  使用死信队列允许从应用程序逻辑中分离错误处理逻辑。你可以看到第二个带有 try-catch 的例子有点乱:)

## 参考文献:

*   [通过亚马逊 SQS 使用 AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html)
*   [SQS 文件](https://docs.aws.amazon.com/cli/latest/reference/sqs/index.html)
*   [出错时的λ重试](https://docs.aws.amazon.com/lambda/latest/dg/retries-on-errors.html)
*   [亚马逊 SQS 延迟](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html)