# 如何使用 Swift 构建实时表格

> 原文：<https://dev.to/neo/how-to-build-a-realtime-table-using-swift--cje>

通常，当您构建供他人使用的应用程序时，您需要在某种表或列表中表示数据。例如，考虑一个用户列表，或者一个充满足球联盟数据的表。现在，假设填充表的数据将被重新排序或更改，如果查看表上数据的每个人都能立即看到所做的更改，那就太好了。

在本文中，您将看到如何使用 iOS 和 Pusher 创建一个在所有设备上实时更新的表格。你可以在下面看到应用程序如何工作的屏幕记录。

[![](img/e069d38fd884f295afd9cdeee7feccac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vA5uoWfv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/11/How-to-build-a-realtime-table-using-Swift-6.gif)

在上面的记录中，您可以看到在一个设备上对表所做的更改是如何立即镜像到另一个设备上的。让我们考虑如何使用 Pusher 和 Swift 来实现这一点。

## 在 iOS 上构建实时表格的要求

为了完成本教程，您需要满足以下所有要求:
–安装在您机器上的 MacBook Pro
–[Xcode](https://developer.apple.com/xcode/)–[Swift](https://developer.apple.com/swift/)和使用 Xcode
的基本知识–安装在您机器上的 JavaScript(node . js)
–[node . js](https://docs.npmjs.com/getting-started/installing-node)和 NPM
–[Cocoapods](http://www.raywenderlich.com/12139/introduction-to-cocoapods)* * * *的基本知识。
–一个[推杆](https://pusher.com)应用。

一旦你具备了以下所有条件，那么让我们继续这篇文章。

## 准备我们的环境以创建我们的应用程序

启动 Xcode 并创建一个新项目。遵循新建应用程序向导，创建一个新的**单页应用程序**。创建项目后，关闭 Xcode 并启动终端。

在终端窗口中，`cd`到 app 目录的根目录，运行命令`pod init`。这将生成一个 **Podfile** 。

将 **Podfile** 的内容更新为以下内容(将`PROJECT_NAME`替换为您的项目名称):

```
 platform :ios, '9.0'
    target 'PROJECT_NAME' do
      use_frameworks!
      pod 'PusherSwift', '~> 4.1.0'
      pod 'Alamofire', '~> 4.4.0'
    end 
```

Enter fullscreen mode Exit fullscreen mode

保存 **Podfile** ，然后在您的终端窗口上运行命令:`pod install`。运行这个命令将安装我们构建实时应用程序所需的所有第三方包。

安装完成后，打开项目根目录下的`**.xcworkspace**`文件。这应该会启动 Xcode。现在我们准备开始创建我们的 iOS 应用程序。

## 在 iOS 上构建我们的实时表格的用户界面

一旦 Xcode 完成加载，我们现在就可以开始构建我们的界面了。

打开`Main.storyboard`文件。将导航控制器拖放到情节提要中，并将入口点设置为新的导航控制器。现在，您的故事板中应该有这样的内容:

[![](img/762db5c6e8cdd2ee8ee041c420762711.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RoracnMA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/11/How-to-build-a-realtime-table-using-Swift-1.png)

正如在截图中看到的，我们有一个简单的导航控制器，我们已经将附加到它的表格视图控制器作为我们的根视图控制器。

现在我们需要向表格单元格添加一个重用标识符。单击原型单元格，并添加一个新的重用标识符。

[![](img/a11ba904ca2ef1016ee8f6c4d49fe992.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U1RfBh4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/11/How-to-build-a-realtime-table-using-Swift-2.png)

我们已经将我们的重用标识符命名为用户**T2，但是您可以随意称呼这个重用标识符。接下来，创建一个新的`TableViewController`，并使用故事板的身份检查器将它附加到根视图控制器，如下所示:**

[![](img/a7eaff1d61138d953ed29f1eea73e4c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4mnDGx0z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/11/How-to-build-a-realtime-table-using-Swift-3.png)

太好了！现在我们已经完成了应用程序的用户界面，让我们开始创建将填充并使我们的 iOS 表实时化的逻辑。

## 用用户数据填充我们的 iOS 表并操作它

我们要做的第一件事是用一些模拟数据填充我们的表。一旦我们做到了这一点，我们就可以添加和测试我们希望表拥有的所有可能的操作，比如移动行、删除行和向表中添加新行。

打开你的`UserTableViewController`。现在删除文件中除了`viewDidLoad`之外的所有函数，这样文件就清晰了。你应该有这样的东西，当你完成:

```
 import UIKit

    class UserTableViewController: UITableViewController {

        override func viewDidLoad() {
            super.viewDidLoad()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加一些模拟数据。创建一个新函数，该函数应该从 API 加载数据。但是现在，我们将对数据进行硬编码。将以下功能添加到控制器:

```
 private func loadUsersFromApi() {
        users = [
            [
                "id": 1,
                "name" : "John Doe",
            ],
            [
                "id": 2,
                "name": "Jane Doe"
            ]
        ]
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在实例化类声明下类右边的`users`属性:

```
 var users:[NSDictionary] = [] 
```

Enter fullscreen mode Exit fullscreen mode

最后，在`viewDidLoad`函数中，调用`loadUsersFromApi`方法:

```
 override func viewDidLoad() {
        super.viewDidLoad()

        loadUsersFromApi()
    } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要添加所有的函数，使我们的表视图控制器与
`UITableViewController`兼容，从而显示我们的数据。将以下函数添加到视图控制器:

```
 // MARK: - Table view data source

    override func numberOfSections(in tableView: UITableView) -> Int {
        return 1
    }

    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return users.count
    }

    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "user", for: indexPath)
        cell.textLabel?.text = users[indexPath.row]["name"] as! String?
        return cell
    }

    override func tableView(_ tableView: UITableView, moveRowAt sourceIndexPath: IndexPath, to destinationIndexPath: IndexPath) {
        let movedObject = users[sourceIndexPath.row]
        users.remove(at: sourceIndexPath.row)
        users.insert(movedObject, at: destinationIndexPath.row)
    }

    override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {
            self.users.remove(at: indexPath.row)
            self.tableView.deleteRows(at: [indexPath], with: .automatic)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码有 5 个功能。第一个函数告诉表格我们的表格有多少部分。下一个函数告诉表有多少用户(或行)。第三个函数在每次创建一行时被调用，负责用数据填充单元格。第四个和第五个函数是回调函数，分别在移动或删除数据时调用。

现在，如果您运行您的应用程序，您应该看到显示的模拟数据。但是，我们看不到“添加”或“编辑”按钮。因此，让我们添加该功能。

在`viewDidLoad`函数中添加以下几行:

```
 navigationItem.title = "Users List"
    navigationItem.rightBarButtonItem = self.editButtonItem
    navigationItem.leftBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(showAddUserAlertController)) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们添加了两个按钮，左按钮和右按钮。左边是添加按钮，右边是编辑按钮。

在 add 按钮中，它调用一个`showAddUserAlertController`方法。我们的代码中还没有定义，所以让我们添加它。将以下功能添加到您的视图控制器:

```
 public func showAddUserAlertController() {
        let alertCtrl = UIAlertController(title: "Add User", message: "Add a user to the list", preferredStyle: .alert)

        // Add text field to alert controller
        alertCtrl.addTextField { (textField) in
            self.textField = textField
            self.textField.autocapitalizationType = .words
            self.textField.placeholder = "e.g John Doe"
        }

        // Add cancel button to alert controller
        alertCtrl.addAction(UIAlertAction(title: "Cancel", style: .cancel, handler: nil))

        // "Add" button with callback
        alertCtrl.addAction(UIAlertAction(title: "Add", style: .default, handler: { action in
            if let name = self.textField.text, name != "" {
                self.users.append(["id": self.users.count, "name" :name])
                self.tableView.reloadData()
            }
        }))

        present(alertCtrl, animated: true, completion: nil)
    } 
```

Enter fullscreen mode Exit fullscreen mode

当单击 add 按钮时，代码只是创建一个警告。该警报有一个`textField`，它将获取您想要添加的用户的名称，并将其附加到`users`属性中。

现在，让我们在类声明:
之后声明控制器上的`textField`属性

```
 var textField: UITextField! 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了一个没有连接到任何 API 的工作原型。如果您在此时运行应用程序，您将能够看到所有的函数，它们将会工作，但是不会被持久化，因为它是硬编码的。

[![](img/8d1e69a5da2b036c389ca86751b58b60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BaSpmWzc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/11/How-to-build-a-realtime-table-using-Swift-4.png)

很好，但是现在我们需要添加一个数据源。为此，我们将需要创建一个 Node.js 后端，然后我们的应用程序将能够调用它来检索数据。此外，当通过重新排序或删除修改数据时，请求被发送到后端，更改被存储在那里。

## 向我们的 iOS 表格应用程序添加 API 调用

现在，让我们从尚未创建的远程数据源中检索数据开始(我们将在本文后面创建它)。

**从 API** 加载用户
返回到`loadUsersFromApi`方法，用下面的代码替换内容:

```
 private func loadUsersFromApi() {
        indicator.startAnimating()

        Alamofire.request(self.endpoint + "/users").validate().responseJSON { (response) in
            switch response.result {
            case .success(let JSON):
                self.users = JSON as! [NSDictionary]
                self.tableView.reloadData()
                self.indicator.stopAnimating()
            case .failure(let error):
                print(error)
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的方法使用 **Alamofire** 来调用 a `self.endpoint`，然后将响应存储到`self.users`。它还调用了一个`indicator.startAnimating()`，这应该显示一个数据正在加载的指示器。

在我们创建加载指示器之前，让我们先来看一下`import Alamofire`。在`import UIKit`语句下，添加下面的代码行:

```
 import Alamofire 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！现在，让我们创建已经在上面的`loadUsersFromApi`函数中被调用的加载指示器。

首先，在控制器类声明:
之后声明类中的`indicator`和`endpoint`

```
 var endpoint = "http://localhost:4000"
    var indicator = UIActivityIndicatorView() 
```

Enter fullscreen mode Exit fullscreen mode

> 💡**`**endpoint**`**在您为真实环境进行开发时，需要将它改为您的 web 服务器的 URL。****

 **现在，创建一个函数来初始化和配置加载指示器。将以下功能添加到控制器:

```
 private func setupActivityIndicator() {
        indicator = UIActivityIndicatorView(frame: CGRect(x: 0, y: 0, width: 50, height: 50))
        indicator.activityIndicatorViewStyle = .white
        indicator.backgroundColor = UIColor.darkGray
        indicator.center = self.view.center
        indicator.layer.cornerRadius = 05
        indicator.hidesWhenStopped = true
        indicator.layer.zPosition = 1
        indicator.isOpaque = false
        indicator.tag = 999
        tableView.addSubview(indicator)
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数将简单地设置我们的`UIActivityIndicatorView`，它只是一个指示我们的数据正在加载的微调器。设置装载视图后，我们将它添加到表格视图中。

> 💡**我们将** `**hidesWhenStopped**` **属性设置为** `**true**` **，这意味着每次我们使用** `**stopAnimating**` **停止指标时，指标会自动隐藏。**

现在，在`viewDidLoad`函数中，在对`loadUsersFromApi`的调用之上，添加对`setupActivityIndicator` :
的调用

```
 override func viewDidLoad() {
        // other stuff...
        setupActivityIndicator()
        loadUsersFromApi()
    } 
```

Enter fullscreen mode Exit fullscreen mode

在调用`loadUsersFromApi`调用之前添加它将确保在加载用户函数调用中引用它之前已经创建了指示器。

**将用户添加到 API，然后本地添加到表中**
现在，让我们将“Add”按钮挂接到我们的后端，这样当使用 textfield 添加用户时，一个请求将被发送到端点。

在`showAddUserAlertController`中我们会做一些修改。替换下面的行:

```
 if let name = self.textField.text, name != "" {
        self.users.append(["id": self.users.count, "name" :name])
        self.tableView.reloadData()
    } 
```

Enter fullscreen mode Exit fullscreen mode

用这个:

```
 if let name = self.textField.text, name != "" {
        let payload: Parameters = ["name": name, "deviceId": self.deviceId]

        Alamofire.request(self.endpoint + "/add", method: .post, parameters:payload).validate().responseJSON { (response) in
            switch response.result {
            case .success(_):
                self.users.append(["id": self.users.count, "name" :name])
                self.tableView.reloadData()
            case .failure(let error):
                print(error)
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在，在上面的代码块中，我们向端点发送一个请求，而不是直接操作`users`属性。如果请求成功，我们就将新数据追加到`users`属性中。但是，如果你注意到，在`payload`中我们引用了`self.deviceId`，所以我们需要创建这个属性。在类声明之后添加下面的代码:

```
 let deviceId = UIDevice.current.identifierForVendor!.uuidString 
```

Enter fullscreen mode Exit fullscreen mode

> 💡**我们正在添加设备 ID，这样我们可以区分谁对后端进行了什么调用，并避免在发送请求的是同一个** **设备** **的情况下多次操纵数据。当我们集成 Pusher 时，监听器将对** `**user**` **属性进行同样的操作。** **然而，如果是同一台设备发出了请求，那么它应该跳过更新属性****。**

**在 API 中移动用户，然后本地移动到表中**
接下来是添加远程移动功能。让我们把它连接起来，与端点进行通信。

在您的代码中，替换下面的函数:

```
 override func tableView(_ tableView: UITableView, moveRowAt sourceIndexPath: IndexPath, to destinationIndexPath: IndexPath) {
        let movedObject = users[sourceIndexPath.row]
        users.remove(at: sourceIndexPath.row)
        users.insert(movedObject, at: destinationIndexPath.row)
    } 
```

Enter fullscreen mode Exit fullscreen mode

有了这个:

```
 override func tableView(_ tableView: UITableView, moveRowAt sourceIndexPath: IndexPath, to destinationIndexPath: IndexPath) {
        let movedObject = users[sourceIndexPath.row]

        let payload:Parameters = [
            "deviceId": self.deviceId,
            "src":sourceIndexPath.row,
            "dest": destinationIndexPath.row,
            "src_id": users[sourceIndexPath.row]["id"]!,
            "dest_id": users[destinationIndexPath.row]["id"]!
        ]

        Alamofire.request(self.endpoint+"/move", method: .post, parameters: payload).validate().responseJSON { (response) in
            switch response.result {
            case .success(_):
                self.users.remove(at: sourceIndexPath.row)
                self.users.insert(movedObject, at: destinationIndexPath.row)
            case .failure(let error):
                print(error)
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们将有效负载设置为发送到端点，并使用 **Alamofire** 发送它。然后，如果我们从 API 收到一个成功的响应，我们就对`user`属性进行修改。

**在 API 中删除一行，然后在本地删除表中的一行**
接下来我们要做的是在本地删除之前从 API 中删除数据。为此，请查找下面的函数:

```
 override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {
            self.users.remove(at: indexPath.row)
            self.tableView.deleteRows(at: [indexPath], with: .automatic)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

并将其替换为以下代码:

```
 override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {
            let payload: Parameters = [
                "index":indexPath.row,
                "deviceId": self.deviceId,
                "id": self.users[indexPath.row]["id"]!
            ]

            Alamofire.request(self.endpoint + "/delete", method: .post, parameters:payload).validate().responseJSON { (response) in
                switch response.result {
                case .success(_):
                    self.users.remove(at: indexPath.row)
                    self.tableView.deleteRows(at: [indexPath], with: .automatic)
                case .failure(let err):
                    print(err)
                }
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

就像其他人一样，我们刚刚将生成的有效负载发送给 API，然后，如果有成功的响应，我们就从`users`属性中删除该行。

现在，下一件事是创建后端 API。然而，在此之前，让我们使用 Pusher 将实时功能添加到应用程序中。

## 在 iOS 上为我们的表格添加实时功能

现在我们已经完成了 API 的连接，我们需要添加一些实时功能，以便任何其他设备都可以立即获得更改，而不必手动重新加载表。

首先，将 Pusher SDK 导入您的应用程序。在`import Alamofire`语句下，添加以下内容:

```
 import PusherSwift 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们在类声明:
下的类权限中声明`pusher`属性

```
 var pusher: Pusher! 
```

Enter fullscreen mode Exit fullscreen mode

太好了。现在将下面的函数添加到控制器:

```
 private func listenToChangesFromPusher() {
        // Instantiate Pusher
        pusher = Pusher(key: "PUSHER_APP_KEY", options: PusherClientOptions(host: .cluster("PUSHER_APP_CLUSTER")))

        // Subscribe to a pusher channel
        let channel = pusher.subscribe("userslist")

        // Bind to an event called "addUser" on the event channel and fire 
        // the callback when the event is triggerred
        let _ = channel.bind(eventName: "addUser", callback: { (data: Any?) -> Void in
            if let data = data as? [String : AnyObject] {
                if let name = data["name"] as? String {

                    // We only want to run this block if the update was from a 
                    // different device
                    if (data["deviceId"] as! String) != self.deviceId {
                        self.users.append(["id": self.users.count, "name": name])
                        self.tableView.reloadData()
                    }
                }
            }
        })

        // Bind to an event called "removeUser" on the event channel and fire 
        // the callback when the event is triggerred
        let _ = channel.bind(eventName: "removeUser", callback: { (data: Any?) -> Void in
            if let data = data as? [String : AnyObject] {
                if let _ = data["index"] as? Int {
                    let indexPath = IndexPath(item: (data["index"] as! Int), section:0)

                    // We only want to run this block if the update was from a 
                    // different device
                    if (data["deviceId"] as! String) != self.deviceId {
                        self.users.remove(at: indexPath.row)
                        self.tableView.deleteRows(at: [indexPath], with: .automatic)
                    }
                }
            }
        })

        // Bind to an event called "moveUser" on the event channel and fire 
        // the callback when the event is triggerred
        let _ = channel.bind(eventName: "moveUser", callback: { (data: Any?) -> Void in
            if let data = data as? [String : AnyObject] {
                if let _ = data["deviceId"] as? String {
                    let sourceIndexPath = IndexPath(item:(data["src"] as! Int), section:0)
                    let destinationIndexPath = IndexPath(item:(data["dest"] as! Int), section:0)
                    let movedObject = self.users[sourceIndexPath.row]

                    // We only want to run this block if the update was from a 
                    // different device
                    if (data["deviceId"] as! String) != self.deviceId {
                        self.users.remove(at: sourceIndexPath.row)
                        self.users.insert(movedObject, at: destinationIndexPath.row)
                        self.tableView.reloadData()
                    }
                }
            }
        })

        pusher.connect()
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们已经做了很多。首先，我们用应用程序的键和集群实例化 Pusher(替换为 Pusher 应用程序仪表板上提供的详细信息)。接下来，我们订阅了频道`userslist`。我们将监听该频道上的事件。

在第一个`channel.bind`块中，我们绑定到`addUser`事件，然后当一个事件被拾取时，回调运行。

在回调中，我们检查设备 ID，如果不匹配，我们将新用户附加到本地`user`属性。对于`channel.bind`的下两个块也是如此。然而，在其他情况下，它分别删除和移动位置。

最后一部分是`pusher.connect`,它完全按照它所说的去做。

要听这些变化，将调用添加到`viewDidLoad`函数的底部:

```
 override func viewDidLoad() {
        // other stuff...
        listenToChangesFromPusher()
    } 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！我们创建了一个实时表，当数据被操作时，它会对收到的更改做出响应。最后一部分是创建用于保存数据和触发推送事件的后端。

## 为我们的实时 iOS 表创建后端

首先，为 web 应用程序创建一个目录，然后在目录中创建一些新文件:

首先，创建一个名为 **package.json:**
的文件

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

这个文件将包含我们打算用来构建后端应用程序的所有包。

下一个要创建的文件将是 **config.js:**

```
 module.exports = {
        appId: 'PUSHER_APP_ID',
        key: 'PUSHER_APP_KEY',
        secret: 'PUSHER_APP_SECRET',
        cluster: 'PUSHER_APP_CLUSTER',
    }; 
```

Enter fullscreen mode Exit fullscreen mode

这将是所有配置值的位置。使用 Pusher 应用程序仪表板中的数据填写数值。

接下来，在 web 应用程序目录的根目录下创建一个空的`database.sqlite`文件。

接下来，在 web 应用程序目录中创建一个名为`migrations`的目录，在其中创建下一个文件 **001-initial-schema.sql** ，并粘贴下面的内容:

```
 -- Up
    CREATE TABLE Users (
        id INTEGER NOT NULL,
        name TEXT,
        position INTEGER NOT NULL,
        updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        PRIMARY KEY (id)
    );
    INSERT INTO Users (id, name, position) VALUES (1, 'John Doe', 1);
    -- Down
    DROP TABLE Users; 
```

Enter fullscreen mode Exit fullscreen mode

在上面，我们声明了应用程序启动时要运行的迁移。

> 💡**`**-- Up**`**标记应该运行的迁移，** `**-- Down**` **是迁移的回滚，如果您想后退并撤消迁移。****

 **接下来我们将创建主文件 **index.js:**

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
    let pusher     = new Pusher(require('./config.js'));
    // ------------------------------------------------------
    // Set up Express
    // ------------------------------------------------------
    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: false }));
    // ------------------------------------------------------
    // Define routes and logic
    // ------------------------------------------------------
    app.get('/users', (req, res, next) => {
      try {
        // Fetch all users from the database
        db.all('SELECT * FROM Users ORDER BY position ASC, updated_at DESC')
          .then(result => res.json(result))
      } catch (err) {
        next(err)
      }
    })
    app.post("/add", (req, res, next) => {
      try {
        let payload = {name:req.body.name, deviceId: req.body.deviceId}
        // Add the user to the database
        db.run("INSERT INTO Users (name, position) VALUES (?, (SELECT MAX(id) + 1 FROM Users))", payload.name).then(query => {
          payload.id = query.stmt.lastID
          pusher.trigger('userslist', 'addUser', payload)
          return res.json(payload)
        })
      } catch (err) {
        next(err)
      }
    })
    app.post("/delete", (req, res, next) => {
      try {
        let payload = {id:parseInt(req.body.id), index:parseInt(req.body.index), deviceId: req.body.deviceId}
        // Delete the user from the database
        db.run(`DELETE FROM Users WHERE id=${payload.id}`).then(query => {
          pusher.trigger('userslist', 'removeUser', payload)
          return res.json(payload)
        })
      } catch (err) {
        next(err)
      }
    })
    app.post("/move", (req, res, next) => {
      try {
        let payload = {
          deviceId: req.body.deviceId,
          src: parseInt(req.body.src),
          dest: parseInt(req.body.dest),
          src_id: parseInt(req.body.src_id),
          dest_id: parseInt(req.body.dest_id),
        }
        // Update the position of the user
        db.run(`UPDATE Users SET position=${payload.dest + 1}, updated_at=CURRENT_TIMESTAMP WHERE id=${payload.src_id}`).then(query => {
          pusher.trigger('userslist', 'moveUser', payload)
          res.json(payload)
        })
      } catch (err) {
        next(err)
      }
    })
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

在上面的代码中，我们加载了所有需要的包，包括 Express 和 Pusher。在实例化它们之后，我们创建我们需要的路由。

这些路由被设计用来做一些非常基本的事情，比如向数据库中添加一行、从数据库中删除一行以及更新数据库中的行。对于数据库，我们使用的是 [SQLite NPM 包](https://www.npmjs.com/package/sqlite)。

在最后一个块中，我们使用`/migrations/001-initial-schema.sql`文件将数据库迁移到`database.sqlite`文件中。然后我们在一切完成后启动 express 应用程序。

打开终端，`cd`到 web 应用目录的根目录，运行下面的命令分别安装 NPM 依赖项和运行应用:

```
 $ npm install
    $ node index.js 
```

Enter fullscreen mode Exit fullscreen mode

当安装完成，应用程序准备就绪时，您应该会看到消息 **App 侦听端口 4000！**

## 测试应用程序

一旦您的本地节点 web 服务器开始运行，您将需要进行一些更改，以便您的应用程序可以与本地 web 服务器通信。在`info.plist`文件中，进行以下更改:

[![](img/ff8941a2c355ba94a7b549bd259b34fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D8dzqj3V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/11/How-to-build-a-realtime-table-using-Swift-5.png)

通过这一更改，您可以构建并运行您的应用程序，它将直接与您的本地 web 应用程序对话。

## 结论

本文演示了如何在 iOS 中创建表格，以实时响应其他设备上的更改。这非常有用，可以应用于需要在所有设备上动态即时更新的数据。

如果你有任何问题，反馈或更正，你可以在下面的评论区发表。

以上教程的源代码可以在 [GitHub](https://github.com/neoighodaro/Build-a-realtime-table-using-Swift) 获得。

## 这个帖子最早出现在[推手博客](https://blog.pusher.com/how-to-build-a-realtime-table-using-swift/)上。****