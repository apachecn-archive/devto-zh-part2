# 测试[Nodejs] Lambda 函数

> 原文：<https://dev.to/kehers/testing-nodejs-lambda-functions-4o25>

测试 Lambda 函数可能很棘手。典型的循环是假设一切正常，向上推，卡住，调试并局部修正，然后再向上推。一个更好的方法实际上是在推进之前进行局部测试。

让我们考虑一个典型的节点λ函数

```
exports.handler = (event, context, callback) => {
  // Do stuff here
  return callback();
} 
```

为了能够在本地测试这一点，我们需要能够传入所需的`event`、`context`和`callback`参数。有趣的是，通过研究`event`和`context`对象，当然知道`callback`只是一个传入来返回信息的函数，我们可以创建类似的对象，并将它们用于我们的本地测试。

先说`event`。这用于将事件数据传递给函数。数据将取决于连接到 Lambda 函数的事件源。你可以在这里找到事件源发布的[样本事件列表。以下是 SNS 的事件数据示例:](https://docs.aws.amazon.com/lambda/latest/dg/eventsources.html#eventsources-sns)

```
{  "Records":  [  {  "EventVersion":  "1.0",  "EventSubscriptionArn":  eventsubscriptionarn,  "EventSource":  "aws:sns",  "Sns":  {  "SignatureVersion":  "1",  "Timestamp":  "1970-01-01T00:00:00.000Z",  "Signature":  "EXAMPLE",  "SigningCertUrl":  "EXAMPLE",  "MessageId":  "95df01b4-ee98-5cb9-9903-4c221d41eb5e",  "Message":  "Hello from SNS!",  "MessageAttributes":  {  "Test":  {  "Type":  "String",  "Value":  "TestString"  },  "TestBinary":  {  "Type":  "Binary",  "Value":  "TestBinary"  }  },  "Type":  "Notification",  "UnsubscribeUrl":  "EXAMPLE",  "TopicArn":  topicarn,  "Subject":  "TestInvoke"  }  }  ]  } 
```

不过对我来说，(我在 [TFP](https://thefeed.press/) 上使用它来跨工人发送数据)，两件事:

1.  我对事件数据中唯一感兴趣的值是`event.Records[0].Sns.Message`
2.  `Message`是一个*字符串化的* JSON 对象。

基于这些，我可以如下创建我的`event`对象:

```
var event = {
  Records: [{
    Sns: {
      Message: JSON.stringify(testdata)
    }
  }]
} 
```

接下来是`context`参数。我们可以在[这个文档](https://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-context.html)中看到上下文对象的所有键。让我们创建一个类似的对象。

```
var context = {
  awsRequestId: Math.random().toString(36).replace(/[^a-z]+/g, '').substr(0, 5), // random string per request
  callbackWaitsForEmptyEventLoop: true,
  getRemainingTimeInMillis: function(){ return 0 },
  functionName: '',
  functionVersion: '',
  memoryLimitInMB: '',
  logGroupName: '',
  logStreamName: '',
  clientContext: null,
  identity: null
} 
```

是最容易的一个。

```
var callback = function(err, result) {
  if (err)
    console.log(err);
  if (result)
    console.log(result);
  // Terminate execution once done
  process.exit(0);
} 
```

现在我们已经有了 3 个必需的参数。我们可以把所有东西放在一起。

```
require('dotenv').config(); // Environmental variables
var lambda = require('./path/to/lambda/function/index.js')
;

// Event
var eventdata = {
    url: 'https://api.twitter.com/1.1/statuses/home_timeline.json'
    , qs: {
      tweet_mode: 'extended'
    }
    , user: '5ab7d745174f534889991a30'
    , oauth: {
      consumer_key: process.env['TWTR_CK']
      , consumer_secret: process.env['TWTR_CS']
      , token: process.env['TWTR_TOKEN']
      , token_secret: process.env['TWTR_SECRET']
    }
  }
var event = {
  Records: [{
    Sns: {
      Message: JSON.stringify(eventdata)
    }
  }]
}
// Context 
var context = {
  awsRequestId: Math.random().toString(36).replace(/[^a-z]+/g, '').substr(0, 5),
  callbackWaitsForEmptyEventLoop: true,
  getRemainingTimeInMillis: function(){},
  functionName: '',
  functionVersion: '',
  memoryLimitInMB: '',
  logGroupName: '',
  logStreamName: '',
  clientContext: null,
  identity: null
}
// Callback
var callback = function(err, result) {
  if (err)
    console.log(err);
  if (result)
    console.log(result);
  // Terminate execution once done
  process.exit(0);
}

// Run
lambda.handler(event, context, callback); 
```

现在我们有了。如果函数有任何问题，我们可以很容易地知道和调试。测试期间的一个技巧是在函数中进行大量的控制台日志记录，以记录执行点并查看传递的数据。