# 具有矩阵的服务通知

> 原文：<https://dev.to/halfshot/service-notifs-with-matrix-3fb5>

```
import ...

const log = new Log("MatrixProvider")

export class MatrixProvider {
    private client: any;

    constructor(private matrixConfig: any) {
    }

    Setup() {
        this.client = Matrix.createClient({
            baseUrl: this.matrixConfig.homeserver,
            userId: this.matrixConfig.user_id,
            accessToken: this.matrixConfig.access_token
        });
    }

    SendMessage(roomid: string, body: string): Promise<any> {
        return this.client.sendHtmlMessage(roomid, striptags(body), body);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是所有需要添加到我们的基础设施中的代码，以便开始向网络上的员工发送警报通知。当需要在服务内部发送警报时，Matrix 是我的首选。它很快，这是一个单一的 HTTP 请求，你得到免费的推送！

## 问题

我工作的应用程序向三组人发送通知:管理员、用户和客户。

*   **管理员**需要发现请求是否在后端反复失败。
*   **用户**需要了解客户是否填写了表格或发送了评论。
*   如果分配给**客户**的产品有变化，需要通知他们。

在我参与的服务中，最大的消息传递问题是每个应用都有自己的一套通知规则，通常隐藏在一些配置中。如果你改变一个组，或者某个地方的名字，你可能会发现自己重写了大量的配置文件——更不用说超出标准`sendmail`设置的可扩展性了。

## 解

我们的数据库中的单个服务、单个新消息队列类型和单个映射文档。

服务简单而优雅。它连接到 [CouchDB](https://couchdb.apache.org/) ，读取地图文档并监听变化(CDB 的一个很酷的特性)。

一个典型的绘图文件:

```
{  "_id":"ccs.notifications",  "_rev":"xyz",  "mapping":  {  "#admins":  {  "matrix":  ["!PxMWbvomtRqGOTsaDK:half-shot.uk"],  "email":  ["will@half-shot.uk"]  "twitter":..,  "slack":..,  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当有消息进来时，我们将`content.to`映射到一个映射字段(必须以`#`开头)。获得映射后，我们遍历并向一个或多个房间发送通知。然后，您可以相信 Matrix 主服务器会将其交付给合适的用户。将来，如果用户没有检查他们的 Riot 应用程序，我们可能会检查已读回执的状态，并通过电子邮件提醒用户。

[![Result in Riot](img/88ce4ef3439b98e2da0a8ee68afc7b38.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--exrWFz6v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9tnfbrqyh86qmn6ayfmq.png)

将来，我们可以对此进行扩展，以包括面向客户的 E2E 消息传递，这样他们可以确保消息来自正确的服务，而不是来自攻击者——如果访问令牌泄露的话。

无论如何，这是一个关于发送通知的相当优雅的解决方案的快速帖子。