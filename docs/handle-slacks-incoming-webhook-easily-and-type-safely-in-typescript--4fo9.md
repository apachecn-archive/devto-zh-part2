# 在 TypeScript 中轻松处理 Slack 的传入 Webhook 并确保类型安全

> 原文：<https://dev.to/star__hoshi/handle-slacks-incoming-webhook-easily-and-type-safely-in-typescript--4fo9>

[slackapi/node-slack-sdk](https://github.com/slackapi/node-slack-sdk) 是很棒的 slack 客户端。但是，它不包含 d.ts 文件。所以我做了一个带类型定义的库。

[typed-slack](https://github.com/starhoshi/typed-slack) 有类型定义，所以你可以容易地、类型安全地处理 slack-api。

使用 VSCode 时启用代码完成。

[![vscode\.gif \(898×298\)](img/95dde8c0ed30d7e29fa3b621c396d5d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xhkRTt8---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/starhoshi/typed-slack/master/docs/vscode.gif)

## 安装

```
npm install typed-slack 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

### Webhook

```
import * as Slack from 'typed-slack'

let slack = new Slack.IncomingWebhook('https://hooks.slack.com/services/.......')
slack.send({ text: 'text' }).then(e => {
  console.log('success')
}).catch(e => {
  console.error(e)
}) 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用选项

更多可选参数有[这里#typed-slack.d.ts](https://github.com/starhoshi/typed-slack/blob/master/out/typed-slack.d.ts)

```
const options = <Slack.IncomingWebhookOptions>{
  text: '@star__hoshi Hi!',
  channel: 'debug',
  icon_emoji: ':smile:',
  link_names: 1,
  attachments: [
    {
      color: Slack.Color.Danger,
      fields: [
        {
          title: 'Priority',
          value: 'High',
          short: false
        }
      ],
      image_url: 'http://my-website.com/path/to/image.jpg',
      ts: 123456789
    }
  ]
}
await slack.send(options) 
```

Enter fullscreen mode Exit fullscreen mode

## [starhoshi/typed-slack](https://github.com/starhoshi/typed-slack)

请使用[型松紧](https://github.com/starhoshi/typed-slack)！

[![](img/5a97db3d7a39822ad384dd324227937d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AxJnohXO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/starhoshi/typed-slack/master/docs/logo.png)