# 创建电报机器人的一小步

> 原文：<https://dev.to/egitsptr/small-step-to-create-telegram-bot-n8a>

1.  打开与 [@BotFather 的对话。](https://telegram.me/BotFather)
2.  发布`/start`，你会看到:

[![telegram_bot_help](../Images/0ddbbc166c48701fa7c6a446b82e62aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BT6Q7t9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://esabook.files.wordpress.com/2018/08/telegram_bot_help.png%3Fw%3D343%26h%3D451)

1.  帖子`/newbot`
2.  帖子**名称**和**用户名**
3.  完成，在你得到像`680788960:AAEKP9xGSZK9uyWGGouZFYmTkCN8i_y0w`一样的`​token`之后

# 发送消息到通道:

1.  打开 url 或者 get = >[https://api.telegram.org/bot](https://api.telegram.org/bot)token/sendMessage？聊天 _ id =聊天 _id &文本=测试
2.  完成的

# 寻找**聊天 _id:**

1.  将机器人添加为`​channel administrator`
2.  Post `/start`发送 bot 命令
3.  打开网址或者获取= >[https://api.telegram.org/bottoken/getupdates](https://api.telegram.org/bottoken/getupdates)
4.  参见`channel_post`。`chat`。`id`
5.  完成的

更多 api 请访问[https://core.telegram.org/bots/api](https://core.telegram.org/bots/api)