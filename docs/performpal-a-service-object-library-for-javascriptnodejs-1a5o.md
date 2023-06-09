# 为 Javascript/NodeJS 执行一个服务对象库

> 原文：<https://dev.to/lucasaragno/performpal-a-service-object-library-for-javascriptnodejs-1a5o>

本周我发布了 [Performpal](https://github.com/lucas-aragno/performpal) 一个 JavaScript 服务对象库。

这个库的灵感来自于干净代码架构和开拓者的原则。

操作是从应用程序的其他层中抽象出所有业务逻辑的一种好的、干净的方式。这对于后端应用程序特别有用，在后端应用程序中，我们通常会将业务逻辑放在控制器/模型层。

使用 performpal，我们可以为应用程序的每个“用例”创建一个操作，并以一种反应式的方式依次执行每个必要的步骤:

例如:

```
const { Operation } = require('performpal')
const pug = require('pug')
const emailService = require('../emailService')
const registeredUserEmail = pug.compileFile('../templates/users/register')

class SendEmail extends Operation {
  constructor () {
    super()
    this.steps = [
      this.createEmailBody,
      this.sendEmail
    ]

    this.failures = [
      this.handleSubmitEmailError
    ]
  }

  // on constructor handle this, like react does with this.state

  createEmailBody ({params, options}) {
    let { email } = params
    options['emailBody'] = registeredUserEmail({email})
  }

  sendEmail ({params, options}) {
    const { emailBody } = options
    const { email } = params
    return emailService.send({ email, emailBody })
  }

  handleSubmitEmailError ({params}) {
    const { email } = params
    throw new Error(`Error sending email to ${email}`)
  }
}

module.exports = SendEmail 
```

操作是框架不可知的，所以你可以在任何你想要的地方使用它们。

这是一条快速路线的样子:

```
const SendEmail = require('../operations/SendEmail')

app.post('/sendEmail', async (req) => {
  try {
    let { email } = req.body
    let result = await (new SendEmail()).run({params: {email}})
    res.send(200).json({result})
  } catch (error) {
    res.send(500).json({error})
  }
}) 
```

这样，我们的路由将只是我们的应用程序的 HTTP 端点，它只关心获取请求、获取参数、调度操作和传递结果。

您还可以使用 Operation 编写 GraphQL 解析器、回调或任何您想要的东西。

这种模式对我保持大型应用程序的整洁有很大帮助，所以我决定把它移到一个库中，欢迎反馈！