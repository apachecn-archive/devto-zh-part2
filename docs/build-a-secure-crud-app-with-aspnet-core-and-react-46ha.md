# 使用 ASP.NET 核心构建一个安全的 CRUD 应用程序并做出反应

> 原文：<https://dev.to/oktadev/build-a-secure-crud-app-with-aspnet-core-and-react-46ha>

如今，允许两个(或更多)团队在一个项目上工作的“后端”和“前端”非常普遍。微软最新版本的 ASP.NET 核心框架是跨平台和高性能的。将它与脸书的 React 框架的功能和灵活性结合起来，使它成为一个非常稳定的平台。在本教程中，我将向您展示如何使用这两种强大的技术构建一个安全的 CRUD(创建、读取、更新和删除)应用程序。

当会议需要发言人时，他们有时会推出一个公共发言人“要求发言”或 CFP。然后，潜在的演讲者提交他们希望在会议上发表的演讲，组织者从这些提交的演讲中选择他们希望在会议上发表的演讲。

您将要构建的应用程序是一个演讲者提交页面。它将允许用户注册一个帐户，登录并提交潜在的会议会话。他们还需要能够登录后，更新他们的提交，或者，如果他们无法提出该报告，删除提交。

该应用程序的架构将是一个从 ASP.NET 核心网络 API 反馈数据的反应前端应用程序。出于演示的目的，您将使用实体框架的内存数据库选项。这是概念验证应用程序的绝佳选择。您可以稍后通过更改一行配置代码来更改它以使用 SQL Server 或 PostgreSQL 数据库！

## 入门所需

您需要一些工具来获取、开发和运行应用程序。

*   Git 命令行
*   的。NET Framework 2+
*   节点和 NPM
*   Visual Studio 或 VS 代码

我正在 Linux 笔记本电脑上开发应用程序。无论您使用的是 Linux、macOS 还是 Windows，大多数命令都是相同的。唯一不同的是您的 Okta 凭证存储在 ASP.NET 核心 API 的主文件夹中，我将把这两个路径都放在示例中。

## 获取 ASP.NET 基地核心和 React 应用

要为应用程序搭建基础，首先要克隆基本的 React 和 ASP.NET 核心应用程序。

```
git clone git@github.com:oktadeveloper/dotnetcore-react-crud-example 
```

Enter fullscreen mode Exit fullscreen mode

然后创建一个新的分支，我们将使用它把 shell 变成一个完全 CRUD 的应用程序。

```
git checkout -b crud-app 
```

Enter fullscreen mode Exit fullscreen mode

## 设置您的 Okta 应用程序

对于每个开发人员来说，处理 web 应用程序中的用户认证是一个巨大的痛苦。这就是 Okta 的闪光点:它帮助您以最小的努力保护您的 web 应用程序。首先，您需要在 Okta 中创建一个 OpenID Connect 应用程序。注册一个永远免费的开发者账户(或者如果你已经有了的话就登录)。

[![Okta Signup Screenshot](img/4c4cec62bb393084b380961aed8243be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BHC0CKn5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/crud-aspnet-core-react/okta-signup-accab135cb5e7cb06a3446679d6aef0958ea31b3b9444d87ffb2f70e5882d045.png)

一旦您登录并登录到仪表板页面，请复制下图中的 Org URL。你以后会需要这个的。

[![Org Url Dashboard Screenshot](img/4c0591ffca2695de422ca40a02996cf3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xd2neOfj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/crud-aspnet-core-react/okta-org-url-38ad1e82ab84ae824e053148055d7b7456c5675d3ef3f27e06874ba47904fcc4.png)

然后通过浏览到**应用**选项卡并点击**添加应用**来创建一个新的应用。

[![Application Dashboard Screenshot](img/5e914cd4f078ac5dc20b86e2939def59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bbFxAUTc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/crud-aspnet-core-react/okta-app-dashboard-28e02cba86bd97f0898d883d5a416ac6581d56d2207623f5ae94c816103976e2.png)

从向导中，为 React 应用程序选择**单页应用程序**选项。

[![App Wizard screenshot with Single Page App Selected](img/16fbd27dc80ca8b48b4944af56fe0e33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1kLbjoko--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/crud-aspnet-core-react/single-page-app-679f5e1ea17bc95523dfd741084c8af93011042aa300eda87e6a515a6821ffe6.png)

在“设置”页面上，输入以下值:

*   **名称:**反应堆
*   **基地 URIs:** `http://localhost:5000`
*   **登录重定向 URIs:** `http://localhost:5000/implicit/callback`

您可以保持其他值不变。

[![Application Settings Screenshot](img/930a1fad753046f6e9abbfac07b50ee9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m5cNuzbB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/crud-aspnet-core-react/application-settings-3f9b43a78e47b54c1cb0abc3fed7f849528823dd6bd58164c2b74962826c2754.png)

现在您的应用程序已经创建好了，请将客户端 ID 和客户端密码值复制到下一页，您很快就会用到它们。

[![Application Secrets Screenshot](img/ca38b91dd152bf6fa7ea1f7751d440ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BbHswXlb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/crud-aspnet-core-react/okta-app-secrets-04f9ae1d82ab33072a0de5187e16c23eac3752b8fac955e10d4d2d6377358b12.png)

最后，创建一个新的身份验证令牌。这将允许你的应用程序与 Okta 对话，以检索用户信息等。为此，点击页面顶部的 **API** 标签，然后点击**创建令牌**按钮。给你的令牌起个名字，在这个例子中“Crud API”是个好名字，然后点击**创建令牌**。记下这个令牌值，因为您很快就会用到它。

[![API Token Creation Screenshot](img/ebfe48023365929d4cc70f28aa79cc33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hoh6Ggha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/crud-aspnet-core-react/create-api-token-1e8f16398b8899e64b9aa02b9ab180535a3bd27d489cb1e0303212af2914f2bc.png)

一旦创建了新的 Okta 应用程序，就需要将配置信息存储在 React 和 ASP.NET 核心应用程序中。

对于 React 应用程序，在`/ClientApp`文件夹中创建一个名为`app.config.js`的文件。这将导出一个简单的 JSON 对象，其中包含 React 应用程序的配置信息。

```
export default {
  url: 'https://{yourOktaDomain}',
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  redirect_uri: window.location.origin + '/implicit/callback',
  client_id: '{yourClientID}'
} 
```

Enter fullscreen mode Exit fullscreen mode

对于 ASP.NET 核心应用程序，最好的办法是在您的主文件夹中建立一个文件来存储配置。Okta 的 SDK 将为您选择设置，您永远不会意外地将它们签入源代码控制！

在您的主目录中，创建一个`.okta`文件夹并添加一个名为`okta.yaml`的文件。您的个人文件夹将取决于您的操作系统。对于*nix 变种，如 Linux 或 macOS，它是:

```
~/.okta/okta.yaml 
```

Enter fullscreen mode Exit fullscreen mode

对于 Windows 环境，它是:

```
%userprofile%\.okta\okta.yaml 
```

Enter fullscreen mode Exit fullscreen mode

YAML，只是一个文件格式的配置。`okta.yaml`文件看起来像:

```
okta:
  client:
    orgUrl: "https://{yourOktaDomain}/"
    token: "{yourApiToken}" 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经准备好设置依赖项了！

## 安装您的 React 依赖项

为了充分利用 Okta 进行身份管理，您需要 Okta 的 React SDK 和它所依赖的通用 JavaScript 包。要安装，从命令行运行:

```
npm install @okta/okta-react@1.0.2 --save 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经准备好编写一些代码了！

## 向 React 应用程序添加注册

首先为将要创建的身份验证组件添加一个文件夹。在`/ClientApp/components`文件夹中，创建一个名为`auth`的文件夹，并在里面创建一个`RegisterPage.js`文件。页面将是由`withAuth`高阶组件包装的组件。要了解更多关于高阶组件的信息，请阅读 React 网站上的文档[。](https://reactjs.org/docs/higher-order-components.html)

最终的`RegisterPage.js`组件将如下所示:

```
import React from 'react'; 
import { Redirect} from 'react-router-dom';
import OktaAuth from '@okta/okta-auth-js';
import { withAuth } from '@okta/okta-react';

import config from '../../app.config';

export default withAuth(class RegisterPage extends React.Component{
  constructor(props){
    super(props);
    this.state = {
      firstName: '',
      lastName: '',
      email: '',
      password: '',
      sessionToken: null,
      registered: false
    };
    this.oktaAuth = new OktaAuth({ url: config.url });
    this.checkAuthentication = this.checkAuthentication.bind(this);
    this.checkAuthentication();

    this.handleSubmit = this.handleSubmit.bind(this);
    this.handleFirstNameChange = this.handleFirstNameChange.bind(this);
    this.handleLastNameChange = this.handleLastNameChange.bind(this);
    this.handleEmailChange = this.handleEmailChange.bind(this);
    this.handlePasswordChange = this.handlePasswordChange.bind(this);    
  }

  async checkAuthentication() {
    const sessionToken = await this.props.auth.getIdToken();
    if (sessionToken) {
      this.setState({ sessionToken });
    }
  }

  componentDidUpdate() {
    this.checkAuthentication();
  }

  handleFirstNameChange(e){
    this.setState({firstName:e.target.value});
  }
  handleLastNameChange(e) {
    this.setState({ lastName: e.target.value });
  }
  handleEmailChange(e) {
    this.setState({ email: e.target.value });
  }
  handlePasswordChange(e) {
    this.setState({ password: e.target.value });
  }

  handleSubmit(e){
    e.preventDefault();
    fetch('/api/users', { 
      method: 'POST', 
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(this.state)
    }).then(user => {
      this.setState({ registered: true });
    })
    .catch(err => console.log);
  }

  render(){
    if (this.state.sessionToken) {
      this.props.auth.redirect({ sessionToken: this.state.sessionToken });
      return null;
    }

    if(this.state.registered === true){
      return <Redirect to="/login"/>
    }

    return(
      <form onSubmit={this.handleSubmit} className="registration">
        <div className="form-element">
          <label>Email:</label>
          <input type="email" id="email" value={this.state.email} 
          onChange={this.handleEmailChange}/>
        </div>
        <div className="form-element">
          <label>First Name:</label>
          <input type="text" id="firstName" value={this.state.firstName} 
          onChange={this.handleFirstNameChange} />
        </div>
        <div className="form-element">
          <label>Last Name:</label>
          <input type="text" id="lastName" value={this.state.lastName} 
          onChange={this.handleLastNameChange} />
        </div>
        <div className="form-element">
          <label>Password:</label>
          <input type="password" id="password" value={this.state.password} 
          onChange={this.handlePasswordChange} />
        </div>
        <div className="form-actions">
          <input type="submit" id="submit" className="btn btn-primary" value="Register"/>
        </div>
      </form>
    );
  }

}); 
```

Enter fullscreen mode Exit fullscreen mode

我知道这看起来很多，但大部分都很简单。让我们把它分成几类:状态、处理程序和 Okta 之类的东西。

## 处理 React 应用程序中的用户状态

构造函数中声明的状态有六个属性。`firstName`、`lastName`、`email`和`password`是在 Okta 注册账户的必填字段..当用户已经登录时，使用`sessionToken`属性检查用户是否错误地进入页面。使用从构造函数运行的`checkAuthentication()`方法对其进行检查。`render()`方法检查`sessionToken`的值，如果会话令牌存在，重定向用户被重定向到主页。

组件中有许多处理函数:一个用于用户将在表单中编辑的每个属性，另一个用于处理注册表单的提交。表单上每个字段的处理程序都很简单。当用户更改字段的值时，它们会更新状态。表单提交处理程序完全按照您的预期工作；它将表单中的信息提交到一个 URL，这样 API 就可以在 Okta 中创建用户。

`withAuth`高阶组件包装了`RegisterPage`组件，并为道具添加了一个`auth`对象，该对象可以为当前登录的用户做类似于`getIdToken()`的事情。任何需要获取当前用户或关于他们的信息的组件都需要由`withAuth`高阶组件包装。

## 添加注册到 ASP.NET 核心 API

为了进行用户注册，您需要在注册页面中添加您调用的端点。这个端点将调用 Okta 并使用 Okta 添加一个用户。NET SDK。

在`Controllers`文件夹中添加一个名为`UserController.cs`的控制器。然后，您将向控制器添加一个`POST`动作，以处理获取用户的注册信息并在 Okta 中创建用户。

```
using Microsoft.AspNetCore.Mvc;
using Okta.Sdk;
using Okta.Sdk.Configuration;
using okta_dotnetcore_react_example.Models;

namespace okta_dotnetcore_react_example.Controllers
{
  [Route("api/[controller]")]
  public class UsersController : Controller
  {
    [HttpPost]
    public async void Post([FromBody]Registration reg)
    {

      var oktaClient = new OktaClient();
      var user = await oktaClient.Users.CreateUserAsync(
          new CreateUserWithPasswordOptions
          {
            Profile = new UserProfile
            {
              FirstName = reg.FirstName,
              LastName = reg.LastName,
              Email = reg.Email,
              Login = reg.Email
            },
            Password = reg.Password,
            Activate = true
          }
      );
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您还需要在`Models`文件夹中的`Registration.cs`文件。

```
namespace okta_dotnetcore_react_example.Models
{
    public class Registration
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public string Email { get; set; }
        public string Password { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么复杂的，只是一组保存注册信息的属性。

## 向 React 应用程序添加认证

现在，您需要为用户添加使用新创建的帐户进行身份验证的功能。为此，向名为`LoginPage.js`的`/ClientApp/auth`文件夹添加一个文件。

登录页面将被包装在`withAuth`组件中，但也需要来自通用 JavaScript 库的`OktaAuth`组件。

```
import React from 'react';
import OktaAuth from '@okta/okta-auth-js';
import { withAuth } from '@okta/okta-react';

export default withAuth(class LoginPage extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      sessionToken: null,
      error: null,
      username: '',
      password: ''
    }

    this.oktaAuth = new OktaAuth({ url: props.baseUrl });

    this.handleSubmit = this.handleSubmit.bind(this);
    this.handleUsernameChange = this.handleUsernameChange.bind(this);
    this.handlePasswordChange = this.handlePasswordChange.bind(this);
  }

  handleSubmit(e) {
    e.preventDefault();
    this.oktaAuth.signIn({
      username: this.state.username,
      password: this.state.password
    })
      .then(res => this.setState({
        sessionToken: res.sessionToken
      }))
      .catch(err => {
        this.setState({error: err.message});
        console.log(err.statusCode + ' error', err)
      });
  }

  handleUsernameChange(e) {
    this.setState({ username: e.target.value });
  }

  handlePasswordChange(e) {
    this.setState({ password: e.target.value });
  }

  render() {
    if (this.state.sessionToken) {
      this.props.auth.redirect({ sessionToken: this.state.sessionToken });
      return null;
    }

    const errorMessage = this.state.error ? 
    <span className="error-message">{this.state.error}</span> : 
    null;

    return (
      <form onSubmit={this.handleSubmit} className="login">
        {errorMessage}
        <div className="form-element">
          <label>Username:</label>
          <input
            id="username" type="text"
            value={this.state.username}
            onChange={this.handleUsernameChange} />
        </div>

        <div className="form-element">
          <label>Password:</label>
          <input
            id="password" type="password"
            value={this.state.password}
            onChange={this.handlePasswordChange} />
        </div>
        <div className="form-actions">
        <input id="submit" type="submit" value="Submit" />
        </div>
      </form>
    );
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

既然您已经构建了注册页面组件，这个组件应该更容易理解。状态又有了`sessionToken`，这一次是为了在用户登录后进行检查。`username`和`password`属性用于表单，`error`属性允许您存储向用户显示的验证错误消息。

这些处理程序与注册组件中的处理程序非常相似，它们在用户更新表单值时更新表单值，而`handleSubmit()`函数再次处理表单提交按钮的动作。

这里也有更多的 Okta 功能。在构造函数中，有一个用基本 URL(来自 props)创建的新的`OktaAuth`对象，表单提交处理程序使用它。当提交表单时，用用户输入的用户名和密码调用`OktaAuth`对象的`signIn()`方法，并通过 promise 返回一个带有会话令牌的响应。在承诺的`then`中，该方法将会话令牌添加到会话状态。Okta SDK 将为您处理令牌的存储，接下来您将对其进行设置。

## 向 React 应用程序添加一个个人资料页面

一旦用户登录，您将需要一个地方让他们查看他们提交的列表，所以在`/ClientApp/components/auth`文件夹中创建一个名为`ProfilePage.js`的新文件。

同样，您需要将组件包装在`withAuth`组件中，以获得在 props 中传递的`auth`对象。

```
import React from 'react';
import { Link } from 'react-router-dom';
import { withAuth } from '@okta/okta-react';

export default withAuth(class ProfilePage extends React.Component {
  constructor(props){
    super(props);
    this.state = { 
      user: null
    };
    this.getCurrentUser = this.getCurrentUser.bind(this);
  }

  async getCurrentUser(){
    this.props.auth.getUser()
      .then(user => this.setState({user}));
  }

  componentDidMount(){
    this.getCurrentUser();
  }

  render() {
    if(!this.state.user) return null;
    return (
      <section className="user-profile">
        <h1>{this.state.user.name}'s Submitted Sessions</h1>
      </section>
    )
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

简单吧？只是一个用户对象，处于可以在组件中使用的状态，由更高级的组件`withAuth`提供！`componentDidMount`生命周期方法检索用户，而`render()`方法只是在呈现页面之前检查用户。这允许组件在等待这个异步调用返回时进行呈现，并在用户对象出现时进行更新。

## 为登录和注册页面添加路线

这里有一些要添加到`/ClientApp/boot.js`文件中的东西。首先，您需要从 Okta React SDK 导入一些东西，所以添加这个导入语句:

```
import { Security, SecureRoute, ImplicitCallback } from '@okta/okta-react'; 
```

Enter fullscreen mode Exit fullscreen mode

当您将它们添加到`render()`方法时，我会马上解释这些，但是现在，您还需要导入您刚刚创建的组件。

```
import config from './app.config';
import RegisterPage from './components/auth/RegisterPage';
import ProfilePage from './components/auth/ProfilePage';
import LoginPage from './components/auth/LoginPage'; 
```

Enter fullscreen mode Exit fullscreen mode

然后像这样更新`render()`方法:

```
render(
  <BrowserRouter>
    <Security issuer={config.issuer}
      client_id={config.client_id}
      redirect_uri={config.redirect_uri}
      onAuthRequired={onAuthRequired}>
      <Layout>
        <Route exact path="/" component={HomePage} />
        <Route path="/contact" component={ContactPage} />
        <Route path="/about" component={AboutPage} />
        <Route path="/notes" component={NotesPage} />
        <Route path="/login" render={() => <LoginPage baseUrl={config.url} />} />
        <Route path="/implicit/callback" component={ImplicitCallback} />
        <Route path="/register" component={RegisterPage} />
        <SecureRoute path="/profile" component={ProfilePage} />
      </Layout>
    </Security>
  </BrowserRouter>,
  document.getElementById('app')
); 
```

Enter fullscreen mode Exit fullscreen mode

这里有几件值得注意的事情。首先是`Security`组件。它接收关于 Okta 应用程序的所有信息，并包装所有的`Route`组件，以便它们可以做类似于`signIn()`的事情，并使这些信息可用。它还有一个`onAuthRequired`属性，将由您添加到`render()`方法之上的方法来处理:

```
const onAuthRequired = ({ history }) => history.push('/login'); 
```

Enter fullscreen mode Exit fullscreen mode

当需要认证时，该方法将用户重定向到登录页面(就像对`SecureRoute`路由一样)。`SecureRoute`简单地触发那个`onAuthRequired()`处理程序。

登录路线看起来有点搞笑。没有`component`属性让 React 知道哪个组件将处理该路由。然而，`render`属性允许你指定一个“快速组件”，如果你愿意的话，只需要一个 render 方法。这个 render 方法输出了`LoginPage`组件，但是它向组件中的 props 传递了一个`baseUrl`属性。

最后，`/implicit/callback`的路由由 Okta 的 React SDK 中的`ImplicitCallback`组件处理。还记得在 Okta 中配置应用程序时的设置吗？它处理从 Okta 返回的身份验证响应，并存储用户的令牌。

## 为 React 应用程序添加导航

在`/ClientApp/components/common/Navigation.js`文件中，您需要为所有这些新路线添加导航链接。您将希望再次导入那个`withAuth`组件，将该组件包装在`withAuth`高阶组件中，并添加根据用户当前是否登录来更改菜单项所需的方法。

```
import React from 'react';
import { Link } from 'react-router-dom';
import { withAuth } from '@okta/okta-react';

export default withAuth(class Navigation extends React.Component {
  constructor(props) {
    super(props);
    this.state = { authenticated: null };
    this.checkAuthentication = this.checkAuthentication.bind(this);
    this.logout = this.logout.bind(this);
    this.checkAuthentication();
  }

  async checkAuthentication() {
    const authenticated = await this.props.auth.isAuthenticated();
    if (authenticated !== this.state.authenticated) {
      this.setState({ authenticated });
    }
  }

  componentDidUpdate() {
    this.checkAuthentication();
  }

  logout(){
    this.props.auth.logout('/');
  }

  render() {
    if (this.state.authenticated === null) return null;
    const authNav = this.state.authenticated ?
      <ul className="nav navbar-nav navbar-right">
        <li><a href="javascript:void(0)" onClick={this.logout}>Logout</a></li>
        <li><Link to="/profile">Profile</Link></li>
      </ul> :
      <ul className="nav navbar-nav navbar-right">
        <li><Link to="/login">Login</Link></li>
        <li><Link to="/register">Register</Link></li>
      </ul>;

    return (
      <div className="navbar navbar-inverse navbar-fixed-top">
        <div className="container">
          <div className="navbar-header">
            <button type="button" className="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
              <span className="sr-only">Toggle navigation</span>
              <span className="icon-bar"></span>
              <span className="icon-bar"></span>
              <span className="icon-bar"></span>
            </button>
            <Link to="/" className="navbar-brand">React App</Link>
          </div>
          <div className="navbar-collapse collapse">
            <ul className="nav navbar-nav">
              <li><Link to="/about">About</Link></li>
              <li><Link to="/contact">Contact</Link></li>
              <li><Link to="/notes">Notes</Link></li>
            </ul>
            {authNav}
          </div>
        </div>
      </div>
    )
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一的*新*是`render()`方法里面的三元运算。它只是根据用户是否经过身份验证生成一些 JSX 菜单，并将其存储在一个变量中。然后在`return`中，它将该变量添加为第二个右侧菜单。

这样，您应该能够使用 **F5** 运行应用程序，注册用户，并让他们登录。

## 添加一个 Talk 提交 React 组件

现在，潜在的发言人可以登录，他们需要一种方法来提交在会议上发言的建议。为此，通过在名为`sessions`的`/ClientApp/components`文件夹中添加一个文件夹来创建一组新的组件。在那个文件夹中，创建一个`SubmissionPage.js`。这将处理提案的创建，然后将它们发送到 API 以保存在数据库中。

```
import React from 'react';
import { withRouter } from 'react-router-dom';
import { withAuth } from '@okta/okta-react';

class SubmissionPage extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      title: '',
      abstract: '',
      submitted: false
    };

    this.handleTitleChange = this.handleTitleChange.bind(this);
    this.handleAbstractChange = this.handleAbstractChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleTitleChange(e) {
    this.setState({ title: e.target.value });
  }

  handleAbstractChange(e) {
    this.setState({ abstract: e.target.value });
  }

  async handleSubmit(e){
    e.preventDefault();
    fetch('/api/sessions', {
      body: JSON.stringify(this.state),
      cache: 'no-cache',
      headers: {
        'content-type':'application/json',
        Authorization: 'Bearer ' + await this.props.auth.getAccessToken()
      },
      method: 'POST'
    })
    .then(rsp => {
      if(rsp.status === 201){
        this.props.history.push('/profile');
      }
    })
    .catch(err => {
      console.error(err);
    });
  }

  render(){
    if(this.state.submitted === true){
      <Redirect to="/profile"/>
    }
    return(
      <form onSubmit={this.handleSubmit}>
        <div className="form-element">
          <label>Title:</label>
          <input
            id="title" type="text"
            value={this.state.title}
            onChange={this.handleTitleChange} />
        </div>
        <div className="form-element">
          <label>Abstract:</label>
          <textarea
            id="abstract"
            cols="100"
            rows="10"
            value={this.state.abstract}
            onChange={this.handleAbstractChange} />
        </div>
        <div className="form-actions">
          <input id="submit" type="submit" value="Submit Session"/>
        </div>
      </form>
    );
  }

};

export default withAuth(withRouter(SubmissionPage)); 
```

Enter fullscreen mode Exit fullscreen mode

您会看到这看起来很像登录和注册页面，只是有一些小的不同。处理程序现在应该非常熟悉处理字段更新和表单提交。最大的区别在于`handleSubmit()`函数和高阶元件。

表单提交是在做一个 POST(类似于注册页面)，但是它添加了一个值为“Bearer { theUsersAccessToken }”的`Authorization`头。这就是使用`withAuth`高阶组件的原因，它的存在是为了让服务器端通过发送一个[承载令牌](https://oauth.net/2/bearer-tokens/)来知道是谁在发出请求。

表单提交处理程序还使用`this.props.history`在提交被保存后将用户重定向回 profile 页面。您还会注意到高阶函数也在底部包装提交页面组件。那是怎么回事？当使用两个包装函数时，比如使用`withAuth` **和**`withRouter`高阶组件，我认为在底层导出组件更具可读性。两种导出组件的语法都有效。

为了使提交更容易，在用户的概要文件上添加一个按钮来添加新的提交，这样`render()`方法看起来就像:

```
render() {
  if(!this.state.user) return null;
  return (
    <section className="user-profile">
      <h1>{this.state.user.name}'s Submitted Sessions</h1>
      <Link to="/submission" className="btn btn-primary">Submit A Session</Link>
    </section>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记在`boot.js`中为新组件添加一条安全路径！

```
<SecureRoute path="/submission" component={SubmissionPage} /> 
```

Enter fullscreen mode Exit fullscreen mode

提交页面会将用户的输入发送到服务器，现在您需要告诉服务器如何处理这些令人惊叹的东西！

## 添加会话创建路由到 ASP.NET 核心

为了保存信息，您将需要一些东西:您将需要实体框架以便更容易地与数据存储进行交互，并且您将为此需要一个数据库上下文。在`Models`文件夹中创建一个`ApiContext.cs`文件。这个 C#类应该从`DbContext`派生，你需要为 EntityFramework 导入一个 using。

```
using Microsoft.EntityFrameworkCore;

namespace okta_dotnetcore_react_example.Data
{
  public class ApiContext : DbContext
  {
    public ApiContext(DbContextOptions<ApiContext> options)
  : base(options)
    { }

    public DbSet<Session> Sessions { get; set; }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是在数据存储中设置了一个“Sessions”表，其中将存储每个类型为`Session`的对象。

您还需要一个`Session`模型，因此您需要在`Models`文件夹中创建`Session.cs`文件并添加:

```
using System.ComponentModel.DataAnnotations;

namespace okta_dotnetcore_react_example.Models
{
    public class Session
    {
        [Key]
        public int SessionId { get; set; }

        public string UserId { get; set; }
        public string Title { get; set; }
        public string Abstract { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一值得注意的是`Key`数据注释，它告诉实体框架表中的主键值是什么。这也意味着，由于我们没有指定生成键值的方法，Entity Framework 将使用缺省值，从 1 开始递增。

最后一个数据库上下文设置步骤是添加到`ConfigureServices()`函数中的`Startup.cs`文件:

```
services.AddDbContext<ApiContext>(options => options.UseInMemoryDatabase("ConferenceDb")); 
```

Enter fullscreen mode Exit fullscreen mode

这让。NET 知道将数据库上下文注入到每个需要它的控制器中。

现在，React 应用程序需要一个端点来发送数据。您已经知道端点需要什么样子，因为提交组件正在向`/api/session`提交数据，所以您需要一个控制器来处理传入的数据。

在`Controllers`文件夹中创建一个名为`SessionsController.cs`的文件，并添加如下内容:

```
using System.Linq;
using System.Security.Claims;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using okta_dotnetcore_react_example.Data;
using okta_dotnetcore_react_example.Models;

namespace okta_dotnetcore_react_example.Controllers
{
  [Authorize]
  [Route("/api/[controller]")]
  public class SessionsController : Controller
  {
    private readonly ApiContext context;
    public SessionsController(ApiContext context)
    {
      this.context = context;
    }

    [HttpPost]
    public IActionResult AddSession([FromBody] Session session)
    {
      session.UserId = User.Claims.SingleOrDefault(u=>u.Type == "uid")?.Value;
      context.Add<Session>(session);
      context.SaveChanges();
      return Created($"api/sessions/{session.SessionId}", session);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

控制器上的`AddSession`动作在参数上具有`FromBody`属性。这让 ASP。NET 的默认模型绑定器知道如何获取传入的 JSON 数据并将其映射到一个`Session`类型。然后，该操作获取会话，并将`UserId`属性设置为已验证用户的用户 id 值。然后，它将会话添加到上下文中的`Sessions`集合，并将这些更改保存回数据库。

最后，它返回一个`IActionResult`类型。有一些“助手”可以创建常见的操作结果。在这里，它返回一个带有 201 HTTP 状态的消息，这意味着已经创建了一个资源。第一个参数是将来检索该资源的 URL，第二个参数是会话。传出会话现在将有一个数据存储在插入记录时创建的`SessionId`。

控制器类上的`Authorize`属性保护控制器上的每个动作。因此，要调用这里声明的任何端点，用户必须提供一个访问令牌(作为承载令牌)。为了确保它在那里，您需要在`Startup.cs`文件中再添加一个东西。

在`services.AddMvc()`行之前的`ConfigureServices()`方法中，添加:

```
services.AddAuthentication(sharedOptions =>
{
  sharedOptions.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
  sharedOptions.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
  options.Authority = "https://{yourOktaOrgUrl}/oauth2/default";
  options.Audience = "api://default";
}); 
```

Enter fullscreen mode Exit fullscreen mode

并在`Configure()`方法的`app.UseMvc()`行前添加:

```
app.UseAuthentication(); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在，您的 ASP.NET 核心应用程序将获取这个不记名令牌，从 Okta 获取用户信息，并将它们添加到`User`对象中，这样您就可以获取当前请求用户的数据。

## 阅读 React 应用程序中的会话

即使用户现在可以向您的会议提交会话，他们也需要能够看到他们提交的列表，以便他们可以看到他们提交的内容，并在需要时编辑和删除它们。首先将提交列表添加到配置文件页面。在`/ClientApp/components/auth/ProfilePage.js`组件中，添加一个列出用户提交的会话的组件。将渲染方法改为:

```
render() {
  if(!this.state.user) return null;
  return (
    <section className="user-profile">
      <h1>{this.state.user.name}'s Submitted Sessions</h1>
      <Link to="/submission" className="btn btn-primary">Submit A Session</Link>
      <SessionListing userId={this.state.user.sub} />
    </section>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这将把当前登录用户的 id 传递给您接下来将创建的`SessionListing`组件。

回到`/ClientApp/components/sessions`文件夹，创建一个`SessionListing.js`文件。

```
import React from 'react';
import { withAuth } from '@okta/okta-react';

import './SessionListing.css';
import Session from './Session';

export default withAuth(class SessionListing extends React.Component {
  constructor(props) {
    super(props);
    this.state = { sessions: [] }
  }

  async getUsersSessions() {
    fetch('/api/sessions', {
      headers: {
        Authorization: 'Bearer ' + await this.props.auth.getAccessToken()
      }
    })
    .then(rsp => rsp.json())
    .then(sessions => {
      this.setState({ sessions });
    })
    .catch(err => {
      console.error(err);
    });
  }

  componentDidMount() {
    this.getUsersSessions();
  }

  render() {
    return (
      <ul className="session-list">
        {this.state.sessions.map(session => 
          <Session key={session.sessionId} 
            id={session.sessionId} 
            session={session} />)}
      </ul>
    )
  }

}) 
```

Enter fullscreen mode Exit fullscreen mode

所有这些现在应该都很熟悉了。一旦组件被挂载，就调用`getUserSessions()`方法，当前用户的访问令牌在`Authorize`头中被发送。在 header 上调用的`map`函数实际上会遍历会话，并将它们传递给`Session`组件进行显示。`render()`方法只需要多一件事:一个`Session`组件来传递每个会话。

> 提示:如果你得到一个`cannot call map on undefined`错误。确保您已经将状态中的`sessions`属性初始化为一个空数组。

将一个`Session.js`文件添加到清单组件所在的`sessions`文件夹中。

```
import React from 'react';
import {Link} from 'react-router-dom';

const Session = (props) => {
  return (
    <li key={props.id} className="session">
      <h2>{props.session.title}</h2>
      <div>{props.session.abstract}</div>
    </li>
  );    
}

export default Session; 
```

Enter fullscreen mode Exit fullscreen mode

如果你是一个新手，这就是所谓的表象或“愚蠢”的组成部分。它没有被包装在`class`类型中，只是接受道具和渲染输出。它就是为这种情况而制造的。这个组件所做的只是显示会话提交的标题和摘要。

## 从您的 ASP.NET 核心应用程序获取会话

既然 React 应用程序可以请求所有用户会话的列表，ASP.NET 核心 API 需要一种方法来响应。在`SessionsController.cs`文件中添加一个新动作:

```
[HttpGet]
public IActionResult GetAllSessions()
{
  var userId = User.Claims.SingleOrDefault(u=>u.Type == "uid")?.Value;
  var sessions = context.Sessions.Where(x=>x.UserId == userId).ToList(); 
  return Ok(sessions);
} 
```

Enter fullscreen mode Exit fullscreen mode

因为这是在一个具有`Authorize`属性的控制器中，所以将有一个`User`对象需要询问。在这里，用户返回他们的“uid”，这是他们用户的惟一标识符。然后，上下文在数据存储中的所有会话中搜索属于该用户的会话，并以 200 OK HTTP 状态返回它们。

## 使会话在 React 中可编辑

现在，用户可以看到他们提交的会话，如果他们犯了任何错误，或者想要更新内容，他们需要能够编辑它们。对于`Session`组件，将返回改为:

```
 return (
    <li key={props.id} className="session">
      <h2><Link to={`/submission/${props.session.sessionId}`}>{props.session.title}</Link></h2>
      <div>{props.session.abstract}</div>
    </li>
  ); 
```

Enter fullscreen mode Exit fullscreen mode

该链接将把用户带到提交页面，并通过会话的`sessionId`。为了处理这个问题，您需要更新提交页面的路由来处理路由参数。更新`boot.js`文件，更改`/submissions`的路线。

```
<SecureRoute path="/submission/:sessionId?" component={SubmissionPage} /> 
```

Enter fullscreen mode Exit fullscreen mode

`/submission/`后面的冒号让 React 知道这里的值将被放入`this.props.match.params`中，供处理路由的组件使用。路由后面的问号表明该参数是可选的，并且`SubmissionPage`组件将处理有和没有可选参数的路由。如果该值未通过，则`this.props.match.params.sessionId`中的值将未定义。

现在，提交页面只需要处理加载现有的会话提交。向组件添加功能:

```
async loadSubmission(){
  fetch(`/api/sessions/${this.props.match.params.sessionId}`, {
    headers: {
      Authorization: 'Bearer ' + await this.props.auth.getAccessToken()
    }
  })
  .then(rsp => rsp.json())
  .then(session => {
    this.setState(Object.assign({}, this.state, session));
  })
  .catch(err => {
    console.error(err);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

通过添加:
，确保它绑定到构造函数中组件的`this`上下文

```
this.loadSubmission = this.loadSubmission.bind(this); 
```

Enter fullscreen mode Exit fullscreen mode

然后通过添加生命周期方法:
确保`loadSessions()`方法只在组件完成渲染后运行

```
componentDidMount(){
  if(this.props.match.params.sessionId){
    this.loadSubmission();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，更改`handleSubmit()`方法，使其根据用户是更新还是创建会话，向 API 中的 add **或** save URL 发送消息。您可以根据是否将`sessionId`作为路由参数进行传递来确定这一点。

```
async handleSubmit(e){
  e.preventDefault();
  var sessionId = this.props.match.params.sessionId;
  var url = sessionId ? `/api/sessions/${sessionId}` : '/api/sessions';
  fetch(url, {
    body: JSON.stringify(this.state),
    cache: 'no-cache',
    headers: {
      'content-type':'application/json',
      Authorization: 'Bearer ' + await this.props.auth.getAccessToken()
    },
    method: 'POST'
  })
  .then(rsp => {
    if(rsp.status === 201 || rsp.status === 200){
      this.props.history.push('/profile');
    }
  })
  .catch(err => {
    console.error(err);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这将拉出`sessionId`,如果它存在，将更改您发布会话数据的 URL。

## 更新 ASP.NET 核心 API 中的会话

既然数据存储和身份验证的基础设施已经就绪，那么添加 API 就非常简单了。向`SessionsController.cs`添加以下动作:

```
[HttpPost("/api/sessions/{sessionId}")]
public IActionResult UpdateSession([FromBody] Session session)
{
  var savedSession = context.Sessions.SingleOrDefault(x=>x.SessionId == session.SessionId);
  if(savedSession == null){
    return NotFound();
  }
  if(savedSession.UserId != User.Claims.SingleOrDefault(u=>u.Type == "uid")?.Value)
  {
    return Unauthorized();
  }
  savedSession.Title = session.Title;
  savedSession.Abstract = session.Abstract;
  context.SaveChanges();
  return Ok(savedSession);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个动作在`HttpPost`属性中有一个指定的 URL。就像创建会话的动作一样，这从 JSON 数据中提取数据，并将其映射到一个会话对象。与 create 动作不同，它通过会话的`sessionId`来查找会话。如果没有找到，它返回一个`NotFound`动作结果。

然后，该操作通过对照会话的`UserId`属性检查当前登录用户的 ID，来查看当前登录用户是否是该会话的所有者。如果不匹配，则返回一个`Unauthorized`动作结果。

一旦通过了所有这些检查，会话就会用传入的数据进行更新，并保存回数据库。

## 从 React 中删除提交内容

最后，一个潜在的发言人可能决定他们根本不想提交那个会话来考虑。他们需要一种方法来删除该会话提交。

向会话列表添加一个`deleteSession()`方法。

```
async deleteSession(session){
  fetch(`/api/sessions/${session.sessionId}`, {
    method: 'DELETE',
    headers: {
      Authorization: 'Bearer ' + await this.props.auth.getAccessToken()
    }
  })
  .then(rsp => {
    if(rsp.status === 200){
      this.getUsersSessions();
    }
  })
  .catch(err => {
    console.error(err);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

和以前一样，您正在发送用户的访问令牌。这次到了一个`DELETE`终点。之所以在这里而不在会话组件中，是因为一旦提交被删除，列表将重新显示。要从会话组件调用它，您需要将它作为一个动作来传递。

你甚至不想把这个选项给不拥有这个会话的人，所以首先根据会话的`userId`属性检查 props 中传递的`userId`。在`SessionListing.js`组件中，更改`render()`方法，因此它显示为:

```
render() {
  return (
    <ul className="session-list">
      {this.state.sessions.map(session => 
        <Session key={session.sessionId} 
          id={session.sessionId}
          isOwner={session.userId === this.props.userId}
          delete={this.deleteSession.bind(this, session)} 
          session={session} />)}
    </ul>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以编辑`Session.js`文件了。该文件的最终版本将类似于:

```
import React from 'react';
import {Link} from 'react-router-dom';

const Session = (props) => {
  const deleteButton = props.isOwner ? 
    <input type="button" title="Delete This Session" className="btn btn-sm btn-danger" value="X" onClick={props.delete}/> : 
    null;
  return (
    <li key={props.id} className="session">
      <h2><Link to={`/submission/${props.session.sessionId}`}>{props.session.title}</Link> {deleteButton}</h2>
      <div>{props.session.abstract}} />
    </li>
  );    
}

export default Session; 
```

Enter fullscreen mode Exit fullscreen mode

现在，组件使用`isOwner`属性来决定是否显示删除按钮，删除按钮调用会话列表组件上的`delete()`方法。

## 删除 ASP.NET 核心中的会话

既然 React 应用程序可以请求删除特定的会话，那么在`SessionsController.cs`文件中添加处理该请求的动作。

```
[HttpDelete("/api/sessions/{sessionId}")]
public IActionResult Delete(int sessionId){
  var session = context.Sessions.SingleOrDefault(sess => sess.SessionId == sessionId);
  if(session == null){
    return NotFound();
  }
  if(session.UserId != User.Claims.SingleOrDefault(u=>u.Type == "uid")?.Value)
  {
    return Unauthorized();
  }
  context.Remove(session);
  context.SaveChanges();
  return Ok();
} 
```

Enter fullscreen mode Exit fullscreen mode

就像更新操作一样，您需要确保请求想要删除的会话存在，并且它属于发出请求的用户。如果是这种情况，那么在上下文中调用`Remove()`方法，将要删除的会话传递给它。最后，不要忘记调用`SaveChanges()`将上下文写回数据库，并返回一个`Ok()`动作结果。

您现在应该能够运行应用程序和注册用户，登录，创建，阅读，更新和删除会话提交！

[![Application Running Screenshot](img/bcdd4c149d4e98249e704190268e1327.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mg90aWpl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/crud-aspnet-core-react/crud-app-running-998e5cea339f4253282658c669b1b34c805b2378fa39178eedb2cd7dbef93276.png)

## 做更全栈！！

如果您喜欢用 React 构建这个 ASP.NET 核心 API，请查看 Okta 发布的更多完整的 CRUD 帖子。

*   [用 Vue.js 和节点](https://developer.okta.com/blog/2018/02/15/build-crud-app-vuejs-node)构建一个基本的 CRUD App
*   [用 Symfony 4 和 Vue 构建一个基本的 CRUD 应用](https://developer.okta.com/blog/2018/06/14/php-crud-app-symfony-vue)
*   [用 ASP.NET 内核和 Angular 构建 CRUD 应用](https://developer.okta.com/blog/2018/04/26/build-crud-app-aspnetcore-angular)

和往常一样，如果你对这篇文章有任何问题、评论或担忧，请在下面留下你的评论。对于 Okta 开发团队的其他精彩内容，请在 Twitter [@OktaDev](https://twitter.com/oktadev) 、[脸书](https://www.facebook.com/oktadevelopers/)上关注我们，并在 [YouTube](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q) 上关注我们！