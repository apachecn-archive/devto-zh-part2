# 用 AWS 跟踪我的 Maestro 卡交易

> 原文：<https://dev.to/norus/keeping-track-of-my-maestro-card-transactions-with-aws-4c8>

*最初发布于:[https://dude.wait4.io/card-transactions-aws](https://dude.wait4.io/card-transactions-aws)T3】*

### 目的

我是那种从不随身携带现金的人。嗯，几乎从来没有。

瑞士正朝着无现金社会迈进。但是，仍然有许多地方不接受信用卡(👎逃税者)或者他们在某些条件下接受，比如你必须购买至少 X 的金额。

我如何追踪我的借记卡消费？

我的银行和今天的许多其他银行一样，提供电子银行服务，有时我会用手机查看余额——使用瑞银的应用程序。对于更高级的金钱/预算管理，还有很多其他的应用程序和网站，我不会在这篇文章中介绍。

以上的“问题”是，养成一个习惯需要时间和坚持。如果你有联名账户&借记卡/信用卡，事情会变得更加复杂。

我想，如果能收到每日通知，告知我当月的支出和余额，那会很酷。显然，如果银行有公开/免费使用的 API，实现这样的事情可能是一个容易的项目。或者是否有可能以编程方式登录(也称为无头浏览)，由于需要双因素身份验证，这无论如何都是行不通的。幸运的是，瑞银提供电子邮件/短信/推送通知。

[![alt text](img/4eeab2b475e66d6e17289e181965e96b.png "UBS Push Notification")](https://res.cloudinary.com/practicaldev/image/fetch/s--ht38UuRk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qqxod4sk27t78c58q3le.png) 
*来源:ubs.com*

这是我可以储存起来供将来分析的那种数据。问题是如何和在哪里？因此，这篇文章的目的有两个:1)在我的银行中启用电子邮件通知 2)解析数据并将其存储在某种数据库中。

### 所需的 AWS 组件

> 过度工程化警告:我可以用 Python 脚本实现同样的结果，但是这有什么意思呢？

AWS 中有大量的[服务可用，将来我可能会添加更多的组件，但现在，我只需要这五个:](https://docs.aws.amazon.com/aws-technical-content/latest/aws-overview/aws-overview.pdf)

*   [SES](https://aws.amazon.com/ses/) —邮件收发
*   [SNS](https://aws.amazon.com/sns/) — SNS 发布/订阅、移动推送和短信
*   [Lambda](https://aws.amazon.com/lambda/) —运行代码而不考虑服务器
*   [云监控](https://aws.amazon.com/cloudwatch/) —监控资源和应用
*   [DynamoDB](https://aws.amazon.com/dynamodb/) —托管的 NoSQL 数据库

逻辑流程非常简单:

[![alt text](img/5e8636449cd336569448199ea478ac03.png "AWS Workflow")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XSvWRND7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b8g1yb8b9vsexv8w7lab.png)

1.  收到银行(SES)的通知
2.  将其传递给主题订阅者(SNS)
3.  提取时间戳、交易金额和余额(λ)
4.  将结果存储在数据库中(DynamoDB)

### 接收消息

简单电子邮件服务(SES)是一种接收和发送电子邮件的服务。收到消息后，SES 可以触发指定的操作:在 S3 保存消息，运行 Lambda 函数，将消息发送到 SNS 等。

为了接收来自银行的通知，我的域的 MX 记录必须指向我所在地区的 SES 入站服务器。

```
$ dig +short mx wait4.io
10 inbound-smtp.eu-west-1.amazonaws.com. 
```

Enter fullscreen mode Exit fullscreen mode

该域也托管在 [Route 53](https://aws.amazon.com/route53/) 上，因此 SES 可以自动用所需的 TXT 和 MX 值更新该区域，这为我节省了一些时间。

SES 回执 *bank_alerts* 规则配置为在 SNS:
中向 *ubs_events* 主题发布消息(在 [bogus@wait4.io](mailto:bogus@wait4.io) 上收到)

```
aws> ses describe-active-receipt-rule-set
{
    "Rules": [
        {
            "Name": "bank_alerts",
            "Recipients": [
                "bogus@wait4.io"
            ],
            "Enabled": true,
            "ScanEnabled": true,
            "Actions": [
                {
                    "SNSAction": {
                        "TopicArn": "arn:aws:sns:eu-west-1:0123456789:ubs_events",
                        "Encoding": "UTF-8"
                    }
                }
            ],
            "TlsPolicy": "Optional"
        }
    ],
    "Metadata": {
        "CreatedTimestamp": "2018-04-22T18:39:30.726Z",
        "Name": "default-rule-set"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 消息处理

当在 *ubs_events* 中发布新消息时，Lambda 调用 *parseTransaction* 函数。该函数有两个用途:解析消息并将数据存储在数据库中。

```
from datetime import datetime
import json
import re
import uuid
import boto3

def lambda_handler(event, context):
    msg = json.loads(event['Records'][0]['Sns']['Message'])

    data = msg['content']
    trn = parse_data(data)

    if trn is not None:
        parse_transaction(trn) 
```

Enter fullscreen mode Exit fullscreen mode

解析在 *parse_data* 中完成，接收到的消息的格式有两种变化——每种交易类型一种:

> 从“支票”账户中扣除 100.00 瑞士法郎。新账户余额:5775.84 瑞士法郎。

或者:

> 将 100.00 瑞士法郎记入“支票”账户。新账户余额:5775.84 瑞士法郎。

删除不必要的字符，并将数据与正则表达式进行匹配，以确定消息是否包含有关交易的信息。

```
def parse_data(data):
    data = data.replace('=\r\n', '').replace('\'', '')
    match = re.search(r'\w+ of CHF (.*) \w+ account \"Checking\". New account balance: CHF (.*)\.', data, re.M)

    if match:
        if 'Debit' in data:
            debit = match.group(1)
            credit = 0.00
        elif 'Credit' in data:
            credit = match.group(1)
            debit = 0.00
        balance = match.group(2)

        return {'datetime': datetime.now().isoformat(),
                'debit': format(float(debit), '.2f'),
                'credit': format(float(credit), '.2f'),
                'balance': format(float(match.group(2)), '.2f')}
    else:
        return None 
```

Enter fullscreen mode Exit fullscreen mode

最终结果包含插入到*事务*表中的五个键。该方法返回一个哈希表，其中包含交易金额、借方、贷方、余额和交易的日期时间。这个数据然后被转发给 *parse_transaction* 方法。

```
def parse_transaction(trn):
    ddb = boto3.client('dynamodb')
    trn_id = uuid.uuid4().hex

    ddb.put_item(
        TableName = 'Transactions',
        Item = {'id': {'S': trn_id},
              'datetime': {'S': trn['datetime']},
              'debit': {'N': trn['debit']},
              'credit': {'N': trn['credit']},
              'balance': {'N': trn['balance']}}) 
```

Enter fullscreen mode Exit fullscreen mode

表中的每个事务都由一个惟一的 id 标识，这个 ID 是由 *uuid* 库生成的。

[![alt text](img/ba082cfa6073fa466b01705a93806d04.png "Transactions Table")](https://res.cloudinary.com/practicaldev/image/fetch/s--eU1GsWrF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ate5hul3xem0giwgqu5d.png) 
*注:由于显而易见的原因，数字并非真实*

### 预算更新

*总计*表中每个键的用途非常清楚。

*   `budget` —预定义值
*   `total_balance` —账户 MTD 可用
*   `total_credit` —总到账操作 MTD
*   `total_debit` —账户外操作合计 MTD

在任何给定的时间，我想知道所有这些值。每次记录新的交易时，总数也会更新。UBS 允许配置通知，以便剩余余额包括在消息中，但如果不是这样，我将不得不通过减去借方和增加贷方来计算可用余额。

这种方法要求对每个请求计算总和。对于我个人使用来说，这不是问题，但在特定情况下，这也意味着我受到了 DynamoDB 的限制。计算 *N* 笔交易的金额，需要读取 *N* 条记录，因此需要花费 *N* 个读取容量单位。显然，这种解决方案是不可扩展的，因为它会在几十个交易中导致更大的复杂性(和更高的成本)。

在我的例子中，总余额在每笔交易后更新，并且总是最新的，这减少了对每笔交易求和的必要性。当 *updateTotals* Lambda 检测到*事务*表中的变化时，总数被更新。当考虑多线程时，这种方法是有意义的。

```
from datetime import datetime
import boto3
from dateutil.parser import parse

def lambda_handler(event, context):
    for record in event['Records']:
        if record['eventName'] != 'INSERT':
            print('Unsupported event {}'.format(record))
            return

        trn = record['dynamodb']['NewImage']
        print(trn)

        parse_transaction(trn) 
```

Enter fullscreen mode Exit fullscreen mode

在该功能中，只有*插入*操作是相关的，其他操作被忽略。

```
def parse_transaction(trn):
    period = get_period(trn)
    if period is None:
        return

    debit = trn['debit']['N']
    credit = trn['credit']['N']
    balance = trn['balance']['N']

    refresh_totals(period, debit, credit, balance) 
```

Enter fullscreen mode Exit fullscreen mode

期间以*年月*格式计算，调用 *refresh_totals* 方法。

```
def get_period(trn):
    dt = parse(trn['datetime']['S'])

    return dt.strftime('%Y-%m') 
```

Enter fullscreen mode Exit fullscreen mode

刷新总计部分:

```
def refresh_totals(period, debit, credit, balance):
    ddb = boto3.client('dynamodb')

    response = get_totals(ddb, period)

    if 'Item' not in response:
        create_totals(ddb, period, debit, credit, balance)
    else:
        total_debit = response['Item']['total_debit']['N']
        total_credit = response['Item']['total_credit']['N']
        total_balance = response['Item']['total_balance']['N']

        update_totals(ddb,
            period,
            total_debit, debit,
            total_credit, credit,
            total_balance, balance) 
```

Enter fullscreen mode Exit fullscreen mode

在该方法中，当前期间的*总计*使用 *get_totals* 加载，并在其中更新。如果*总计*不存在，则用 *create_totals* 创建。如果已经存在，则用 *update_totals* 更新它们。

```
def get_totals(ddb, period):
    return ddb.get_item(TableName = 'Totals',
        Key = {'period': {'S': period}},
        ConsistentRead = True) 
```

Enter fullscreen mode Exit fullscreen mode

由于*总数*的更新可以从几个线程中进行，因此一致的读数非常重要。虽然代价更高，但 DynamoDB 在操作过程中使用强一致性读取。

```
def create_totals(ddb, period, total_debit, total_credit, total_balance):
    ddb.put_item(TableName = 'Totals',
        Item = {'period': {'S': period},
                'total_debit': {'N': total_debit},
                'total_credit': {'N': total_credit},
                'budget': {'N': '8000'},
                'total_balance': {'N': total_balance}},
        ConditionExpression = 'attribute_not_exists(period)') 
```

Enter fullscreen mode Exit fullscreen mode

当一个新的 *Totals* 条目被创建时，一个 *ConditionExpression* 被指定，以防来自多个线程的同时请求。只有当新的*总计*不存在时，`attribute_not_exists(period)`才会保存该总计。
因此，如果在此期间以某种方式创建了另一个*总计*(当试图将其加载到 *load_totals* 中时，它将不会存在，并且当试图使用 *create_totals* 创建它时， *put_item* 调用将导致异常，迫使整个 Lambda 函数重新启动。

```
def update_totals(ddb, period, total_debit, debit, total_credit, credit, total_balance, balance):
    ddb.update_item(TableName = 'Totals',
        Key = {'period': {'S': period}},
        UpdateExpression = 'SET #total_debit = #total_debit + :debit, #total_credit = #total_credit + :credit, #total_balance = :balance',
        ConditionExpression = '(#total_debit = :total_debit) and (#total_credit = :total_credit) and (#total_balance = :total_balance)',
        ExpressionAttributeNames = {
            '#total_debit': 'total_debit',
            '#total_credit': 'total_credit',
            '#total_balance': 'total_balance'},
        ExpressionAttributeValues = {
            ':debit': {'N': debit},
            ':total_debit': {'N': total_debit},
            ':credit': {'N': credit},
            ':total_credit': {'N': total_credit},
            ':balance': {'N': balance},
            ':total_balance': {'N': total_balance}}) 
```

Enter fullscreen mode Exit fullscreen mode

在*总计*表:
中更新*总计 _ 借方*的值

```
UpdateExpression = 'SET #total_debit = #total_debit + :debit' 
```

Enter fullscreen mode Exit fullscreen mode

以上的 *UpdateExpression* 对于这次更新来说可能已经足够了(安全方面的),但是我决定更实际一些。在这样做的时候，我添加了一个*条件表达式*来指示 DynamoDB，只有当*总数*没有在另一个线程中更新时，记录才应该被更新，并且它仍然包含传递的值:

```
ConditionExpression = '(#total_debit = :total_debit)' 
```

Enter fullscreen mode Exit fullscreen mode

最终的更新如下所示:

[![alt text](img/af13a8dd2410f472cde9735572baabcf.png "Summary Table")](https://res.cloudinary.com/practicaldev/image/fetch/s--9fcCBrjx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ql8dh4n3460cn5fasifa.png) 
*注:由于显而易见的原因，数字并非真实*

### 总计通知

设置的最后一部分是获取每日报告，通知流程同样非常简单:

[![alt text](img/50c66c8e1a70068fac8b9066d0cfa9a1.png "AWS Workflow")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LjoF8b_J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d6qmoce9qbo7g9j53rqh.png)

我配置了 *CloudWatch 定时器*在每天 19:00 GMT 触发 *dailyNotify* Lambda 功能。

[![alt text](img/2a8de2316be88473e7dcfcb950f6e4ad.png "DailyBankingNotification")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E2tdqvZY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ibyd81akjynmsr0bhwyu.png)

*load* 函数从 DynamoDB 表中检索数据，而 *make_html* 函数返回 html 代码。

```
import boto3
import decimal
from datetime import datetime
from operator import itemgetter

def load_summary(ddb, period):
    result = ddb.get_item(TableName = 'Totals',
        Key = {'period': {'S': period}},
        ConsistentRead = True)

    return result

def load_last_transactions(ddb, num_trans=5):
    table = ddb.scan(TableName = 'Transactions')

    entries = []
    for i in table['Items']:
      i['datetime']['S'] = datetime.strptime(i['datetime']['S'], '%Y-%m-%dT%H:%M:%S.%f')
      entries.append(i)

    result = sorted(entries, key=itemgetter('datetime'), reverse=True)

    return result[:num_trans]

def make_transactions_html(data):
    html_start = '''
            <h3>Last 5 transactions</h3>
            <div class="divTable">
                <div class="divTableHeading">
                    <div class="divTableRow">
                        <div class="divTableHead">Datetime</div>
                        <div class="divTableHead">Debit</div>
                        <div class="divTableHead">Credit</div>
                        <div class="divTableHead">Balance</div>
                    </div>
                </div>'''

    html_rows = ''
    for trn in data:
        html_rows += '''
                <div class="divTableRow">
                    <div class="divTableCell">{}</div>
                    <div class="divTableCell">CHF {}</div>
                    <div class="divTableCell">CHF {}</div>
                    <div class="divTableCell">CHF {}</div>
                </div>'''.format('{:%Y-%m-%dT%H:%M:%S}'.format(trn['datetime']['S']),
                    float(trn['debit']['N']),
                    float(trn['credit']['N']),
                    float(trn['balance']['N']))

    html_end = '''
            </div>'''

    return '{}{}{}'.format(html_start, html_rows, html_end)

def make_summary_html(data, period):
    html = '''
            <h3>Totals for {0}</h3>
            <div class="divTable">
                <div class="divTableHeading">
                    <div class="divTableRow">
                        <div class="divTableHead">Total Debit</div>
                        <div class="divTableHead">Total Credit</div>
                        <div class="divTableHead">Current Balance</div>
                    </div>
                </div>
                <div class="divTableRow">
                    <div class="divTableCell">CHF {1:.2f}</div>
                    <div class="divTableCell">CHF {2:.2f}</div>
                    <div class="divTableCell">CHF {3:.2f}</div>
                </div>
            </div>'''.format(period,
                    float(data['Item']['total_debit']['N']),
                    float(data['Item']['total_credit']['N']),
                    float(data['Item']['total_balance']['N']))

    return html

def lambda_handler(event, context):
    ddb = boto3.client('dynamodb')

    current_period = datetime.today().strftime('%Y-%m')

    summary_result = load_summary(ddb, current_period)
    transactions_result = load_last_transactions(ddb)

    summary_html = make_summary_html(summary_result, current_period)
    transactions_html = make_transactions_html(transactions_result)

    send_email(summary_html, transactions_html) 
```

Enter fullscreen mode Exit fullscreen mode

最后，html 代码被传递给 email 函数。

```
def send_email(totals_html, transactions_html):
    client = boto3.client('ses', region_name = 'eu-west-1')

    MAIL_FROM = 'bogus@wait4.io'
    MAIL_TO = ['bogus@gmail.com']
    SUBJECT = 'AWS Bank Summary Daily Report'

    HTML_BODY = '''<html>
    <head>
    <style>
    .divTable {{
      display: table;
      width: 100%;
    }}
    .divTableRow {{
      display: table-row;
    }}
    .divTableCell, .divTableHead {{
      border: 1px solid #999999;
      display: table-cell;
      padding: 3px 10px;
    }}
    .divTableHeading {{
      background-color: #EEE;
      display: table-header-group;
      font-weight: bold;
    }}
    .divTableBody {{
      display: table-row-group;
    }}
    </style>
    </head>
    <body>
    {0}
    {1}
    </body>
    </html>'''.format(totals_html, transactions_html)

    response = client.send_email(
        Destination = {
            'ToAddresses': MAIL_TO
        },
        Message = {
            'Body': {
                'Html': {
                    'Charset': 'UTF-8',
                    'Data': HTML_BODY,
                },
            },
            'Subject': {
                'Charset': 'UTF-8',
                'Data': SUBJECT,
            },
        },
        Source = MAIL_FROM) 
```

Enter fullscreen mode Exit fullscreen mode

### 思想

暂时就这样吧！交易发生后，会处理收到的消息，更新总数，并在一天结束时发送一份报告。

[![alt text](img/6b45c24c0e3563810fa6d19a23282882.png "Daily Email Report")](https://res.cloudinary.com/practicaldev/image/fetch/s--dfWp2LKr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4qhdn5jyibf0lvqxpk2f.png) 
*注:由于显而易见的原因，数字并非真实*

我有更多的计划来扩展功能，如包括商家信息和每个成本类别的支出。使用 D3.js 或类似的制作一些视觉效果也很棒，但是我会为此写一篇新的帖子。

敬请关注，感谢阅读！🙌