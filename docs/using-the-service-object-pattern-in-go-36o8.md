# 在 Go 中使用服务对象模式

> 原文：<https://dev.to/joncalhoun/using-the-service-object-pattern-in-go-36o8>

> *这篇文章最初发表在我的网站上[calhoun . io](https://www.calhoun.io/using-the-service-object-pattern-in-go/)T3】*

**注:** *这篇帖子里的大部分代码和想法都是我一直在尝试的东西。这并不意味着这些想法和教训是没有价值的，但它确实意味着你不应该只是盲目地遵循这种模式。它有自己的一套利弊，应该在个案的基础上加以考虑。也就是说，这种模式对我来说非常有效，使用一种设计将解析数据与应用程序逻辑隔离开来是构建支持多种格式(HTML 和 JSON API)的 web 应用程序的关键一步，我们将在以后的文章中探讨这一点。*

我们都可能在 Go 中见过一个 web 应用程序，它有一个如下所示的处理函数:

```
type  WidgetHandler  struct  {  DB  *sql.DB  // Renders the HTML page w/ a form to create a widget  CreateTemplate  *template.Template  // Renders a list of widgets in an HTML page  ShowTemplate  *template.Template  }  func  (handler  *WidgetHandler)  Create(w  http.ResponseWriter,  r  *http.Request)  {  // Most HTML based web apps will use cookies for sessions  cookie,  err  :=  r.Cookie("remember_token")  if  err  !=  nil  {  http.Redirect(w,  r,  "/login",  http.StatusFound)  return  }  // Hash the value since we store remember token hashes in our db  rememberHash  :=  hash(cookie.Value)  // Then look up the user in the database by hashed their remember token  var  user  User  row  :=  handler.DB.QueryRow(`SELECT id, email FROM users WHERE remember_hash=$1`,  rememberHash)  err  =  row.Scan(&user.ID,  &user.Email)  if  err  !=  nil  {  http.Redirect(w,  r,  "/login",  http.StatusFound)  return  }  // From here on we can assume we have a user and move on to processing  // the request  var  widget  Widget  widget.UserID  =  user.ID  err  =  r.ParseForm()  // TODO: handle the error  widget.Name  =  r.FormValue("name")  // postgres specific SQL  const  insertWidgetSql  =  `
INSERT INTO widgets (user_id, name) 
VALUES ($1, $2) 
RETURNING id`  err  =  handler.DB.QueryRow(insertWidgetSql,  widget.UserID,  widget.Name).Scan(&widget.ID)  if  err  !=  nil  {  // Render the error to the user and the create page  w.Header().Set("Content-Type",  "text/html")  handler.CreateTemplate.Execute(w,  map[string]interface{}{  "Error":  "Failed to create the widget...",  "Widget":  widget,  })  return  }  // Redirect the user to the widget  http.Redirect(w,  r,  fmt.Sprintf("/widgets/%d",  widget.ID),  http.StatusFound)  } 
```

具体细节可能会有所不同——例如，应用程序可能会使用另一个数据库，它可能会创建`UserService`和`WidgetService`接口，而不是直接编写 SQL，您可能会使用像 [echo](https://echo.labstack.com/) 这样的框架/路由器，但一般来说，代码看起来大致相同。处理程序的前几行将用于解析数据，然后我们将着手做我们真正想做的事情，最后我们将呈现任何结果或错误。

## 处理程序是数据解析和呈现层

如果我们回头看看我们的原始代码，令人震惊的是有多少代码实际上只是解析和呈现。整个 cookie 检索部分仅用于获取一个记住的令牌，或者在出现错误时重定向用户。一旦有了令牌，我们就执行数据库查找，但是很快又会有一个错误处理和呈现逻辑。然后解析表单，获取小部件名称，并呈现创建小部件时出现的任何错误。最后，如果创建了新的小部件，我们可以将用户重定向到该小部件，但是如果您仔细想想，重定向基本上也只是呈现逻辑。

总而言之，我们大约 60%的代码只是解析数据和呈现结果/错误。

解析这些数据本质上不是坏事，但是我发现令人不安的是，在解析数据之前，需求是不明确的。想想看——如果我把这个函数定义交给你，让你测试一下，你能告诉我它期望什么数据吗？

```
func  (handler  *WidgetHandler)  Create(w  http.ResponseWriter,  r  *http.Request) 
```

您可能能够从`WidgetHandler`类型和函数名- `Create`推断出这是用来创建一个小部件的，所以我们需要一些描述小部件的信息，但是您知道数据应该是什么格式吗？您知道用户需要通过基于 cookie 的会话登录吗？

更糟糕的是，我们甚至无法推断出`WidgetHandler`的哪些部分需要实例化才能工作。如果我们扫描代码，我们可以清楚地看到我们使用了`DB`字段，看起来我们在出现错误时渲染了`CreateTemplate`,所以我们需要设置它，但是我们必须浏览所有的代码，看看都使用了什么。

**注意:** *在这个例子中，我们使用哪些字段是显而易见的，但是想象一下我们的`WidgetHandler`被用来创建、更新、发布和执行小部件上的许多其他动作。在这种情况下，我们的`WidgetHandler`类型将有更多的字段，我们肯定不需要将它们都设置为只测试这个处理程序。*

处理函数需要模糊；如果不对传入的 http 请求有一个模糊的定义，然后编写一些代码来解析传入的数据，那么就没有可行的方法来创建 HTTP 服务器。即使我们创建了可重用的中间件，并利用来存储解析的数据，我们仍然需要编写和测试这些中间件，**它并没有解决我们的处理函数的数据需求**不明确的问题。那么我们如何解决这个问题呢？

## 服务对象模式

我们需要在处理程序中解析数据，而不是与之抗争，我发现更好的做法是接受它，让那些处理程序严格地解析呈现层的数据。也就是说，在我的 http 处理程序中，我试图避免任何与解析或呈现数据无关的逻辑，而是采用与 Ruby 中的[服务对象模式非常相似的模式。](https://hackernoon.com/service-objects-in-ruby-on-rails-and-you-79ca8a1c946e)

**注:** *在现实中，我甚至试图从处理程序中提取尽可能多的数据渲染。有关如何做到这一点的更多想法，请参见。*

该模式的工作方式非常简单——与其在我的处理程序中编写逻辑来做诸如创建小部件之类的事情，不如将 ocde 提取到一个具有明确数据需求且易于测试的函数中。例如，在小部件创建示例中，我可能会创建如下内容:

```
func  CreateWidget(db  *sql.DB,  userID  int,  name  string)  error  {  var  widget  Widget  widget.Name  =  name  widget.UserID  =  userID  const  insertWidgetSql  =  `
INSERT INTO widgets (user_id, name) 
VALUES ($1, $2) 
RETURNING id`  err  =  db.QueryRow(insertWidgetSql,  widget.UserID,  widget.Name).Scan(&widget.ID)  if  err  !=  nil  {  return  err  }  return  nil  } 
```

现在更清楚了，为了创建一个小部件，我们需要一个数据库连接、创建小部件的用户的 ID 和小部件的名称。

**注意:** *你不必在这里创建这样具体的要求。例如，我经常会创建这样的函数，它期望一个`User`和一个`Widget`作为它的参数，而不是更具体的`userID`和`name`参数。这个选择取决于你。*

## 一个更有趣的例子

这个例子很无聊，所以让我们看一个更有趣的例子。假设我们想让用户注册我们的应用程序，当这种情况发生时，我们在数据库中创建用户，向用户发送欢迎电子邮件，并将他们添加到我们的邮件列表工具中。传统的处理程序可能如下所示:

```
func  (handler  *UserHandler)  Signup(w  http.ResponseWriter,  r  *http.Reqeust)  {  // 1\. parse user data  r.ParseForm()  email  =  r.FormValue("email")  password  =  r.FormValue("password")  // 2\. hash the pw and create the user, handling any errors  hashedPw,  err  :=  handler.Hasher.Bcrypt(password)  if  err  !=  nil  {  // ... handle this  }  var  userID  int  err  :=  handler.DB.QueryRow("INSERT INTO users .... RETURNING id",  email,  hashedPw).Scan(&userID)  if  err  !=  nil  {  handler.SignupForm.Execute(...)  return  }  // 3\. Add the user to our mailing list  err  =  handler.MailingService.Subscribe(email)  if  err  !=  nil  {  // handle the error somehow  }  // 4\. Send them a welcome email  err  =  handler.Emailer.WelcomeUser(email)  if  err  !=  nil  {  // handle the error  }  // 5\. Finally redirect the user to their dashboard  http.Redirect(...)  } 
```

如你所见，我们有很好的错误处理，在每一个`if`块中，我们可以很容易地呈现一个错误页面，将用户送回注册页面，或者其他任何事情。我们还最终使用了处理程序的相当多的部分——`MailingService`、`SignupForm`、`Emailer`和`Hasher`——这些对于测试来说都不是显而易见的。

更糟糕的是，测试每一个单独的部分有点烦人。如果我们只想验证调用这个端点是否在数据库中创建了一个用户，我们至少还需要剔除所有其他部分。

在这种情况下，将我们的代码分割成几个具有明确需求并且可以独立测试的服务对象是非常有用的。

```
type  UserCreator  struct  {  DB  *sql.DB  Hasher  Emailer  MailingService  }  func  (uc  *UserCreator)  Run(email,  password  string)  (*User,  error)  {  pwHash,  err  :=  uc.Hasher.BCrypt(password)  if  err  !=  nil  {  return  nil,  err  }  user  :=  User{  Email:  email,  }  row  :=  uc.DB.QueryRow("INSERT INTO users .... RETURNING id",  email,  hashedPw)  err  =  row.Scan(&user.ID)  if  err  !=  nil  {  return  nil,  err  }  err  =  uc.MailingService.Subscribe(email)  if  err  !=  nil  {  // log the error  }  err  =  uc.Emailer.WelcomeUser(email)  if  err  !=  nil  {  // log the error  }  return  &user,  nil  } 
```

现在我们可以很容易地测试用来创建用户的代码；依赖关系很清楚，我们不需要在 HTTP 请求上浪费时间。这只是普通的旧代码。

我们还有简化处理程序代码的额外好处。它不再需要处理只需要记录信息的非致命错误，我们可以专注于解析数据。

```
type  UserHandler  struct  {  signup  func(email,  password  string)  (*User,  error)  }  func  (handler  *UserHandler)  Signup(w  http.ResponseWriter,  r  *http.Reqeust)  {  // 1\. parse user data  r.ParseForm()  email  =  r.FormValue("email")  password  =  r.FormValue("password")  user,  err  :=  handler.signup(email,  password)  if  err  !=  nil  {  // render an error  }  http.Redirect(...)  } 
```

要实例化这段代码，我们应该编写如下代码:

```
uc  :=  &UserCreator{...}  uh  :=  &UserHandler{signup:  uc.Run} 
```

然后我们就可以在路由器中自由地使用`uh`上的方法作为`http.HandlerFunc`了。

## 更多但更清晰的代码

这种方法显然需要更多的代码。我们现在需要设置一个`UserCreator`类型，然后将它的`Run`函数设置为`UserHandler`中的`signup`字段，但是通过这样做，我们已经清楚地分离了每个函数的角色，并且使得测试我们的代码更加容易。我们甚至不再需要数据库连接来测试我们的处理程序，而是可以用如下代码来测试它:

```
uh  :=  &UserHandler{  signup:  func(email,  password)  (*User,  error)  {  return  &User{  ID:  123,  Email:  email,  },  nil  }  } 
```

类似地，当测试我们的`UserCreator`时，我们根本不需要使用`httptest`包。太棒了。🙌

最后，正如我们将在后续的文章中看到的(我还在继续努力——这篇文章很长),这也为编写大多数不知道其输入/输出格式的应用程序打开了大门。也就是说，我们可以使用一个现有的 web 应用程序，并以相当小的努力添加 JSON API 支持。

## 你喜欢这篇文章吗？加入我的邮件列表！

如果你喜欢这篇文章，请考虑将[加入我的邮件列表](https://www.calhoun.io/#subscribe)。

我大概每周会给你发一封邮件，让你知道我最近正在写的或者已经发表的新文章(比如这篇)或者视频( [ex](https://www.calhoun.io/lets-learn-algorithms-stacks-what-they-are-and-how-to-implement-them-in-go/) )。没有垃圾邮件。不要出售你的电子邮件。没什么见不得人的- *我会像对待自己的收件箱一样对待你。*

作为对您加入的特别感谢，我还将向您发送我的课程 [Web Development with Go](https://www.usegolang.com) 的截屏和电子书样本。