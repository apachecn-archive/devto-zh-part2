# 通过 alexa-conversation 测试 Alexa 技能

> 原文：<https://dev.to/hideokamoto/testing-alexa-skill-by-alexa-conversation-52j2>

via:[https://wp-kyoto.net/en/test-by-alexa-conversation](https://wp-kyoto.net/en/test-by-alexa-conversation)

对话是一个测试 alexa 技能的 npm 包。

我们可以测试自己的技能，如跟踪代码。

```
conversation(opts)
  .userSays('LaunchRequest')
  .plainResponse
  .shouldContain('Welcome to example skills')
  .userSays('AMAZON.StopIntent')
  .plainResponse
  .shouldContain('See you again!')
  .end() 
```

## 入门

如果要用套餐，首先要加摩卡。

```
$ npm init -y
$ npm install --save alexa-sdk
$ npm install --save-dev alexa-conversation mocha# 
```

## 制作λ功能代码

安装这些软件包后，我们就可以实现 Lambda 功能了。我为测试编写了一个示例代码。

### index.js

```
const Alexa = require('alexa-sdk')
const handlers = {
  'LaunchRequest': function () {
    this.emit(':tell', 'Welcome to my skill !')
  },
  'AMAZON.StopIntent': function () {
    this.response.speak('See you again!')
    this.emit(':responseReady')
  }
}

module.exports.hello = (event, context, callback) => {
  alexa = Alexa.handler(event, context, callback)
  alexa.registerHandlers(handlers)
  alexa.execute()
} 
```

## 做第一次测试

让我们创建第一个测试。

### [T1】index . test . js](#indextestjs)

```
const conversation = require('alexa-conversation')
// Load tested target
const app = require('../index.js')

// init
const opts = {
  name: 'Alexa Sample App', // test name
  appId: 'your-app-id',     // Alexa Skill ID
  app: app,                 // test target
  handler: app.handler      // Lambda handler
}

// write test like conversation
conversation(opts)
  .userSays('LaunchRequest')
  .plainResponse
  .shouldContain('Welcome to my skill')
  .userSays('AMAZON.StopIntent')
  .plainResponse
  .shouldContain('good by!')
  .end() 
```

## 运行测试

创建这些代码后，我们可以通过 mocha cli 运行自己的测试

```
$ ./node_modules/mocha/bin/_mocha index.test.js
  Conversation: Alexa Sample App
    User triggers: LaunchRequest 
      ✓ Alexa's plain text response should contain: Welcome to my skill
    User triggers: AMAZON.StopIntent
      1) Alexa's plain text response should contain: good by!
  21 passing (33ms)
  1 failing

  1) Conversation: Alexa Sample App
       User triggers: LaunchRequest 
         Alexa's plain text response should contain: good by!:
     AssertionError: expected ' See you again! ' to include 'good by!'
      at Context.it (node_modules/alexa-conversation/response.js:100:32) 
```

示例测试失败。因为亚马逊。StopIntent 期望`good by!`，但实际源代码返回`See you again!`。因此，我们必须修正响应以通过测试。