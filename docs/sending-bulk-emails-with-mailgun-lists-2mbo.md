# 发送带有邮件枪列表的批量电子邮件

> 原文：<https://dev.to/kehers/sending-bulk-emails-with-mailgun-lists-2mbo>

除了事务性电子邮件，有时我们需要向所有用户或部分用户发送通用电子邮件。最常用的方法是使用 MailChimp 这样的电子邮件营销服务。对于定制的东西，一种选择是使用循环或队列一个接一个地发送电子邮件。但是更简单的方法是使用 [Mailgun 邮件列表](https://documentation.mailgun.com/en/latest/user_manual.html#mailing-lists)。

Mailgun 邮件列表允许您创建一个电子邮件地址，您可以订阅成员。发送到该地址的任何电子邮件将被发送到每个订阅的成员。

### 创建邮件列表

让我们从创建示例邮件列表开始。我们可以从仪表板或 Mailgun API 中完成这项工作。

[![](img/f818b26c641b367128a0e1925fbbe983.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XIZW4FFu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://obem.be/assets/image/2018/04/create.png)

> 注意事项:
> 
> *   重要的是将访问级别设为只读。这确保了只有您可以通过 API 向列表发送邮件。
> *   您的域的 MX 记录必须指向 Mailgun，以便 Mailgun 可以接收和处理发送到别名地址(邮件列表电子邮件地址)的电子邮件。如果您使用另一个电子邮件提供商的电子邮件，您可以创建一个单独的子域，并将其用于您的邮件列表。

### 添加成员

现在我们有了邮件列表，我们可以开始添加成员了。

**端点**:[https://api.mailgun.net/v3/lists/{address}/members](https://api.mailgun.net/v3/lists/%7Baddress%7D/members)
**方法** : POST
**认证** : HTTP Basic ( `api`为用户名，API Key 为密码)
**参数**:

*   `address`:电子邮件地址，如`Alice <alice@example.com>`或`alice@example.com`
*   `name`:会员名称。可选。
*   `vars` : JSON 编码的任意参数字典字符串，如`{"gender":"female","age":27}`
*   `subscribed` : `yes`添加为已订阅(默认)，`no`为未订阅
*   `upsert` : `yes`更新存在的成员，`no`在成员重复的情况下引发错误(默认)

当必要的事件被触发时，您将希望从您的应用程序中自动执行此操作。例如，如果您的列表是针对您的付费用户，那么一旦您的网站上触发了支付事件，您将需要调用`Add Member API`。

```
stripe.charges.create({
  amount: charge * 100,
  currency: 'usd',
  customer: user.customer.id
}, (err, charge) => {
  // Error and charge checks
  // ...

  // Subscribe user to list
  request.post({
    url: 'https://api.mailgun.net/v3/lists/customers@lists.suet.co/members',
    gzip: true,
    auth: {
      user: 'api',
      pass: 'secretpassword',
      sendImmediately: false
    },
    form: {
      address: user.email
    }
  }, (err, response, body) => {
    // Checks
    // ...
  });
}) 
```

您也可以使用 Mailgun 仪表板中的**邮件列表**下的批量上传选项将现有用户添加到列表中。

[![](img/e5ff41659c242d10296979811296108c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uddQPzLA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://obem.be/assets/image/2018/04/bulk.png)

或者使用大容量插入 API。

**端点**:[https://api.mailgun.net/v3/lists/{address}/members.json](https://api.mailgun.net/v3/lists/%7Baddress%7D/members.json)
**方法** : POST
**认证** : HTTP Basic ( `api`为用户名，API Key 为密码)
**参数**:

*   `members` : JSON 编码的数组。元素可以是地址、。`["bob@example.com", "alice@example.com"]`，或 JSON 对象，如`[{“address": "bob@example.com", "name": "Bob", "subscribed": false}, {"address": "alice@example.com", "name": "Alice"}]`。也可以提供自定义变量，如`[{"address": "Bruce <bruce@jl.dc>", "vars": {"superpower": "money"}},{"name": "Barry", "address": "barry@jl.dc", "vars": {"superpower": "speed"}}]`。每次通话 1000 封邮件。
*   `upsert` : `yes`更新存在的成员，`no`在成员重复的情况下引发错误(默认)

```
// ...
// users => array of retrieved users from db

let chunk = [];
while(users.length) {
  chunk.push(users.splice(0, 1000));
}

Promise.all(chunk.map(d => {
  return new Promise((resolve, reject) => {
    request.post({
      url: 'https://api.mailgun.net/v3/lists/customers@lists.suet.co/members.json',
      gzip: true,
      auth: {
        user: 'api',
        pass: 'secretpassword',
        sendImmediately: false
      },
      form: {
        members: JSON.stringify(d)
      }
    }, (err, response, body) => { 
      // ...
      return resolve(); 
      // Or reject() depending or response
    })
  })
}))
.then(() => {
  // ...
}).catch(reason => {
  // ...
}) 
```

### 删除成员

有两种方法可以从我们的列表中删除联系人。一是删除它们。这会将它们从列表中完全删除。另一个是取消他们的订阅。他们仍将留在列表中，但发送到列表中的电子邮件将不会发送给他们。让我们来看看这两个选项。当他们不再适合我们的细分市场时(在这种情况下，当他们不再是付费用户时)，我们将完全删除他们，如果他们对我们发送的邮件不再感兴趣，我们将让他们退订。

让我们从删除用户开始。

```
stripe.customers.del(user.customer.id, (err, status) => {
  // Error and checks
  // ...

  // Remove user to list
  request.delete({
    url: `https://api.mailgun.net/v3/lists/customers@lists.suet.co/members/${user.email}`,
    gzip: true,
    auth: {
      user: 'api',
      pass: 'secretpassword',
      sendImmediately: false
    }
  }, (err, response, body) => {
    // Checks
    // ...
  });
}) 
```

要取消订阅，我们可以在邮件中包含取消订阅变量`%mailing_list_unsubscribe_url%`。这将由 Mailgun 的退订链接代替，剩下的由 Mailgun 处理。我们也可以创建一个自定义的退订页面，我们可以从我们的邮件链接。在我们的电子邮件中，我们的退订链接将类似于这个`http://domain.tld/unsubscribe/%recipient_email%`。`%recipient_email%`变量将自动替换为用户的电子邮件。

```
// An express route
app.get('/unsubscribe/:email', (req, res) => {
  let email = req.params.email;

  request.put({
    url: `https://api.mailgun.net/v3/lists/customers@lists.suet.co/members/${email}`,
    gzip: true,
    auth: {
      user: 'api',
      pass: 'secretpassword',
      sendImmediately: false
    },
    form: {
      subscribed: 'no'
    }
  }, (err, response, body) => {
    // Checks
    // ...

    res.send('You have been successfully unsubscribed :(');
  });
}) 
```

### 发送邮件

因为我们的列表的访问级别是只读的，所以发布到它的唯一方式是通过 API。让我们创建一封测试邮件。它可以是一封简单的文本邮件。

```
Hi, Opeyemi here.

I am happy to announce a new feature - Quick Mail.

Quick Mail allows you to quickly send emails to any of your users. This is really helpful when you need to do a quick follow up. Simply provide a name and email to send as (it is recommended you use the same email and name you send with within your app), subject, body and send!

I look forward to feedback from you.
Thanks.

%mailing_list_unsubscribe_url% 
```

或者一个简单的 HTML 模板。

[![](img/74c891daf6a9a8438468082d687ff4ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--28sGW0_4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://obem.be/assets/image/2018/04/template.png)

[来源此处](https://gist.github.com/kehers/65bfca51458628b173d6cf50e3da56b3)

```
request.post({
  url: 'https://api.mailgun.net/v3/lists.suet.co/messages',
  auth: {
    user: 'api',
    pass: process.env.EMAIL_KEY
  },
  sendImmediately: false,
  form: {
    from: process.env.EMAIL_FROM,
    subject: 'New feature: Quick Mail',
    html: html, // Content of our html template
    to: 'customers@lists.suet.co', // Our list address
    text: text // Text version of our email
  }
}, (err, response, body) => {
  // Status and checks
}); 
```

### 跟踪发货、打开和点击

您可以从 Mailgun 仪表板轻松跟踪电子邮件的发送、打开和点击。如果您使用[羊脂](https://suet.co/)，这也将在您的羊脂仪表板上可用。

### 结论

使用邮件列表可以很容易地向用户发送大量电子邮件。有趣的是，你可以创建多个列表，并把你的用户划分到这些列表中。