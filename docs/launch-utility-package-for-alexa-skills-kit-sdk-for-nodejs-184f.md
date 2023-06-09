# 为 Node.js 启动 Alexa Skills Kit SDK 的实用程序包

> 原文：<https://dev.to/hideokamoto/launch-utility-package-for-alexa-skills-kit-sdk-for-nodejs-184f>

Alexa 技能工具包 SDK for Node.js 版本 2 (ask-sdk)对于创建自己的 Alexa 技能非常有用。但是有时我们不得不写很长的源代码来处理 Alexa 的请求。

```
const HelpIntentHandler = {
  canHandle (handlerInput) {
    return handlerInput.requestEnvelope.request.type === 'IntentRequest' &&
      handlerInput.requestEnvelope.request.intent.name === 'AMAZON.HelpIntent'
  },
... 
```

我认为代码`handlerInput.requestEnvelope.request.type === 'IntentRequest' && handlerInput.requestEnvelope.request.intent.name === 'AMAZON.HelpIntent'`可以更短一些。
所以我为 ask-sdk 发布了实用程序包。

## 什么是`ask-utils`？

`ask-utils`是 ask-sdk 的实用程序包。

### 安装

```
$ npm i -S ask-utils 
```

### 易于处理的请求

#### 在提问之前

```
const HelpIntentHandler = {
  canHandle (handlerInput) {
    return handlerInput.requestEnvelope.request.type === 'IntentRequest' &&
      handlerInput.requestEnvelope.request.intent.name === 'AMAZON.HelpIntent'
  },
... 
```

#### 使用后

```
const { canHandle } = require('ask-utils')
const HelpIntentHandler = {
  canHandle (handlerInput) {
    return canHandle(handlerInput, 'IntentRequest', 'AMAZON.HelpIntent')
  },
... 
```

### 容易创建随机消息

Alexa 博客说`Let's randomize your response!`。
Alexa 技能食谱:随机化你的回答以增加你技能的多样性
[https://developer . Amazon . com/ja/blogs/Alexa/post/37e 732 b 7-48fa-4940-9f 12-9 FDE 7 eeeaf 8/Alexa-Skill-Recipe-随机化你的回答以增加你技能的多样性](https://developer.amazon.com/ja/blogs/alexa/post/37e732b7-48fa-4940-9f12-9ffde7eeeaf8/alexa-skill-recipe-randomize-your-responses-to-add-variety-to-your-skill)

所以`ask-utils`提供了辅助函数来进行随机化响应。

#### 在提问之前

你必须创建函数来创建随机的 paharse。

```
function randomPhrase(myData) {

  // the argument is an array [] of words or phrases

  var i = 0;

  i = Math.floor(Math.random() * myData.length);

  return(myData[i]);

}
const welcomeGreetings = ['hello','howdy','hi', 'good day'];
const speachText = randomPhrase(welcomeGreetings); 
```

#### 使用后

`ask-utils`提供助手功能。

```
const { getRandomMessage } = require('ask-utils')
const welcomeGreetings = ['hello','howdy','hi', 'good day'];
const speachText = getRandomMessage(welcomeGreetings); 
```

### 还有更多功能！

#### 获取插槽值

```
const { canHandle, getSlotValueByName } = require('ask-utils')
const ExampleIntentHandler = {
  canHandle (handlerInput) {
    return canHandle(handlerInput, 'IntentRequest', 'ExampleIntent')
  },
  handle (handlerInput) {
    const yourName = getSlotValueByName(handlerInput, 'nameSlot')
    const speechText = `Hello ${yourName} ! Welcome to our skill !` 
```

#### 渐进反应

```
const { enqueueProgressiveResponseDirective } = require('ask-utils') 
const GetFirstEventHandler = {
  canHandle (handlerInput) {
    const request = handlerInput.requestEnvelope.request
    return request.type === 'IntentRequest' && request.intent.name === 'GetFirstEventIntent'
  },
  async handle (handlerInput) {
    try {
      await enqueueProgressiveResponseDirective(handlerInput, 'Please wait for a while')
    } catch (err) {
      // if it failed we can continue, just the user will wait longer for first response
      console.log(err)
    }
    // call some api
    const content = await get()
    return responseBuilder
        .speak(content)
        .getResponse()
  }
} 
```

## 需要您的反馈！

该包正在 GitHub 中开发。
[https://github.com/ask-utils/ask-utils](https://github.com/ask-utils/ask-utils)

并提供了工程实例。
[https://github.com/ask-utils/example](https://github.com/ask-utils/example)

如果你对这些包感兴趣，请尝试一下并给我反馈，功能需求，pul 需求！

谢谢！