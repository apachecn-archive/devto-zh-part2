# Jelly Fin:我应该使用查询还是扫描？

> 原文：<https://dev.to/sublimegeek/jelly-fin-should-i-use-a-query-or-scan-40fa>

嘿戴夫托，

我有一个端点，在那里我试图使用 between 按日期获取事务的子集。当我运行这个时，我没有得到任何结果。奇怪的是，我也没有出错。

我知道，我知道...为什么不把这个贴在 StackOverflow 上。我不想被评判，你们更支持我。:)

```
// get transactions for {month} and {year}
api.get('/transaction/tab/{year}/{month}', (request) => {
  const year = request.pathParams.year
  const month = request.pathParams.month
  const params = {
    TableName: request.env.tableName,
    KeyConditionExpression: '#date between :val1 and :val2',
    ExpressionAttributeNames: {
      '#date': 'date'
    },
    ExpressionAttributeValues: {
      ':d': 'date',
      ':val1': year +'-'+month+'-01',
      ':val2': year +'-'+month+'-'+getDaysInMonth(month, year)
    }
  }

  console.log(params)

  // post-process dynamo result before returning
  dynamoDb.query(params, (err, data) => {
    console.log(data)
    if (err) {
      console.error('Unable to query. Error:', JSON.stringify(err, null, 2))
      return 'Unable to query. Error: '+ JSON.stringify(err, null, 2)
    } else {
      console.log('Query succeeded.')
      data.Items.forEach((item) => {
        console.log(' -', item.year + ': ' + item.title)
      })
      return data.Items
    }
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

下面是我的数据模型:

```
{
    id: String
    date: String
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode