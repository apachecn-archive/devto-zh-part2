# 用自动气象站监测弗罗茨瓦夫天气

> 原文：<https://dev.to/lukaszkuczynski/monitoring-wrocaw-weather-with-aws-903>

如果我想比较 AWS Lambdas 和 Azure 函数 app 呢？前几周，我做了一些 Azure 示例应用程序。有一些关于那篇文章。但我总觉得 AWS 比 Azure 更智能，GUI 更轻便，我喜欢 Python。那么为什么不把我的项目复制到 AWS 呢？

## 计划

创建一个通知机制，告诉我弗罗茨瓦夫的天气变化。这将在 AWS 机制中使用 Lambda 架构来完成。

## 实现

### 查看天气

Lambda 函数可以由 *CloudWatch 事件*触发，利用它我可以设置一些 cron 表达式。因此，我将检查由 [openweather API](https://openweathermap.org/current) 公开的天气，并将其放入一个队列中。亚马逊的排队是所谓的 T4 SQS 的一部分。使用 [Python boto 客户端](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sqs.html)，在亚马逊处理 SQS 相当容易。接下来是我获取数据的函数。在我的例子中，每 10 分钟我将调用 *openweather* API 来查询当前天气，并将它的描述放入一个队列中。

```
import os
from datetime import datetime
from urllib.request import Request, urlopen
import json
import boto3

SITE = os.environ['site'] # URL of the site to check, stored in the site environment variable 
def validate(res):
    return EXPECTED in res

def lambda_handler(event, context):
    print('Checking {} at {}...'.format(SITE, event['time']))
    try:
        req = Request(SITE, headers={'User-Agent': 'AWS Lambda'})
        response = urlopen(req).read()
        print(response)
        data = json.loads(response.decode("utf-8"))
        weather = data['weather'][0]['main']
        dt = data['dt']
        dt_iso = datetime.isoformat(datetime.fromtimestamp(dt))
        print(f"weather is {weather} with UTC time {dt_iso}")

        queueUrl = os.environ['SQS_NEW_WEATHER']
        client = boto3.client('sqs')
        response = client.send_message(
            QueueUrl=queueUrl,
            MessageBody=weather
        )

    except:
        print('Check failed!')
        raise
    else:
        print('Check passed!')
        return weather
    finally:
        print('Check complete at {}'.format(str(datetime.now()))) 
```

Enter fullscreen mode Exit fullscreen mode

### 写坚持

当收到值时，我想将它与前一个值进行比较，以确定是否有变化。我必须把状态放在某个地方。我将使用 AWS 的默认数据库:DynamoDB。

该函数是为 SQS 上的新消息触发的，并且可以很容易地用 AWS lambda GUI 进行配置。在 SQS 上接收的消息可作为λ的*事件*参数。

```
import boto3
from boto3.dynamodb.conditions import Key
from datetime import datetime

def lambda_handler(event, context):
    print("received!")
    print(event)
    new_value = event['Records'][0]['body']
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('weather')
    table.put_item(
       Item={
            'created_at': datetime.utcnow().strftime('%Y%m%d_%H%M%S'),
            'type': 'wroclawWeather',
            'text': new_value
        }
    )
    return "time created with value "+new_value 
```

Enter fullscreen mode Exit fullscreen mode

所以上面的函数将在 DynamoDB 实例中创建一个新文档。如你所见，boto3 库帮助处理 DynamoDB 连接。你所需要的就是调用*资源*和*表*，你就准备好`put_item`进入表中了。这将有助于在不久的将来进行比较。看哪！

### 对比时间！

将新文档插入到表中后，我可以将其与同类型的前一个文档进行比较。我将再次使用适当的触发器，这次将是 DynamoDB 的“新行”触发器。最后两项将使用这个复杂的查询和 *KeyConditionExpression* 来获取，因为我只想获取精确类型的两个先前值(就像 SQL 中的`WHERE`子句)。

```
import os
import json
import boto3
from boto3.dynamodb.conditions import Key

def lambda_handler(event, context):
    # print("Received event: " + json.dumps(event, indent=2))
    records = event['Records']
    if len(records) != 1:
        print(f"len records != 1, len = {len(records)}")
        print(records)
        return "len(REC) <> 0"
    record = records[0]
    current_type = record['dynamodb']['NewImage']['type']['S']
    print(f"current_type = {current_type}")
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('weather')
    response = table.query(
        KeyConditionExpression=Key('type').eq(current_type),
        ScanIndexForward=False, 
        Limit=2
    )
    new_value = record['dynamodb']['NewImage']['text']['S']
    old_value = response['Items'][1]['text']

    if old_value != new_value:
        change_text = "changed IN DYNAMO trigger! "+old_value+"->"+new_value
        print(change_text)
        queueUrl = os.environ['SQS_CHANGED_WEATHER']
        client = boto3.client('sqs')
        response = client.send_message(
            QueueUrl=queueUrl,
            MessageBody=change_text
        )
    print(f"Current FROM DYNAMO {new_value}, previous {old_value}") 
```

Enter fullscreen mode Exit fullscreen mode

如果值被更改，消息将会转到另一个 SQS 主题，我将用这个主题通知用户有关更改的信息。

### 用 SNS 通知

当天气发生变化时，它将转到**简单通知服务**，该服务可用于……应用通知规则。在我的情况下，我将简单地使用一封电子邮件来发送更改文本。

再次感谢 *boto3* 库使我能够创建一个 SNS 客户端，这样我就可以在 lambda 中非常容易地发送 SNS 消息。

```
import os
import json
import boto3

def lambda_handler(event, context):
    print("called weatherChanged")
    print(event)
    change_text_value = event['Records'][0]['body']
    print("I received a change: "+change_text_value)
    arn = os.environ['SNS_TOPIC']
    message = change_text_value
    client = boto3.client('sns')
    response = client.publish(
        TargetArn=arn,
        Message=message
    )
    return change_text_value 
```

Enter fullscreen mode Exit fullscreen mode

之后，我只需要配置我的社交网络连接。

### 配置 SNS

为了接收电子邮件，我必须创建一个主题。这个主题有我在 lambda 函数代码中使用的 SNS 地址。然后需要创建一个订阅。您可以使用“电子邮件”类型的订阅，这样 AWS 就会将消息推送至您的电子邮件收件箱。

## AWS 还是 Azure？

我在 AWS 和 Azure 中配置了相同的业务逻辑。是时候做个比较了。

### 天蓝色优点:

*安全变得更简单*

我没有被迫太关心安全性，所有的功能都是开箱即用的。

*捆绑在一起的功能*

出于某种原因，Azure 中的 Function App 是内部所有函数的根，我可以很好地将所有相关函数放在一个资源中。

### AWS 优点:

*python*

这种语言对我来说总是更好。没有 C#经验，Python 只是玩函数好看。

*没有参数绑定的麻烦*

对我来说，Azure 中的绑定不像 AWS 触发器和处理 AWS Lambdas 的参数那样简单。只是更好。

*boto3*

我不得不在 Azure 中做一些奇怪的返回和异步收集器，而 AWS 给出了 *boto3* 让一切变得简单。

我的选择？ **AWS** 。