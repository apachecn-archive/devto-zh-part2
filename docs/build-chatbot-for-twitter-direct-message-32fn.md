# 为 Twitter 直接消息构建聊天机器人

> 原文：<https://dev.to/sandeshsuvarna/build-chatbot-for-twitter-direct-message-32fn>

# 步骤 1:获取开发者账户

[https://developer.twitter.com/en/apply-for-access](https://developer.twitter.com/en/apply-for-access)
注:审核&审批通常需要 10-15 天。

# 步骤 2:创建 Twitter 应用和开发环境

[https://developer.twitter.com/en/account/get-started](https://developer.twitter.com/en/account/get-started)

# 步骤 3:使用 twitter 开发人员门户为直接消息生成应用程序访问令牌

注意:将应用程序权限更改为“读取、写入和定向消息”，并生成访问令牌。

# 步骤 4:创建节点模块并运行它。

[https://codepen.io/sandeshsuvarna/embed/gdyXWg%20?height=500&default-tab=result&embed-version=2](https://codepen.io/sandeshsuvarna/embed/gdyXWg%20?height=500&default-tab=result&embed-version=2)
运行命令:节点 app.js

# 步骤 5:使用 Ngrok 隧道连接到您的本地主机 webhook

使用终端/命令提示符在同一目录下运行以下命令:ngrok http 1337
复制“https”URL。(会是类似 [https://XXXXXX.ngrok.io](https://XXXXXX.ngrok.io) 的东西)

# 步骤 6:下载帐户活动仪表板

git clone[https://github . com/Twitter dev/account-activity-dashboard . git](https://github.com/twitterdev/account-activity-dashboard.git)
使用终端/命令提示符使用“npm start”运行模块

# 步骤 7:连接 Webhook

在浏览器上打开“localhost:5000”。
点击“管理 Webhook”
将“ngrok url”粘贴到“创建或更新 web hook”字段&点击提交

# 步骤 8:添加用户/页面订阅

打开终端/命令提示符
转到“账户活动仪表板”文件夹
执行“节点示例 _ 脚本/订阅 _ 管理/添加-订阅-app-owner . js-e”
注意:为拥有 app 的用户添加用户订阅。

## 转到 Twitter DM &开始和你的机器人说话

### 感谢阅读！:)如果你喜欢这篇文章，点击❤下面的心形按钮对我来说意义重大，它有助于其他人看到这个故事。