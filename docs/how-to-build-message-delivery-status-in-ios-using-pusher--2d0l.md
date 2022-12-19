# 如何使用 Pusher 在 iOS 中建立消息传递状态

> 原文：<https://dev.to/neo/how-to-build-message-delivery-status-in-ios-using-pusher--2d0l>

在构建移动聊天应用程序时，经常会看到开发人员添加一个传递状态功能，让您知道您发送的消息何时已经传递给了收件人。WhatsApp、Messenger、BBM、Skype 等即时通讯应用都提供这一功能。

本文将重点介绍如何使用 Swift 和 [Pusher](http://pusher.com) 将这一特性添加到 iOS 移动应用程序中。

构建我们的应用程序需要的一些工具有:

1.  [**Xcode**](https://developer.apple.com/xcode/)——应用将使用苹果的 Swift 编程语言构建。
2.  [**NodeJS**](https://nodejs.org/en/download/)**(Express)**——后台应用会用 NodeJS 编写。
3.  [**推送器**](https://pusher.com/)——推送器将在发送的消息送达时提供实时报告。您将需要一个 Pusher 应用程序 ID、密钥和密码。在 pusher.com[创建您的免费账户](https://pusher.com)，然后从 Pusher 仪表盘获取您的应用 ID、密钥和密码。

下面是我们将要构建的内容的屏幕记录。正如你所看到的，当一条信息被发送时，它被标记为已发送，当它到达收件人的手机时，它被标记为已发送。

[![](../Images/3d1fa5b7f38768f59f094c1c2bcef5e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LXRWfcPM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/09/build-message-delivery-status-ios-using-pusher-2.gif)

## iOS 应用入门

启动 Xcode 并创建一个新项目。我们称我们的**为匿名聊天**。一旦加载了工作空间，关闭 Xcode，然后在项目的根目录下运行命令`pod init`。这将为您生成一个`Podfile`。改变`Podfile`的内容:

```
 # Uncomment the next line to define a global platform for your project
    platform :ios, '9.0'

    target 'anonchat' do
      # Comment the next line if you're not using Swift and don't want to use dynamic frameworks
      use_frameworks!

      # Pods for anonchat
      pod 'Alamofire'
      pod 'PusherSwift'
      pod 'JSQMessagesViewController'
    end 
```

Enter fullscreen mode Exit fullscreen mode

现在运行命令`pod install`,这样 Cocoapods 包管理器就可以获取必要的依赖项。完成后，关闭 Xcode(如果打开的话),然后打开项目文件夹根目录下的`.xcworkspace`文件。

## 为我们的 iOS 应用创建视图

我们将创建几个视图，以便聊天应用程序正常运行。这些视图将类似于下面的截图:

[![](../Images/ce215d9b94bde062f350bcc80490321f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ENc6Zpuz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/09/build-message-delivery-status-ios-using-pusher-3.png)

我们上面所做的是创建第一个 ViewController，它将作为我们的 welcome ViewController，我们还添加了一个按钮，它触发导航到下一个控制器`Navigation Controller`。这个导航控制器又将一个视图控制器设置为根控制器。

## 为我们的 iOS 应用程序编码消息传递状态

现在我们已经使用`MainStoryboard`上的界面构建器设置了视图，让我们添加一些功能。我们要做的第一件事是创建一个`WelcomeViewController`并将它与左边的第一个视图关联起来。这将是那个视图的逻辑房子；我们现在不会添加太多:

```
 import UIKit

    class WelcomeViewController: UIViewController {
        override func viewDidLoad() {
            super.viewDidLoad()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们创建另一个名为`ChatViewController`的控制器，它将是主电源，一切都将在这里发生。控制器将扩展`JSQMessagesViewController`,以便我们自动获得一个开箱即用的聊天界面，然后我们必须定制这个聊天界面为我们工作。

```
 import UIKit
    import Alamofire
    import PusherSwift
    import JSQMessagesViewController

    class ChatViewController: JSQMessagesViewController {
        override func viewDidLoad() {
            super.viewDidLoad()

            let n = Int(arc4random_uniform(1000))

            senderId = "anonymous" + String(n)
            senderDisplayName = senderId
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果您注意到了`viewDidLoad`方法，我们正在生成一个随机用户名，并将其设置为控制器上的`senderId`和`senderDisplayName`。这扩展了父控制器中设置的属性，并且是必需的。

在我们继续讨论聊天控制器之前，我们想创建最后一个名为`AnonMessage`的类。这将扩展`JSQMessage`类，我们将使用它来扩展该类的默认功能。

```
 import UIKit
    import JSQMessagesViewController

    enum AnonMessageStatus {
        case sending
        case sent
        case delivered
    }

    class AnonMessage: JSQMessage {
        var status : AnonMessageStatus
        var id : Int

        public init!(senderId: String, status: AnonMessageStatus, displayName: String, text: String, id: Int?) {
            self.status = status

            if (id != nil) {
                self.id = id!
            } else {
                self.id = 0
            }

            super.init(senderId: senderId, senderDisplayName: displayName, date: Date.init(), text: text)
        }

        public required init?(coder aDecoder: NSCoder) {
            fatalError("init(coder:) has not been implemented")
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的类中，我们扩展了`JSQMessage`类，我们也添加了一些新的属性来跟踪:`id`和`status`。我们还添加了一个初始化方法，这样我们可以在正确实例化`JSQMessage`类之前指定新的属性。我们还添加了一个`enum`,包含消息可能具有的所有状态。

回到`ChatViewController`，让我们添加一些我们需要的属性到类中:

```
 static let API_ENDPOINT = "http://localhost:4000";

    var messages = [AnonMessage]()
    var pusher: Pusher!

    var incomingBubble: JSQMessagesBubbleImage!
    var outgoingBubble: JSQMessagesBubbleImage! 
```

Enter fullscreen mode Exit fullscreen mode

现在已经完成了，让我们开始定制控制器来满足我们的需求。首先，我们将为`viewDidLoad`方法添加一些逻辑:

```
 override func viewDidLoad() {
        super.viewDidLoad()

        let n = Int(arc4random_uniform(1000))

        senderId = "anonymous" + String(n)
        senderDisplayName = senderId

        inputToolbar.contentView.leftBarButtonItem = nil

        incomingBubble = JSQMessagesBubbleImageFactory().incomingMessagesBubbleImage(with: UIColor.jsq_messageBubbleBlue())
        outgoingBubble = JSQMessagesBubbleImageFactory().outgoingMessagesBubbleImage(with: UIColor.jsq_messageBubbleGreen())

        collectionView!.collectionViewLayout.incomingAvatarViewSize = CGSize.zero
        collectionView!.collectionViewLayout.outgoingAvatarViewSize = CGSize.zero

        automaticallyScrollsToMostRecentMessage = true

        collectionView?.reloadData()
        collectionView?.layoutIfNeeded()
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们开始定制聊天界面的外观，使用已经设置了这些属性的父类。例如，我们将`incomingBubble`设置为蓝色，将`outgoingBubble`设置为绿色。我们还取消了头像显示，因为我们现在不需要它。

下一步我们要做的是覆盖父控制器的一些方法，这样我们就可以显示消息，自定义感觉等等

```
 override func collectionView(_ collectionView: JSQMessagesCollectionView!, messageDataForItemAt indexPath: IndexPath!) -> JSQMessageData! {
        return messages[indexPath.item]
    }

    override func collectionView(_ collectionView: JSQMessagesCollectionView!, attributedTextForCellBottomLabelAt indexPath: IndexPath!) -> NSAttributedString! {
        if !isAnOutgoingMessage(indexPath) {
            return nil
        }

        let message = messages[indexPath.row]

        switch (message.status) {
        case .sending:
            return NSAttributedString(string: "Sending...")
        case .sent:
            return NSAttributedString(string: "Sent")
        case .delivered:
            return NSAttributedString(string: "Delivered")
        }
    }

    override func collectionView(_ collectionView: JSQMessagesCollectionView!, layout collectionViewLayout: JSQMessagesCollectionViewFlowLayout!, heightForCellBottomLabelAt indexPath: IndexPath!) -> CGFloat {
        return CGFloat(15.0)
    }

    override func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return messages.count
    }

    override func collectionView(_ collectionView: JSQMessagesCollectionView!, messageBubbleImageDataForItemAt indexPath: IndexPath!) -> JSQMessageBubbleImageDataSource! {
        let message = messages[indexPath.item]
        if message.senderId == senderId {
            return outgoingBubble
        } else {
            return incomingBubble
        }
    }

    override func collectionView(_ collectionView: JSQMessagesCollectionView!, avatarImageDataForItemAt indexPath: IndexPath!) -> JSQMessageAvatarImageDataSource! {
        return nil
    }

    override func didPressSend(_ button: UIButton, withMessageText text: String, senderId: String, senderDisplayName: String, date: Date) {
        let message = addMessage(senderId: senderId, name: senderId, text: text, id: nil)

        if (message != nil) {
            postMessage(message: message as! AnonMessage)
        }

        finishSendingMessage(animated: true)
    }

    private func isAnOutgoingMessage(_ indexPath: IndexPath!) -> Bool {
        return messages[indexPath.row].senderId == senderId
    } 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们要做的是在控制器上创建一些新的方法来帮助我们发布新的消息。之后，我们创建一个方法来访问发送消息的远程端点。最后，我们创建一个方法将发送(或接收)的新消息附加到消息数组:

```
 private func postMessage(message: AnonMessage) {
        let params: Parameters = ["sender": message.senderId, "text": message.text]
        hitEndpoint(url: ChatViewController.API_ENDPOINT + "/messages", parameters: params, message: message)
    }

    private func hitEndpoint(url: String, parameters: Parameters, message: AnonMessage? = nil) {
        Alamofire.request(url, method: .post, parameters: parameters).validate().responseJSON { response in
            switch response.result {
            case .success(let JSON):
                let response = JSON as! NSDictionary

                if message != nil {
                    message?.id = (response.object(forKey: "ID") as! Int) as Int
                    message?.status = .sent
                    self.collectionView.reloadData()
                }

            case .failure(let error):
                print(error)
            }
        }
    }

    private func addMessage(senderId: String, name: String, text: String, id: Int?) -> Any? {
        let status = AnonMessageStatus.sending

        let id = id == nil ? nil : id;

        let message = AnonMessage(senderId: senderId, status: status, displayName: name, text: text, id: id)

        if (message != nil) {
            messages.append(message as AnonMessage!)
        }

        return message
    } 
```

Enter fullscreen mode Exit fullscreen mode

太好了。现在，每当我们发送一条新消息时，`didPressSend`方法就会被触发，所有其他的方法都会很好地就位！

对于这个难题的最后一部分，我们想要创建一个方法来监听 Pusher 事件，并在收到事件触发器时触发一个回调:

```
 private func listenForNewMessages() {
        let options = PusherClientOptions(
            host: .cluster("PUSHER_CLUSTER")
        )

        pusher = Pusher(key: "PUSHER_KEY", options: options)

        let channel = pusher.subscribe("chatroom")

        channel.bind(eventName: "new_message", callback: { (data: Any?) -> Void in
            if let data = data as? [String: AnyObject] {
                let messageId = data["ID"] as! Int
                let author = data["sender"] as! String

                if author != self.senderId {
                    let text = data["text"] as! String

                    let message = self.addMessage(senderId: author, name: author, text: text, id: messageId) as! AnonMessage?
                    message?.status = .delivered

                    let params: Parameters = ["ID":messageId]
                    self.hitEndpoint(url: ChatViewController.API_ENDPOINT + "/delivered", parameters: params, message: nil)

                    self.finishReceivingMessage(animated: true)
                }
            }
        })

        channel.bind(eventName: "message_delivered", callback: { (data: Any?) -> Void in
            if let data = data as? [String: AnyObject] {
                let messageId = (data["ID"] as! NSString).integerValue
                let msg = self.messages.first(where: { $0.id == messageId })

                msg?.status = AnonMessageStatus.delivered
                self.finishReceivingMessage(animated: true)
            }
        })

        pusher.connect()
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这个方法中，我们已经创建了一个`Pusher`实例，我们已经设置了集群和键。我们将实例附加到一个`chatroom`通道，然后绑定到通道上的`new_message`事件。我们还绑定了一个`message_delivered`事件，这将是当消息被标记为已传递时触发的事件。它会将消息状态更新为`delivered`，这样发送者就知道消息确实已经被发送了。

> 💡**记得用您从 Pusher 仪表盘**中获得的实际值 **替换密钥和集群。**

现在，我们应该完成了应用程序，就其现状而言，它应该可以工作，但还不能发送任何消息，因为我们需要一个后端应用程序才能正常工作。

**构建后端节点应用**

现在我们已经完成了 iOS 和 Xcode 部分，我们可以为应用程序创建 NodeJS 后端了。我们将使用 Express，这样我们可以很快做出一些东西。

为 web 应用程序创建一个目录，然后创建两个新文件:

**index.js** 文件……

```
 // ------------------------------------------------------
    // Import all required packages and files
    // ------------------------------------------------------

    let Pusher     = require('pusher');
    let express    = require('express');
    let bodyParser = require('body-parser');
    let Promise    = require('bluebird');
    let db         = require('sqlite');
    let app        = express();
    let pusher     = new Pusher(require('./config.js')['config']);

    // ------------------------------------------------------
    // Set up Express
    // ------------------------------------------------------

    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: false }));

    // ------------------------------------------------------
    // Define routes and logic
    // ------------------------------------------------------

    app.post('/delivered', (req, res, next) => {
      let payload = {ID: ""+req.body.ID+""}
      pusher.trigger('chatroom', 'message_delivered', payload)
      res.json({success: 200})
    })

    app.post('/messages', (req, res, next) => {
      try {
        let payload = {
          text: req.body.text,
          sender: req.body.sender
        };

        db.run("INSERT INTO Messages (Sender, Message) VALUES (?,?)", payload.sender, payload.text)
          .then(query => {
            payload.ID = query.stmt.lastID
            pusher.trigger('chatroom', 'new_message', payload);

            payload.success = 200;

            res.json(payload);
          });

      } catch (err) {
        next(err)
      }
    });

    app.get('/', (req, res) => {
      res.json("It works!");
    });

    // ------------------------------------------------------
    // Catch errors
    // ------------------------------------------------------

    app.use((req, res, next) => {
        let err = new Error('Not Found');
        err.status = 404;
        next(err);
    });

    // ------------------------------------------------------
    // Start application
    // ------------------------------------------------------

    Promise.resolve()
      .then(() => db.open('./database.sqlite', { Promise }))
      .then(() => db.migrate({ force: 'last' }))
      .catch(err => console.error(err.stack))
      .finally(() => app.listen(4000, function(){
        console.log('App listening on port 4000!')
      })); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了后端应用程序的整个逻辑。我们还使用 SQLite 来存储聊天消息；这有助于识别消息。当然，您可以随时改变应用程序的工作方式来满足您的需求。

`index.js`文件也有两个路径，它从 iOS 应用程序接收消息，并触发应用程序获取的 Pusher 事件。

下一个文件是 **packages.json** ，在这里我们定义了 NPM 依赖项:

```
 {
      "main": "index.js",
      "dependencies": {
        "bluebird": "^3.5.0",
        "body-parser": "^1.16.0",
        "express": "^4.14.1",
        "pusher": "^1.5.1",
        "sqlite": "^2.8.0"
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

您还需要根目录下的 config.js 文件:

```
 module.exports = {
        appId: '',
        key: '',
        secret: '',
        cluster: '',
    }; 
```

Enter fullscreen mode Exit fullscreen mode

用您的推杆应用中的实际值替代。现在在目录上运行`npm install`，然后在 npm 安装完成后运行`node index.js`。你应该看到一个*应用程序在 4000 端口监听！*消息。

[![](../Images/069e8721d24afd901a271c9d1cfcfdb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kdRvaRZH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/09/build-message-delivery-status-ios-using-pusher-5.png) 屏幕显示当前运行的节点应用程序。

## 测试应用程序

一旦您的本地节点 web 服务器开始运行，您将需要进行一些更改，以便您的应用程序可以与本地 web 服务器通信。在`info.plist`文件中，进行以下更改:

[![](../Images/81e5bb651bcfa2999c323237dfcb3917.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EtIPKiNO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/09/build-message-delivery-status-ios-using-pusher-4.png) 更改 info.plist 文件以允许 iOS 连接到本地主机

通过这一更改，您可以构建并运行您的应用程序，它将直接与您的本地 web 应用程序对话。

## 结论

在本文中，我们探讨了如何创建一个 iOS 聊天应用程序，在将消息发送给其他用户后，该应用程序会显示消息传递状态消息。实际上，您可以扩展状态以支持更多实例。

对文章有问题或反馈吗？请在评论区下面提问。应用程序和节点后端的存储库在这里[可用](https://github.com/neoighodaro/message-delivery-status-ios)。

这篇文章最初出现在 [Pusher 博客](https://blog.pusher.com/build-message-delivery-status-in-ios-using-pusher/)上。