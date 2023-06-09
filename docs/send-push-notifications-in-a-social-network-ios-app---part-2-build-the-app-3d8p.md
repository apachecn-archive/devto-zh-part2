# 在社交网络 iOS 应用中发送推送通知——第 2 部分:构建应用

> 原文：<https://dev.to/neo/send-push-notifications-in-a-social-network-ios-app---part-2-build-the-app-3d8p>

> 要学习本教程，你需要一台安装了 Xcode 的 Mac，Xcode 和 Swift 的知识，PHP 的基础知识(包括 Laravel 框架)，一个 Pusher 帐户，以及安装在你机器上的 CocoaPods。

在[的前一部分](https://dev.to/neo/send-push-notifications-in-a-social-network-ios-app---part-1-build-the-backend-ld5)中，我们能够设置我们的 Pusher Beams 应用程序，并使用 Laravel 创建我们的 API 后端。我们还使用[推送光束](https://github.com/neoighodaro/pusher-beams)包为后端添加了推送通知支持。

在这一部分，我们将从我们停下的地方继续。我们将使用 Swift 创建 iOS 应用程序，然后将推送通知集成到该应用程序中，这样我们就可以在发送通知时收到通知。

[![](img/5c4aa46878dfc816c27776a70dc3264f.png)T2】](//images.ctfassets.net/1es3ne0caaid/51GgQzYiqQEMAQWOK8AMgS/fd0d5bb1bbaa7f1f198fed340d25cd53/ios-push-notifications-social-network-demo.gif)

## 先决条件

为了完成本教程，您需要具备以下条件:

*   我已完成文章的第一部分。

## 使用 Swift 构建我们的 iOS 应用

### 创建我们的控制器

在 Xcode 中，创建一个新的类`LaunchViewController`并将下面文件的内容粘贴到其中:

```
 <span class="hljs-keyword">import</span> UIKit

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">LaunchViewController</span>: <span class="hljs-title">UIViewController</span> </span>{
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> loginButton: <span class="hljs-type">UIButton</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> signupButton: <span class="hljs-type">UIButton</span>!

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">viewDidLoad</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">super</span>.viewDidLoad()

            loginButton.isHidden = <span class="hljs-literal">true</span>
            signupButton.isHidden = <span class="hljs-literal">true</span>

            loginButton.addTarget(<span class="hljs-keyword">self</span>, action: #selector(loginButtonWasPressed), <span class="hljs-keyword">for</span>: .touchUpInside)
            signupButton.addTarget(<span class="hljs-keyword">self</span>, action: #selector(signupButtonWasPressed), <span class="hljs-keyword">for</span>: .touchUpInside)
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">viewDidAppear</span><span class="hljs-params">(<span class="hljs-number">_</span> animated: Bool)</span></span> {
            <span class="hljs-keyword">super</span>.viewDidAppear(animated)

            <span class="hljs-keyword">guard</span> <span class="hljs-type">AuthService</span>.shared.loggedIn() == <span class="hljs-literal">false</span> <span class="hljs-keyword">else</span> {
                <span class="hljs-type">SettingsService</span>.shared.loadFromApi()
                <span class="hljs-keyword">return</span> performSegue(withIdentifier: <span class="hljs-string">"Main"</span>, sender: <span class="hljs-keyword">self</span>)
            }

            loginButton.isHidden = <span class="hljs-literal">false</span>
            signupButton.isHidden = <span class="hljs-literal">false</span>
        }

        <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">loginButtonWasPressed</span><span class="hljs-params">()</span></span> {
            performSegue(withIdentifier: <span class="hljs-string">"Login"</span>, sender: <span class="hljs-keyword">self</span>)
        }

        <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">signupButtonWasPressed</span><span class="hljs-params">()</span></span> {
            performSegue(withIdentifier: <span class="hljs-string">"Signup"</span>, sender: <span class="hljs-keyword">self</span>)
        }   
    } 
```

Enter fullscreen mode Exit fullscreen mode

将控制器设置为相关故事板场景的自定义类。

上面我们有两个`@IBOutlet`按钮用于登录和注册。在`viewDidLoad`方法中，我们隐藏按钮，并在它们被按下时为它们创建一个目标回调。在`viewDidAppear`方法中，我们检查用户是否登录，如果登录，则显示时间线。如果用户没有登录，我们取消隐藏身份验证按钮。

我们还有`loginButtonWasPressed`和`signupButtonWasPressed`方法。这些方法提供了登录和注册控制器。

接下来，创建一个`SignupViewController`类，并将下面的代码粘贴到文件中:

```
 <span class="hljs-keyword">import</span> UIKit
    <span class="hljs-keyword">import</span> NotificationBannerSwift

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SignupViewController</span>: <span class="hljs-title">UIViewController</span> </span>{
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> nameTextField: <span class="hljs-type">UITextField</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> emailTextField: <span class="hljs-type">UITextField</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> passwordTextfield: <span class="hljs-type">UITextField</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> signupButton: <span class="hljs-type">UIBarButtonItem</span>!

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">viewDidLoad</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">super</span>.viewDidLoad()

            activateSignupButtonIfNecessary()

            nameTextField.addTarget(<span class="hljs-keyword">self</span>, action: #selector(textFieldChanged(<span class="hljs-number">_</span>:)), <span class="hljs-keyword">for</span>: .editingChanged)
            emailTextField.addTarget(<span class="hljs-keyword">self</span>, action: #selector(textFieldChanged(<span class="hljs-number">_</span>:)), <span class="hljs-keyword">for</span>: .editingChanged)
            passwordTextfield.addTarget(<span class="hljs-keyword">self</span>, action: #selector(textFieldChanged(<span class="hljs-number">_</span>:)), <span class="hljs-keyword">for</span>: .editingChanged)
        }

        <span class="hljs-meta">@IBAction</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">closeButtonWasPressed</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: <span class="hljs-keyword">Any</span>? = <span class="hljs-literal">nil</span>)</span></span> {
            dismiss(animated: <span class="hljs-literal">true</span>, completion: <span class="hljs-literal">nil</span>)
        }

        <span class="hljs-meta">@IBAction</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">signupButtonWasPressed</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: <span class="hljs-keyword">Any</span>)</span></span> {
            <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> credentials = textFields(), signupButton.isEnabled <span class="hljs-keyword">else</span> {
                <span class="hljs-keyword">return</span>
            }

            <span class="hljs-type">ApiService</span>.shared.signup(credentials: credentials) { token, error <span class="hljs-keyword">in</span>
                <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> token = token, error == <span class="hljs-literal">nil</span> <span class="hljs-keyword">else</span> {
                    <span class="hljs-keyword">return</span> <span class="hljs-type">StatusBarNotificationBanner</span>(title: <span class="hljs-string">"Signup failed. Try again."</span>, style: .danger).show()
                }

                <span class="hljs-type">AuthService</span>.shared.saveToken(token).then {
                    <span class="hljs-keyword">self</span>.closeButtonWasPressed()
                }
            }
        }

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">textFields</span><span class="hljs-params">()</span></span> -> <span class="hljs-type">AuthService</span>.<span class="hljs-type">SignupCredentials</span>? {
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> name = nameTextField.text, <span class="hljs-keyword">let</span> email = emailTextField.text, <span class="hljs-keyword">let</span> pass = passwordTextfield.text {
                <span class="hljs-keyword">return</span> (name, email, pass)
            }

            <span class="hljs-keyword">return</span> <span class="hljs-literal">nil</span>
        }

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">activateSignupButtonIfNecessary</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> field = textFields() {
                signupButton.isEnabled = !field.name.isEmpty && !field.email.isEmpty && !field.password.isEmpty
            }
        }

        <span class="hljs-meta">@objc</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">textFieldChanged</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: UITextField)</span></span> {
            activateSignupButtonIfNecessary()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

将控制器设置为注册故事板场景的自定义类。

上面我们有三个`@IBOutlet`用于注册文本字段，一个`@IBOutlet`用于注册按钮。在`viewDidLoad`方法中，我们为文本字段添加了一个回调函数，当文本发生变化时，这个回调函数将被触发。我们还调用了`activateSignupButtonIfNecessary`方法，如果所有字段的内容都有效，该方法将激活注册按钮。

我们有两个`@IBAction`函数。第一个用于关闭按钮被按下时，另一个用于注册按钮被按下时。当按下**注册**按钮时，调用`signupButtonWasPressed`方法，该方法使用`ApiService`为用户创建一个帐户并让用户登录。如果注册失败，我们使用 [NotificationBanner](https://github.com/Daltron/NotificationBanner) 包来显示错误。

我们还有其他的辅助方法。`textFields`方法返回文本字段内容的元组，而`textFieldChanged`方法在每次文本字段的内容被修改时被触发。

接下来，创建一个`LoginViewController`类，并将下面的代码粘贴到文件中:

```
 <span class="hljs-keyword">import</span> UIKit
    <span class="hljs-keyword">import</span> NotificationBannerSwift

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">LoginViewController</span>: <span class="hljs-title">UIViewController</span> </span>{
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> emailTextField: <span class="hljs-type">UITextField</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> passwordTextField: <span class="hljs-type">UITextField</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> loginButton: <span class="hljs-type">UIBarButtonItem</span>!

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">viewDidLoad</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">super</span>.viewDidLoad()

            activateLoginButtonIfNecessary()

            emailTextField.addTarget(<span class="hljs-keyword">self</span>, action: #selector(textFieldChanged(<span class="hljs-number">_</span>:)), <span class="hljs-keyword">for</span>: .editingChanged)
            passwordTextField.addTarget(<span class="hljs-keyword">self</span>, action: #selector(textFieldChanged(<span class="hljs-number">_</span>:)), <span class="hljs-keyword">for</span>: .editingChanged)
        }

        <span class="hljs-meta">@IBAction</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">closeButtonWasPressed</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: <span class="hljs-keyword">Any</span>? = <span class="hljs-literal">nil</span>)</span></span> {
            dismiss(animated: <span class="hljs-literal">true</span>, completion: <span class="hljs-literal">nil</span>)
        }

        <span class="hljs-meta">@IBAction</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">loginButtonWasPressed</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: <span class="hljs-keyword">Any</span>)</span></span> {
            <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> credentials = textFields(), loginButton.isEnabled <span class="hljs-keyword">else</span> {
                <span class="hljs-keyword">return</span>
            }

            <span class="hljs-type">ApiService</span>.shared.login(credentials: credentials) { token, error <span class="hljs-keyword">in</span>
                <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> token = token, error == <span class="hljs-literal">nil</span> <span class="hljs-keyword">else</span> {
                    <span class="hljs-keyword">return</span> <span class="hljs-type">StatusBarNotificationBanner</span>(title: <span class="hljs-string">"Login failed, try again."</span>, style: .danger).show()
                }

                <span class="hljs-type">AuthService</span>.shared.saveToken(token).then {
                    <span class="hljs-keyword">self</span>.closeButtonWasPressed()
                }
            }
        }

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">textFields</span><span class="hljs-params">()</span></span> -> <span class="hljs-type">AuthService</span>.<span class="hljs-type">LoginCredentials</span>? {
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> email = emailTextField.text, <span class="hljs-keyword">let</span> password = passwordTextField.text {
                <span class="hljs-keyword">return</span> (email, password)
            }

            <span class="hljs-keyword">return</span> <span class="hljs-literal">nil</span>
        }

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">activateLoginButtonIfNecessary</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> field = textFields() {
                loginButton.isEnabled = !field.email.isEmpty && !field.password.isEmpty
            }
        }

        <span class="hljs-meta">@objc</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">textFieldChanged</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: UITextField)</span></span> {
            activateLoginButtonIfNecessary()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

将控制器设置为登录故事板场景的自定义类。

上述控制器的功能与`SignupViewController`非常相似。当调用`loginButtonWasPressed`方法时，它使用`ApiService`让用户登录并保存令牌。

接下来，我们需要创建设置控制器。这将是管理设置的地方。创建一个`SettingsTableViewController`并将下面的代码粘贴到文件中:

```
 <span class="hljs-keyword">import</span> UIKit

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SettingsTableViewController</span>: <span class="hljs-title">UITableViewController</span> </span>{
        <span class="hljs-keyword">let</span> settings = {
            <span class="hljs-keyword">return</span> <span class="hljs-type">SettingsService</span>.shared.settings
        }()

        <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">shouldCheckCell</span><span class="hljs-params">(at index: IndexPath, with setting: String)</span></span> -> <span class="hljs-type">Bool</span> {
            <span class="hljs-keyword">let</span> status = <span class="hljs-type">Setting</span>.<span class="hljs-type">Notification</span>.<span class="hljs-type">Comments</span>(rawValue: setting)

            <span class="hljs-keyword">return</span> (status == .off && index.row == <span class="hljs-number">0</span>) ||
                   (status == .following && index.row == <span class="hljs-number">1</span>) ||
                   (status == .everyone && index.row == <span class="hljs-number">2</span>)
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">tableView</span><span class="hljs-params">(<span class="hljs-number">_</span> tableView: UITableView, cellForRowAt indexPath: IndexPath)</span></span> -> <span class="hljs-type">UITableViewCell</span> {
            <span class="hljs-keyword">let</span> cell = <span class="hljs-keyword">super</span>.tableView(tableView, cellForRowAt: indexPath)
            cell.accessoryType = .<span class="hljs-keyword">none</span>

            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> setting = settings[<span class="hljs-string">"notification_comments"</span>], shouldCheckCell(at: indexPath, with: setting) {
                cell.accessoryType = .checkmark
            }

            <span class="hljs-keyword">return</span> cell
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">tableView</span><span class="hljs-params">(<span class="hljs-number">_</span> tableView: UITableView, didSelectRowAt indexPath: IndexPath)</span></span> {
            <span class="hljs-keyword">let</span> rowsCount = <span class="hljs-keyword">self</span>.tableView.numberOfRows(inSection: indexPath.section)

            <span class="hljs-keyword">for</span> i <span class="hljs-keyword">in</span> <span class="hljs-number">0</span>..<rowsCount  {
                <span class="hljs-keyword">let</span>  rowIndexPath = <span class="hljs-type">IndexPath</span>(row: i, section: indexPath.section)

                <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> cell = <span class="hljs-keyword">self</span>.tableView.cellForRow(at: rowIndexPath) {
                    cell.accessoryType = indexPath.row == i ? .checkmark : .<span class="hljs-keyword">none</span>
                }
            }

            <span class="hljs-keyword">let</span> setting = indexPath.row == <span class="hljs-number">0</span> ? <span class="hljs-string">"Off"</span> : (indexPath.row == <span class="hljs-number">1</span> ? <span class="hljs-string">"Following"</span> : <span class="hljs-string">"Everyone"</span>)

            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> status = <span class="hljs-type">Setting</span>.<span class="hljs-type">Notification</span>.<span class="hljs-type">Comments</span>(rawValue: setting) {
                <span class="hljs-type">SettingsService</span>.shared.updateCommentsNotificationSetting(status)
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

将控制器设置为设置故事板场景的自定义类。

在`SettingsTableViewController`中，我们从`SettingsService`类中加载设置，我们将在后面创建这个类。然后我们定义一个`shouldCheckCell`方法，它将通过检查用户设置来决定是否应该检查单元格行。

[![](img/3c9cac9770fd7d3d6411575847abce11.png)T2】](//images.ctfassets.net/1es3ne0caaid/4TH2I2tOQ82eAIcueuEoqC/4597c23c2705aaf92044a0dc69a08c70/ios-push-notifications-social-network-settings.png)

从故事板场景中可以看到，评论通知部分有三种可能的设置:关闭的**、关注**的**和所有人**的**。当设置改变时，设置控制器尝试使用`SettingsService`本地和远程更新设置。**

接下来，创建`SearchTableViewController`并将下面的代码粘贴到其中:

```
 <span class="hljs-keyword">import</span> UIKit
    <span class="hljs-keyword">import</span> NotificationBannerSwift

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SearchTableViewController</span>: <span class="hljs-title">UITableViewController</span> </span>{

        <span class="hljs-keyword">var</span> users: <span class="hljs-type">Users</span> = []

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">viewDidLoad</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">super</span>.viewDidLoad()

            <span class="hljs-type">ApiService</span>.shared.fetchUsers { users <span class="hljs-keyword">in</span>
                <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> users = users <span class="hljs-keyword">else</span> {
                    <span class="hljs-keyword">return</span> <span class="hljs-type">StatusBarNotificationBanner</span>(title: <span class="hljs-string">"Unable to fetch users."</span>, style: .danger).show()
                }

                <span class="hljs-keyword">self</span>.users = users
                <span class="hljs-keyword">self</span>.tableView.reloadData()
            }
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">tableView</span><span class="hljs-params">(<span class="hljs-number">_</span> tableView: UITableView, numberOfRowsInSection section: Int)</span></span> -> <span class="hljs-type">Int</span> {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">self</span>.users.<span class="hljs-built_in">count</span>
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">tableView</span><span class="hljs-params">(<span class="hljs-number">_</span> tableView: UITableView, cellForRowAt indexPath: IndexPath)</span></span> -> <span class="hljs-type">UITableViewCell</span> {
            <span class="hljs-keyword">let</span> user = <span class="hljs-keyword">self</span>.users[indexPath.row]
            <span class="hljs-keyword">let</span> cell = tableView.dequeueReusableCell(withIdentifier: <span class="hljs-string">"User"</span>, <span class="hljs-keyword">for</span>: indexPath) <span class="hljs-keyword">as</span>! <span class="hljs-type">UserListTableViewCell</span>

            cell.delegate = <span class="hljs-keyword">self</span>
            cell.indexPath = indexPath
            cell.textLabel?.text = user.name

            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> following = user.follows {
                cell.setFollowStatus(following)
            }

            <span class="hljs-keyword">return</span> cell
        }

    }

    <span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">SearchTableViewController</span>: <span class="hljs-title">UserListCellFollowButtonDelegate</span> </span>{

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">followButtonTapped</span><span class="hljs-params">(at indexPath: IndexPath)</span></span> {
            <span class="hljs-keyword">let</span> user = <span class="hljs-keyword">self</span>.users[indexPath.row]
            <span class="hljs-keyword">let</span> userFollows = user.follows ?? <span class="hljs-literal">false</span>

            <span class="hljs-type">ApiService</span>.shared.toggleFollowStatus(forUserId: user.id, following: userFollows) { successful <span class="hljs-keyword">in</span>
                <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> successful = successful, successful <span class="hljs-keyword">else</span> { <span class="hljs-keyword">return</span> }

                <span class="hljs-keyword">self</span>.users[indexPath.row].follows = !userFollows
                <span class="hljs-keyword">self</span>.tableView.reloadData()
            }
        }

    } 
```

Enter fullscreen mode Exit fullscreen mode

将控制器设置为搜索故事板场景的自定义类。

尽管我们已经将这个类命名为`SearchTableViewController`,但实际上我们不会进行任何搜索。我们将有一个虚构的搜索结果，它将显示服务上的用户列表，并带有一个**跟随/取消跟随**按钮，以便于跟随或取消跟随用户。

在`viewDidLoad`方法中，我们调用了`ApiService`类上的`fetchUsers`方法，然后我们将用户加载到`users`属性，该属性然后被用作表的数据。在类扩展中，我们实现了`UserListCellFollowButtonDelegate`协议，这使得我们很容易知道什么时候点击了**跟随/取消跟随**按钮。我们使用[委托模式](https://www.appcoda.com/swift-delegate/)来实现这一点。

接下来，创建`TimelineTableViewController`类并将下面的代码粘贴到其中:

```
 <span class="hljs-keyword">import</span> UIKit
    <span class="hljs-keyword">import</span> Alamofire
    <span class="hljs-keyword">import</span> NotificationBannerSwift
    <span class="hljs-keyword">import</span> PushNotifications

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">TimelineTableViewController</span>: <span class="hljs-title">UITableViewController</span> </span>{
        <span class="hljs-keyword">var</span> photos: <span class="hljs-type">Photos</span> = []
        <span class="hljs-keyword">var</span> selectedPhoto: <span class="hljs-type">Photo</span>?
        <span class="hljs-keyword">let</span> picker = <span class="hljs-type">UIImagePickerController</span>()

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">viewDidLoad</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">super</span>.viewDidLoad()
            <span class="hljs-keyword">self</span>.reloadButtonWasPressed()
            <span class="hljs-keyword">self</span>.picker.delegate = <span class="hljs-keyword">self</span>
        }

        <span class="hljs-meta">@IBAction</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">userButtonWasPressed</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: <span class="hljs-keyword">Any</span>)</span></span> {
            <span class="hljs-type">AuthService</span>.shared.logout()
            dismiss(animated: <span class="hljs-literal">true</span>, completion: <span class="hljs-literal">nil</span>)
        }

        <span class="hljs-meta">@IBAction</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">reloadButtonWasPressed</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: <span class="hljs-keyword">Any</span>? = <span class="hljs-literal">nil</span>)</span></span> {
            <span class="hljs-type">ApiService</span>.shared.fetchPosts { photos <span class="hljs-keyword">in</span>
                <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> photos = photos {
                    <span class="hljs-keyword">self</span>.photos = photos
                    <span class="hljs-keyword">self</span>.tableView.reloadData()
                }
            }
        }

        <span class="hljs-meta">@IBAction</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">addButtonWasPressed</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: <span class="hljs-keyword">Any</span>)</span></span> {
            picker.sourceType = .photoLibrary
            picker.mediaTypes = <span class="hljs-type">UIImagePickerController</span>.availableMediaTypes(<span class="hljs-keyword">for</span>: .photoLibrary)!
            picker.modalPresentationStyle = .popover
            picker.popoverPresentationController?.barButtonItem = <span class="hljs-literal">nil</span>
            present(picker, animated: <span class="hljs-literal">true</span>, completion: <span class="hljs-literal">nil</span>)
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">prepare</span><span class="hljs-params">(<span class="hljs-keyword">for</span> segue: UIStoryboardSegue, sender: <span class="hljs-keyword">Any</span>?)</span></span> {
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> vc = segue.destination <span class="hljs-keyword">as</span>? <span class="hljs-type">CommentsTableViewController</span>, <span class="hljs-keyword">let</span> photo = selectedPhoto {
                selectedPhoto = <span class="hljs-literal">nil</span>
                vc.photoId = photo.id
                vc.comments = photo.comments
            }
        } 
    } 
```

Enter fullscreen mode Exit fullscreen mode

将控制器设置为时间线故事板场景的自定义类。

在上面的控制器中，我们有`photos`属性，它是服务上所有照片的数组；`selectedPhoto`，它将临时保存所选的照片对象；以及`picker`属性，当我们试图将图像上传到服务时，它将用于图像拾取器。

在`viewDidLoad`方法中，我们通过调用`reloadButtonWasPressed`方法来加载文章，然后我们将类设置为`picker.delegate`。我们有`@IBAction`方法`addButtonWasPressed`，它启动 iOS 映像拾取器。

当控制器导航到注释控制器时，会自动调用`prepare`方法。因此，在这里，我们将注释设置为注释控制器，这样我们就可以立即显示一些内容。我们还将`photoId`设置为注释控制器。

接下来，在同一个类中，在底部粘贴以下内容:

```
 <span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">TimelineTableViewController</span> </span>{

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">tableView</span><span class="hljs-params">(<span class="hljs-number">_</span> tableView: UITableView, numberOfRowsInSection section: Int)</span></span> -> <span class="hljs-type">Int</span> {
            <span class="hljs-keyword">return</span> photos.<span class="hljs-built_in">count</span>
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">tableView</span><span class="hljs-params">(<span class="hljs-number">_</span> tableView: UITableView, cellForRowAt indexPath: IndexPath)</span></span> -> <span class="hljs-type">UITableViewCell</span> {
            <span class="hljs-keyword">let</span> photo = photos[indexPath.row]
            <span class="hljs-keyword">let</span> cell = tableView.dequeueReusableCell(withIdentifier: <span class="hljs-string">"PhotoCell"</span>, <span class="hljs-keyword">for</span>: indexPath) <span class="hljs-keyword">as</span>! <span class="hljs-type">PhotoListTableViewCell</span>

            cell.delegate = <span class="hljs-keyword">self</span>
            cell.indexPath = indexPath
            cell.nameLabel.text = photo.user.name
            cell.photo.image = <span class="hljs-type">UIImage</span>(named: <span class="hljs-string">"loading"</span>)

            <span class="hljs-type">Alamofire</span>.request(photo.image).responseData { response <span class="hljs-keyword">in</span>
                <span class="hljs-keyword">if</span> response.error == <span class="hljs-literal">nil</span>, <span class="hljs-keyword">let</span> data = response.data {
                    cell.photo.image = <span class="hljs-type">UIImage</span>(data: data)
                }
            }

            <span class="hljs-keyword">return</span> cell
        } 

    }

    <span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">TimelineTableViewController</span>: <span class="hljs-title">PhotoListCellDelegate</span> </span>{

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">commentButtonWasTapped</span><span class="hljs-params">(at indexPath: IndexPath)</span></span> {
            <span class="hljs-keyword">self</span>.selectedPhoto = photos[indexPath.row]
            <span class="hljs-keyword">self</span>.performSegue(withIdentifier: <span class="hljs-string">"Comments"</span>, sender: <span class="hljs-keyword">self</span>)
        }

    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们有两个对`TimelineTableViewController`的扩展。第一个扩展定义了我们希望如何在表格视图中显示照片。第二个扩展是`PhotoListCellDelegate`的实现，这是委托模式的另一个实现。此处定义的方法`commentButtonWasTapped`将在照片单元格上按下注释按钮时触发。

在同一个文件中，在文件底部添加最后一个类扩展名:

```
 <span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">TimelineTableViewController</span>: <span class="hljs-title">UIImagePickerControllerDelegate</span>, <span class="hljs-title">UINavigationControllerDelegate</span> </span>{

        <span class="hljs-meta">@objc</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">imagePickerController</span><span class="hljs-params">(<span class="hljs-number">_</span> picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : <span class="hljs-keyword">Any</span>])</span></span> {
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> selected = info[<span class="hljs-string">"UIImagePickerControllerOriginalImage"</span>] <span class="hljs-keyword">as</span>? <span class="hljs-type">UIImage</span> {
                <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> image = <span class="hljs-type">UIImageJPEGRepresentation</span>(selected, <span class="hljs-number">0</span>) <span class="hljs-keyword">else</span> { 
                    <span class="hljs-keyword">return</span> 
                }

                <span class="hljs-keyword">let</span> uploadPhotoHandler: (() -> <span class="hljs-type">Void</span>)? = {
                    <span class="hljs-keyword">var</span> caption: <span class="hljs-type">UITextField</span>?

                    <span class="hljs-keyword">let</span> alert = <span class="hljs-type">UIAlertController</span>(title: <span class="hljs-string">"Add Caption"</span>, message: <span class="hljs-literal">nil</span>, preferredStyle: .alert)
                    alert.addTextField(configurationHandler: { textfield <span class="hljs-keyword">in</span> caption = textfield })
                    alert.addAction(<span class="hljs-type">UIAlertAction</span>(title: <span class="hljs-string">"Cancel"</span>, style: .cancel, handler: <span class="hljs-literal">nil</span>))
                    alert.addAction(<span class="hljs-type">UIAlertAction</span>(title: <span class="hljs-string">"Save"</span>, style: .<span class="hljs-keyword">default</span>, handler: { action <span class="hljs-keyword">in</span>
                        <span class="hljs-keyword">var</span> filename = <span class="hljs-string">"upload.jpg"</span>
                        <span class="hljs-keyword">let</span> caption = caption?.text ?? <span class="hljs-string">"No caption"</span>

                        <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> url = info[<span class="hljs-type">UIImagePickerControllerImageURL</span>] <span class="hljs-keyword">as</span>? <span class="hljs-type">NSURL</span>, <span class="hljs-keyword">let</span> name = url.lastPathComponent {
                            filename = name
                        }

                        <span class="hljs-type">ApiService</span>.shared.uploadImage(image, caption: caption, name: filename) { photo, error <span class="hljs-keyword">in</span>
                            <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> photo = photo, error == <span class="hljs-literal">nil</span> <span class="hljs-keyword">else</span> {
                                <span class="hljs-keyword">return</span> <span class="hljs-type">StatusBarNotificationBanner</span>(title: <span class="hljs-string">"Failed to upload image"</span>, style: .danger).show()
                            }

                            <span class="hljs-keyword">try</span>? <span class="hljs-type">PushNotifications</span>.shared.subscribe(interest: <span class="hljs-string">"photo_\(photo.id)-comment_following"</span>)
                            <span class="hljs-keyword">try</span>? <span class="hljs-type">PushNotifications</span>.shared.subscribe(interest: <span class="hljs-string">"photo_\(photo.id)-comment_everyone"</span>)

                            <span class="hljs-keyword">self</span>.photos.insert(photo, at: <span class="hljs-number">0</span>)
                            <span class="hljs-keyword">self</span>.tableView.reloadData()

                            <span class="hljs-type">StatusBarNotificationBanner</span>(title: <span class="hljs-string">"Uploaded successfully"</span>, style: .success).show()
                        }
                    }))

                    <span class="hljs-keyword">self</span>.present(alert, animated: <span class="hljs-literal">true</span>, completion: <span class="hljs-literal">nil</span>)
                }

                <span class="hljs-keyword">self</span>.dismiss(animated: <span class="hljs-literal">true</span>, completion: uploadPhotoHandler)
            }
        }

    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的扩展中，我们实现了`UIImagePickerControllerDelegate`，让我们处理来自`UIImagePickerController`的图像选择。当一个图像被选中时，上面的方法将被调用。

我们通过获取选定的图像来处理它，显示一个带有文本字段的警告控制器，这样我们可以获取图像的标题，然后我们使用`ApiService`将图像和标题发送给 API。

上传完成后，我们将新添加的照片添加到表格中，然后我们为用户订阅 Pusher Beam 兴趣，这样他们就可以在对照片进行评论时收到推送通知。

此外，我们还订阅了两个兴趣。第一个是`photo_\(id)-comment_following`，第二个是`photo_\(id)-comment_everyone`。我们这样做是为了根据用户设置对通知进行分段。在服务器上，当添加评论时，如果照片所有者将评论通知设置为 following，那么推送通知将被发布给`photo_\(id)-comment_following`兴趣。

接下来，创建`CommentsTableViewController`类并将下面的代码粘贴到其中:

```
 <span class="hljs-keyword">import</span> UIKit
    <span class="hljs-keyword">import</span> NotificationBannerSwift

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CommentsTableViewController</span>: <span class="hljs-title">UITableViewController</span> </span>{
        <span class="hljs-keyword">var</span> photoId: <span class="hljs-type">Int</span> = <span class="hljs-number">0</span>
        <span class="hljs-keyword">var</span> commentField: <span class="hljs-type">UITextField</span>?
        <span class="hljs-keyword">var</span> comments: <span class="hljs-type">PhotoComments</span> = []

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">viewDidLoad</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">super</span>.viewDidLoad()

            navigationItem.title = <span class="hljs-string">"Comments"</span>
            navigationController?.navigationBar.prefersLargeTitles = <span class="hljs-literal">false</span>
            navigationItem.rightBarButtonItem = <span class="hljs-type">UIBarButtonItem</span>(title: <span class="hljs-string">"Add"</span>, style: .plain, target: <span class="hljs-keyword">self</span>, action: #selector(addCommentButtonWasTapped))

            <span class="hljs-keyword">if</span> photoId != <span class="hljs-number">0</span> {
                <span class="hljs-type">ApiService</span>.shared.fetchComments(forPhoto: photoId) { comments <span class="hljs-keyword">in</span>
                    <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> comments = comments <span class="hljs-keyword">else</span> { <span class="hljs-keyword">return</span> }

                    <span class="hljs-keyword">self</span>.comments = comments
                    <span class="hljs-keyword">self</span>.tableView.reloadData()
                }
            }
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">tableView</span><span class="hljs-params">(<span class="hljs-number">_</span> tableView: UITableView, numberOfRowsInSection section: Int)</span></span> -> <span class="hljs-type">Int</span> {
            <span class="hljs-keyword">return</span> comments.<span class="hljs-built_in">count</span>
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">tableView</span><span class="hljs-params">(<span class="hljs-number">_</span> tableView: UITableView, cellForRowAt indexPath: IndexPath)</span></span> -> <span class="hljs-type">UITableViewCell</span> {
            <span class="hljs-keyword">let</span> cell = tableView.dequeueReusableCell(withIdentifier: <span class="hljs-string">"Comment"</span>, <span class="hljs-keyword">for</span>: indexPath) <span class="hljs-keyword">as</span>! <span class="hljs-type">CommentsListTableViewCell</span>
            <span class="hljs-keyword">let</span> comment = comments[indexPath.row]

            cell.username?.text = comment.user.name
            cell.comment?.text = comment.comment

            <span class="hljs-keyword">return</span> cell
        }

        <span class="hljs-meta">@objc</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">addCommentButtonWasTapped</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">let</span> alertCtrl = <span class="hljs-type">UIAlertController</span>(title: <span class="hljs-string">"Add Comment"</span>, message: <span class="hljs-literal">nil</span>, preferredStyle: .alert)
            alertCtrl.addAction(<span class="hljs-type">UIAlertAction</span>(title: <span class="hljs-string">"Cancel"</span>, style: .cancel, handler: <span class="hljs-literal">nil</span>))
            alertCtrl.addTextField { textField <span class="hljs-keyword">in</span> <span class="hljs-keyword">self</span>.commentField = textField }
            alertCtrl.addAction(<span class="hljs-type">UIAlertAction</span>(title: <span class="hljs-string">"Add Comment"</span>, style: .<span class="hljs-keyword">default</span>) { <span class="hljs-number">_</span> <span class="hljs-keyword">in</span>
                <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> comment = <span class="hljs-keyword">self</span>.commentField?.text <span class="hljs-keyword">else</span> { <span class="hljs-keyword">return</span> }

                <span class="hljs-type">ApiService</span>.shared.leaveComment(forId: <span class="hljs-keyword">self</span>.photoId, comment: comment) { newComment <span class="hljs-keyword">in</span>
                    <span class="hljs-keyword">guard</span> <span class="hljs-keyword">let</span> comment = newComment <span class="hljs-keyword">else</span> {
                        <span class="hljs-keyword">return</span> <span class="hljs-type">StatusBarNotificationBanner</span>(title: <span class="hljs-string">"Failed to post comment"</span>, style: .danger).show()
                    }

                    <span class="hljs-keyword">self</span>.comments.insert(comment, at: <span class="hljs-number">0</span>)
                    <span class="hljs-keyword">self</span>.tableView.reloadData()
                }
            })

            <span class="hljs-keyword">self</span>.present(alertCtrl, animated: <span class="hljs-literal">true</span>, completion: <span class="hljs-literal">nil</span>)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

将控制器设置为时间线故事板场景的自定义类。

在上面的`CommentsTableViewController`中，我们有保存照片所有评论的`comments`属性、`photoId`属性和`commentField`属性，前者保存评论被加载的照片的 ID，后者是保存新评论的文本字段。

在`viewDidLoad`方法中，我们设置了控制器标题，并在导航栏右侧添加了一个“**添加**按钮。接下来，我们调用`ApiService`中的`fetchComments`方法来加载照片的评论。

我们在控制器中有`addCommentButtonWasTapped`方法，当导航条上的“**添加**按钮被按下时，该方法被激活。这将打开一个带有文本字段的警报控制器，我们可以在其中获取评论文本，然后使用`ApiService`将评论发送给 API。

### 创建我们的自定义视图类

既然我们已经创建了控制器，那么让我们创建一些自定义视图类，这些类是我们之前在控制器中使用的单元格所需要的。

我们将创建的第一个定制单元格是`PhotoListTableViewCell`类。创建该类并将以下代码粘贴到文件中:

```
 <span class="hljs-keyword">import</span> UIKit

    <span class="hljs-class"><span class="hljs-keyword">protocol</span> <span class="hljs-title">PhotoListCellDelegate</span> </span>{
        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">commentButtonWasTapped</span><span class="hljs-params">(at indexPath: IndexPath)</span></span>
    }

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">PhotoListTableViewCell</span>: <span class="hljs-title">UITableViewCell</span> </span>{
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> nameLabel: <span class="hljs-type">UILabel</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> photo: <span class="hljs-type">UIImageView</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> commentButton: <span class="hljs-type">UIButton</span>!

        <span class="hljs-keyword">var</span> indexPath: <span class="hljs-type">IndexPath</span>?    
        <span class="hljs-keyword">var</span> delegate: <span class="hljs-type">PhotoListCellDelegate</span>?

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">awakeFromNib</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">super</span>.awakeFromNib()
            <span class="hljs-keyword">self</span>.selectionStyle = .<span class="hljs-keyword">none</span>

            commentButton.addTarget(<span class="hljs-keyword">self</span>, action: #selector(commentButtonWasTapped), <span class="hljs-keyword">for</span>: .touchUpInside)
        }

        <span class="hljs-meta">@objc</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">commentButtonWasTapped</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> indexPath = indexPath, <span class="hljs-keyword">let</span> delegate = delegate {
                delegate.commentButtonWasTapped(at: indexPath)
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

将此类设置为故事板时间轴场景中单元格的自定义类。

在上面的类中，我们有几个`@IBOutlet`用于名称、照片和评论按钮。我们有一个`commentButtonWasTapped`方法，它在单元格的委托上触发`commentWasTapped`方法。

我们要创建的下一个单元格是`CommentsListTableViewCell`。创建该类并将以下代码粘贴到文件中:

```
 <span class="hljs-keyword">import</span> UIKit

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CommentsListTableViewCell</span>: <span class="hljs-title">UITableViewCell</span> </span>{
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> username: <span class="hljs-type">UILabel</span>!
        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> comment: <span class="hljs-type">UILabel</span>!
    } 
```

Enter fullscreen mode Exit fullscreen mode

将该类设置为故事板的 comments 场景中的单元格的自定义类。

我们要创建的下一个单元格是`UsersListTableViewCell`。创建该类并将以下代码粘贴到文件中:

```
 <span class="hljs-keyword">import</span> UIKit

    <span class="hljs-class"><span class="hljs-keyword">protocol</span> <span class="hljs-title">UserListCellFollowButtonDelegate</span> </span>{
        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">followButtonTapped</span><span class="hljs-params">(at index:IndexPath)</span></span>
    }

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">UserListTableViewCell</span>: <span class="hljs-title">UITableViewCell</span> </span>{
        <span class="hljs-keyword">var</span> indexPath: <span class="hljs-type">IndexPath</span>?    
        <span class="hljs-keyword">var</span> delegate: <span class="hljs-type">UserListCellFollowButtonDelegate</span>?

        <span class="hljs-meta">@IBOutlet</span> <span class="hljs-keyword">weak</span> <span class="hljs-keyword">var</span> followButton: <span class="hljs-type">UIButton</span>!

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">awakeFromNib</span><span class="hljs-params">()</span></span> {
            <span class="hljs-keyword">super</span>.awakeFromNib()
            <span class="hljs-keyword">self</span>.selectionStyle = .<span class="hljs-keyword">none</span>

            <span class="hljs-keyword">self</span>.setFollowStatus(<span class="hljs-literal">false</span>)
            <span class="hljs-keyword">self</span>.followButton.layer.cornerRadius = <span class="hljs-number">5</span>
            <span class="hljs-keyword">self</span>.followButton.setTitleColor(<span class="hljs-type">UIColor</span>.white, <span class="hljs-keyword">for</span>: .normal)
            <span class="hljs-keyword">self</span>.followButton.addTarget(<span class="hljs-keyword">self</span>, action: #selector(followButtonTapped(<span class="hljs-number">_</span>:)), <span class="hljs-keyword">for</span>: .touchUpInside)
        }

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">setFollowStatus</span><span class="hljs-params">(<span class="hljs-number">_</span> following: Bool)</span></span> {
            <span class="hljs-keyword">self</span>.followButton.backgroundColor = following ? <span class="hljs-type">UIColor</span>.red : <span class="hljs-type">UIColor</span>.blue
            <span class="hljs-keyword">self</span>.followButton.setTitle(following ? <span class="hljs-string">"Unfollow"</span> : <span class="hljs-string">"Follow"</span>, <span class="hljs-keyword">for</span>: .normal)
        }

        <span class="hljs-meta">@objc</span> <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">followButtonTapped</span><span class="hljs-params">(<span class="hljs-number">_</span> sender: UIButton)</span></span> {
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">let</span> delegate = delegate, <span class="hljs-keyword">let</span> indexPath = indexPath {
                delegate.followButtonTapped(at: indexPath)
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

将此类设置为故事板中搜索场景的单元格的自定义类。

在上面的类中，我们有一个自定义单元格来显示用户名和一个 follow 按钮。我们有一个`setFollowStatus`方法来切换 follow 按钮的状态，我们有一个`followButtonTapped`方法来调用单元格委托上的`followButtonTapped`方法。

自定义单元格类到此为止。让我们继续创建其他类并设置推送通知。

### 添加其他类别并设置推送通知

我们还需要创建最后一个文件。创建一个`AppConstants`文件，并将以下代码粘贴到该文件中:

```
 <span class="hljs-keyword">import</span> Foundation

    <span class="hljs-class"><span class="hljs-keyword">struct</span> <span class="hljs-title">AppConstants</span> </span>{
        <span class="hljs-keyword">static</span> <span class="hljs-keyword">let</span> <span class="hljs-type">API_URL</span> = <span class="hljs-string">"http://127.0.0.1:8000"</span>
        <span class="hljs-keyword">static</span> <span class="hljs-keyword">let</span> <span class="hljs-type">API_CLIENT_ID</span> = <span class="hljs-string">"API_CLIENT_ID"</span>
        <span class="hljs-keyword">static</span> <span class="hljs-keyword">let</span> <span class="hljs-type">API_CLIENT_SECRET</span> = <span class="hljs-string">"API_CLIENT_SECRET"</span>
        <span class="hljs-keyword">static</span> <span class="hljs-keyword">let</span> <span class="hljs-type">PUSHER_INSTANCE_ID</span> = <span class="hljs-string">"PUSHER_INSTANCE_ID
    }</span> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的结构中，我们有一些将在整个应用程序中使用的常量。这些将用于存储应用程序凭据，并且在应用程序的整个生命周期中不会更改。

> 💡将键值替换为从 Passport 安装和 Pusher 仪表板中获得的实际值。

接下来，打开`AppDelegate`类，将内容替换为以下内容:

```
 <span class="hljs-keyword">import</span> UIKit
    <span class="hljs-keyword">import</span> PushNotifications

    <span class="hljs-meta">@UIApplicationMain</span>
    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">AppDelegate</span>: <span class="hljs-title">UIResponder</span>, <span class="hljs-title">UIApplicationDelegate</span> </span>{

        <span class="hljs-keyword">var</span> window: <span class="hljs-type">UIWindow</span>?

        <span class="hljs-keyword">let</span> pushNotifications = <span class="hljs-type">PushNotifications</span>.shared

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">application</span><span class="hljs-params">(<span class="hljs-number">_</span> application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: <span class="hljs-keyword">Any</span>]?)</span></span> -> <span class="hljs-type">Bool</span> {
            <span class="hljs-keyword">self</span>.pushNotifications.start(instanceId: <span class="hljs-type">AppConstants</span>.<span class="hljs-type">PUSHER_INSTANCE_ID</span>)
            <span class="hljs-keyword">self</span>.pushNotifications.registerForRemoteNotifications()

            <span class="hljs-keyword">return</span> <span class="hljs-literal">true</span>
        }

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">application</span><span class="hljs-params">(<span class="hljs-number">_</span> application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data)</span></span> {
            <span class="hljs-keyword">self</span>.pushNotifications.registerDeviceToken(deviceToken)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的类中，我们使用 [Pusher Beams Swift SDK](https://docs.pusher.com/push-notifications/reference/ios) 为推送通知注册设备。

这就是我们应用程序的全部代码。

### 向我们的 iOS 新应用添加推送通知

现在我们已经完成了应用程序的逻辑，让我们在 Xcode 中启用应用程序上的推送通知。

在项目导航器中，选择您的项目，并点击 **Capabilities** 选项卡。[打开开关，启用推送通知](http://help.apple.com/xcode/mac/current/#/devdfd3d04a1)。

[![](img/ea3c51585520982eb32e6d56e0f1b74f.png)T2】](//images.ctfassets.net/1es3ne0caaid/nwh9O0inWCAwwsi82iq4o/efa381b5ae180d746608e151f4559f0e/ios-push-notifications-social-network-enable-push-notifications.png)

这将在项目的根目录下创建一个授权文件。至此，您已经为您的应用程序提供了完全接收推送通知的能力。

### 添加丰富推送通知

让我们更进一步，添加丰富的通知。我们希望能够在收到的通知中看到对照片的评论，因为这可以提高参与度。

在 Xcode 中，转到“文件”>“新建”>“目标”，然后选择“通知服务扩展”。输入扩展名，然后单击继续。确保扩展被添加并嵌入到 Gram 项目中。我们将调用我们的扩展**通知**。

当目标被创建后，你会看到一个新的`Notification`组(根据你选择的扩展名可能会有所不同),其中有两个文件。打开`NotificationService`类，用下面的方法替换`didReceive`方法:

```
 <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">didReceive</span><span class="hljs-params">(<span class="hljs-number">_</span> request: UNNotificationRequest, withContentHandler contentHandler: @escaping <span class="hljs-params">(UNNotificationContent)</span></span></span> -> <span class="hljs-type">Void</span>) {
        <span class="hljs-keyword">self</span>.contentHandler = contentHandler
        bestAttemptContent = (request.content.mutableCopy() <span class="hljs-keyword">as</span>? <span class="hljs-type">UNMutableNotificationContent</span>)

        <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">failEarly</span><span class="hljs-params">()</span></span> {
            contentHandler(request.content)
        }

        <span class="hljs-keyword">guard</span>
            <span class="hljs-keyword">let</span> content = (request.content.mutableCopy() <span class="hljs-keyword">as</span>? <span class="hljs-type">UNMutableNotificationContent</span>),
            <span class="hljs-keyword">let</span> apnsData = content.userInfo[<span class="hljs-string">"data"</span>] <span class="hljs-keyword">as</span>? [<span class="hljs-type">String</span>: <span class="hljs-type">Any</span>],
            <span class="hljs-keyword">let</span> photoURL = apnsData[<span class="hljs-string">"attachment-url"</span>] <span class="hljs-keyword">as</span>? <span class="hljs-type">String</span>,
            <span class="hljs-keyword">let</span> attachmentURL = <span class="hljs-type">URL</span>(string: photoURL.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed)!),
            <span class="hljs-keyword">let</span> imageData = <span class="hljs-keyword">try</span>? <span class="hljs-type">NSData</span>(contentsOf: attachmentURL, options: <span class="hljs-type">NSData</span>.<span class="hljs-type">ReadingOptions</span>()),
            <span class="hljs-keyword">let</span> attachment = <span class="hljs-type">UNNotificationAttachment</span>.create(imageFileIdentifier: <span class="hljs-string">"image.png"</span>, data: imageData, options: <span class="hljs-literal">nil</span>)
            <span class="hljs-keyword">else</span> {
                <span class="hljs-keyword">return</span> failEarly()
        }

        content.attachments = [attachment]
        contentHandler(content.copy() <span class="hljs-keyword">as</span>! <span class="hljs-type">UNNotificationContent</span>)
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面我们只是简单地获取通知负载，然后提取数据，包括照片 URL 的`attachment-url`。然后，我们为通知创建一个附件，并将其添加到通知的内容中。这就是我们将图片作为附件添加所需要做的全部工作。

> ⚠️你的图片网址必须是一个安全的网址与 HTTPS 或 iOS 将不会加载图像。您可以在您的`info.plist`文件中覆盖这个设置，但是强烈建议您不要这样做。

接下来，在通知扩展中创建一个名为`UNNotificationAttachment.swift`的新文件，并将以下内容粘贴到该文件中:

```
 <span class="hljs-keyword">import</span> Foundation
    <span class="hljs-keyword">import</span> UserNotifications

    <span class="hljs-class"><span class="hljs-keyword">extension</span> <span class="hljs-title">UNNotificationAttachment</span> </span>{

        <span class="hljs-keyword">static</span> <span class="hljs-function"><span class="hljs-keyword">func</span> <span class="hljs-title">create</span><span class="hljs-params">(imageFileIdentifier: String, data: NSData, options: [NSObject : AnyObject]?)</span></span> -> <span class="hljs-type">UNNotificationAttachment</span>? {
            <span class="hljs-keyword">let</span> fileManager = <span class="hljs-type">FileManager</span>.<span class="hljs-keyword">default</span>
            <span class="hljs-keyword">let</span> tmpSubFolderName = <span class="hljs-type">ProcessInfo</span>.processInfo.globallyUniqueString
            <span class="hljs-keyword">let</span> tmpSubFolderURL = <span class="hljs-type">NSURL</span>(fileURLWithPath: <span class="hljs-type">NSTemporaryDirectory</span>()).appendingPathComponent(tmpSubFolderName, isDirectory: <span class="hljs-literal">true</span>)

            <span class="hljs-keyword">do</span> {
                <span class="hljs-keyword">try</span> fileManager.createDirectory(at: tmpSubFolderURL!, withIntermediateDirectories: <span class="hljs-literal">true</span>, attributes: <span class="hljs-literal">nil</span>)
                <span class="hljs-keyword">let</span> fileURL = tmpSubFolderURL?.appendingPathComponent(imageFileIdentifier)
                <span class="hljs-keyword">try</span> data.write(to: fileURL!, options: [])
                <span class="hljs-keyword">let</span> imageAttachment = <span class="hljs-keyword">try</span> <span class="hljs-type">UNNotificationAttachment</span>(identifier: imageFileIdentifier, url: fileURL!, options: options)
                <span class="hljs-keyword">return</span> imageAttachment
            } <span class="hljs-keyword">catch</span> <span class="hljs-keyword">let</span> error {
                <span class="hljs-built_in">print</span>(<span class="hljs-string">"error \(error)"</span>)
            }

            <span class="hljs-keyword">return</span> <span class="hljs-literal">nil</span>
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码是对`UNNotificationAttachment`类的扩展。该扩展包含`create`方法，允许我们创建一个临时图像来存储作为推送通知发送的图像附件。

现在您可以使用 Xcode 构建您的应用程序了。确保 Laravel 应用程序正在运行，否则应用程序将无法获取数据。

### 允许我们的应用程序本地连接

如果你打算使用本地服务器测试应用程序的后端，那么我们还需要做最后一件事。打开`info.plist`文件并向`plist`文件添加一个条目，以允许连接到我们的本地服务器:

[![](img/4411e4b820004ee90ad7d74e4066ffe9.png)T2】](//images.ctfassets.net/1es3ne0caaid/5UymGAiLeg22C46SEyay4U/0f66ae00ca879dc03852ece398c955f9/ios-push-notifications-social-network-connect-locally.png)

现在就这样了。我们可以运行我们的应用程序。然而，**请记住，要演示推送通知，您需要一个实际的 iOS 设备，因为模拟器无法接收推送通知。**

下面是应用程序运行的屏幕记录:

[![](img/5c4aa46878dfc816c27776a70dc3264f.png)T2】](//images.ctfassets.net/1es3ne0caaid/51GgQzYiqQEMAQWOK8AMgS/fd0d5bb1bbaa7f1f198fed340d25cd53/ios-push-notifications-social-network-demo.gif)

## 结论

在本文中，我们看到了如何使用 Pusher Beams 从 Laravel 后端和 Swift iOS 客户端应用程序发送推送通知。在创建社交网络时，我们发送的推送通知必须是相关的，而不是垃圾邮件，Pusher Beams 可以帮助我们做到这一点。

应用程序的源代码在 [GitHub](https://github.com/neoighodaro-articles/pusher-beams-ios-social-network) 上。

这篇文章最初出现在 [Pusher 博客](https://pusher.com/tutorials/social-notifications-ios-part-2)上。