# 如何从 React with Twilio 发送短信

> 原文：<https://dev.to/twilio/how-to-send-an-sms-from-react-with-twilio-18gn>

我们经常谈论从 web 应用程序发送 [SMS 消息，但是从 React 应用程序发送 SMS 消息呢？除了服务器端版本](https://www.twilio.com/docs/sms/send-messages)的[之外，还有更多东西，但这不会花我们太多时间。](https://www.twilio.com/blog/2016/04/send-text-in-javascript-node-in-30-seconds.html)

### 为什么我不能在客户端使用 REST API？

从技术上讲，您可以使用 Twilio REST API 直接从 JavaScript 客户端应用程序发送短信。但是(这是一个很大的“但是”)如果你这样做，你将会把你的 Twilio 证书暴露给任何使用你的站点的人。然后，恶意用户可能会获取这些凭据并滥用它们，从而在您的帐户中累积巨额账单。

[![An animation showing a fake hacker, with a balaclava and extra hands.](img/ebec63a6388cd5d5614586a066e6063d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VHFNtzkU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/WU8JMLBGyKsTCY1u_Qa7mPji5mpT1SqxmgsO3eCJSN77kn.width-800.gif)

*使用您的帐户凭证实时查看黑客*

为了避免这种情况，我们将创建一个后端应用程序来实现 Twilio REST API，包装您的凭证并为您发送 SMS 消息。然后，您可以从 React 应用程序调用后端并发送 SMS 消息，而无需将您的凭证分发到互联网。

### 我们的工具

对于使用 Twilio REST API 发送文本消息的应用程序，我们需要以下内容:

*   可以发送短信的 Twilio 帐户和电话号码(您可以[在此](https://www.twilio.com/try-twilio)免费注册 Twilio 帐户)
*   构建我们的 React 应用程序并运行我们的服务器(你可以用任何语言构建服务器端组件，但在本文中，我们将在 Node 中这样做，这样我们就可以全部使用 JavaScript)
*   [React 开发工具](https://github.com/facebook/react-devtools)用于您的浏览器(可选，但对查看应用程序中发生的事情非常有用

首先，下载或克隆我在[上一篇博文](https://dev.to/twilio/set-up-a-react-app-with-a-nodejs-server-proxy-2pgc)中构建的 [react-express-starter 应用程序](https://github.com/philnash/react-express-starter)。

```
git clone https://github.com/philnash/react-express-starter.git 
```

Enter fullscreen mode Exit fullscreen mode

转到目录并安装依赖项。

```
cd react-express-starternpm install 
```

Enter fullscreen mode Exit fullscreen mode

在项目目录中，创建一个名为`.env` :
的文件

```
touch .env 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以通过运行`npm run dev`来测试项目是否正在运行。该应用程序将加载到您的浏览器 localhost:3000 中。

这个 starter 应用程序被设置为在同一个项目中有一个 React 应用程序和一个 [Express](http://expressjs.com/) 应用程序，您可以同时运行这两个应用程序。如果你想知道这是如何工作的，看看[这篇博文](https://dev.to/twilio/set-up-a-react-app-with-a-nodejs-server-proxy-2pgc)。

### 构建服务器端

如前所述，我们需要从服务器进行 Twilio API 调用。我们将向 Express 服务器添加一个端点，可以从 React 应用程序调用该端点。首先安装 [Twilio Node.js 模块](https://www.npmjs.com/package/twilio)。注意:出于这个应用程序的目的，我将服务器依赖项保存为开发依赖项，以便将它们与客户端依赖项分开。

```
npm install twilio --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要用我们的 Twilio 凭证配置应用程序。从 [Twilio 控制台](https://www.twilio.com/console)收集您的 Twilio 帐户 Sid 和身份验证令牌，以及可以发送短信的 [Twilio 电话号码](https://www.twilio.com/console/phone-numbers/incoming)。将这三个输入到您之前创建的`.env`文件中，如下所示:

```
TWILIO_ACCOUNT_SID=YOUR_ACCOUNT_SID
TWILIO_AUTH_TOKEN=YOUR_AUTH_TOKEN
TWILIO_PHONE_NUMBER=YOUR_TWILIO_PHONE_NUMBER 
```

Enter fullscreen mode Exit fullscreen mode

这将[在环境](https://www.twilio.com/blog/2017/08/working-with-environment-variables-in-node-js.html)中设置您的凭证。现在，打开`server/index.js`，这样我们就可以开始编写发送消息所需的代码。在文件顶部的“其他模块要求”下，要求并使用环境中的凭证初始化 Twilio 库。

```
const express = require('express');
const bodyParser = require('body-parser');
const pino = require('express-pino-logger')();
const client = require('twilio')(
  process.env.TWILIO_ACCOUNT_SID,
  process.env.TWILIO_AUTH_TOKEN
); 
```

Enter fullscreen mode Exit fullscreen mode

我们将把数据发送到我们构建为 JSON 的端点，所以我们需要能够解析 JSON 主体。用 body parser 的 JSON 解析器配置 Express app:

```
const app = express();
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());app.use(pino); 
```

Enter fullscreen mode Exit fullscreen mode

为一个`POST`请求创建一个路由。在`/api/greeting`的路线下增加以下内容:

```
app.post('/api/messages', (req, res) => {

}); 
```

Enter fullscreen mode Exit fullscreen mode

我们也将使用 JSON 进行响应，所以将`Content-Type`头设置为`application/json`。

```
app.post('/api/messages', (req, res) => {
  res.header('Content-Type', 'application/json');

}); 
```

Enter fullscreen mode Exit fullscreen mode

然后[我们将使用之前初始化的 Twilio 客户端创建一条消息](https://www.twilio.com/docs/sms/send-messages?code-sample=code-send-an-sms-message&code-language=Node.js&code-sdk-version=3.x)。我们将使用我们的 Twilio 号作为`from`号，并从传入的请求体获得消息的`to`号和`body`。这将返回一个[承诺](https://www.twilio.com/blog/2016/10/guide-to-javascript-promises.html),当 API 请求成功时它将履行，如果失败则拒绝。无论哪种情况，我们都将返回一个 JSON 响应来告诉客户端请求是否成功。

```
app.post('/api/messages', (req, res) => {
  res.header('Content-Type', 'application/json');
  client.messages
    .create({
      from: process.env.TWILIO_PHONE_NUMBER,
      to: req.body.to,
      body: req.body.body
    })
    .then(() => {
      res.send(JSON.stringify({ success: true }));
    })
    .catch(err => {
      console.log(err);
      res.send(JSON.stringify({ success: false }));
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们在服务器上所需要的，让我们从 React 部分开始。

### 构建客户端

在客户端，我们可以将通过服务器发送 SMS 的表单完全封装在一个组件中。因此，在`src`目录中创建一个名为`SMSForm.js`的新组件，并从组件的样板文件开始:

```
import React, { Component } from 'react';

class SMSForm extends Component {

}

export default SMSForm; 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建一个用户可以填写电话号码和消息的表单。当表单提交后，它会将详细信息发送到我们的服务器端点，并将消息作为短信发送到该号码。

让我们首先为这个组件构建`render`方法:它将包括一个表单、一个电话号码输入、一个消息文本区和一个提交按钮:

```
 render() {
    return (
      <form>
        <div>
          <label htmlFor="to">To:</label>
          <input
             type="tel"
             name="to"
             id="to"
          />
        </div>
        <div>
          <label htmlFor="body">Body:</label>
          <textarea name="body" id="body"/>
        </div>
        <button type="submit">
          Send message
        </button>
      </form>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以添加一些 CSS 来设计这个表单。创建文件`src/SMSForm.css`并添加以下内容:

```
.sms-form {
  text-align: left;
  padding: 1em;
}
.sms-form label {
  display: block;
}
.sms-form input,
.sms-form textarea {
  font-size: 1em;
  width: 100%;
  box-sizing: border-box;
}
.sms-form div {
  margin-bottom: 0.5em;
}
.sms-form button {
  font-size: 1em;
  width: 100%;
}
.sms-form.error {
  outline: 2px solid #f00;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 SMSForm 组件的顶部导入 CSS:

```
import React, { Component } from 'react';
import './SMSForm.css'; 
```

Enter fullscreen mode Exit fullscreen mode

现在，将组件导入到`src/App.js`中，并将渲染方法替换为以下内容:

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import SMSForm from './SMSForm';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />

          <SMSForm />
        </header>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

用`npm run dev`启动您的应用程序，您将在页面上看到这个表单。

[![](img/af57d970ab849cd8008603255df74de0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6QXncK8x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/lQ_7J7OP9Wc8uM5lAWxNkavJ2bGd8F_i0ONhZvdHXFysVt.width-500.png)

表单还没有做任何事情，所以让我们来解决这个问题。

### 在 React 中制作交互表单

为了将 HTML 表单与组件挂钩，我们需要做一些事情:

*   在组件的状态中保持输入和文本区域的状态是最新的
*   处理提交表单和发送数据到服务器
*   如果消息发送成功，处理来自服务器的响应并清除表单，否则显示错误

我们将从在构造函数中设置一些初始状态开始。我们需要存储表单输入，表单是否正在被提交(这样我们可以禁用提交按钮)以及是否有错误。为组件创建构造函数，如下所示:

```
class SMSForm extends Component {
  constructor(props) {
    super(props);
    this.state = {
      message: {
        to: '',
        body: ''
      },
      submitting: false,
      error: false
    };
  }

  // rest of the component
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个方法来处理表单字段的变化并更新状态。我们可以创建两个方法，一个用于 input，一个用于 textarea，但是由于表单元素和状态项的名称匹配，我们可以构建一个方法来涵盖这两个方法。

```
 onHandleChange(event) {
    const name = event.target.getAttribute('name');
    this.setState({
      message: { ...this.state.message, [name]: event.target.value }
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们使用 [ES2015 的计算属性名称](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)来设置状态中的正确属性，并使用[扩展操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)来填充状态的其余部分。

我们需要将这个方法绑定到对象，以确保当我们使用它来接收事件时`this`是正确的。将以下内容添加到构造函数的底部:

```
 constructor(props) {
    super(props);
    this.state = {
      message: {
        to: '',
        body: ''
      },
      submitting: false,
      error: false
    };
    this.onHandleChange = this.onHandleChange.bind(this);
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以更新我们的渲染 JSX，使用当前状态设置表单字段的值，并使用我们的`onHandleChange`方法:
处理更新

```
 render() {
    return (
      <form>
        <div>
          <label htmlFor="to">To:</label>
          <input
            type="tel"
            name="to"
            id="to"
            value={this.state.message.to}
            onChange={this.onHandleChange}
          />
        </div>
        <div>
          <label htmlFor="body">Body:</label>
          <textarea
            name="body"
            id="body"
            value={this.state.message.body}
            onChange={this.onHandleChange}
          />
        </div>
        <button type="submit">Send message</button>
      </form>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

重新加载应用程序，您将能够更新表单字段。如果你的浏览器有 [React 开发工具](https://github.com/facebook/react-devtools)，你也能看到状态更新。

[![](img/7cdd517a9a0ddf1227cb290a0fc44b4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QJWi8lxO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/_dzcSo7MrETpFCBo-yhmOkD376ckMEWm3HHhEY8YF4CLbY.width-500.png)

现在我们需要处理表单提交。构建另一个函数`onSubmit`，首先将`submitting`状态属性更新为 true。然后使用 [`fetch` API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 向服务器发出请求。如果响应成功，则清除表格并将`submitting`设置为假。如果响应不成功，则将`submitting`设置为假，但将`error`设置为真。

```
 onSubmit(event) {
    event.preventDefault();
    this.setState({ submitting: true });
    fetch('/api/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(this.state.message)
    })
      .then(res => res.json())
      .then(data => {
        if (data.success) {
          this.setState({
            error: false,
            submitting: false,
            message: {
              to: '',
              body: ''
            }
          });
        } else {
          this.setState({
            error: true,
            submitting: false
          });
        }
      });
  } 
```

Enter fullscreen mode Exit fullscreen mode

像使用`onHandleChange`方法一样，我们也在构造函数中绑定这个方法:

```
 constructor(props) {
    super(props);
    this.state = {
      message: {
        to: '',
        body: ''
      },
      submitting: false,
      error: false
    };
    this.onHandleChange = this.onHandleChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 JSX 中，我们添加了`onSubmit`方法作为表单上的提交处理程序。如果从请求中收到一个错误，我们还将表单的类设置为“error”。当表单提交时，我们设置按钮的`disabled`属性。

```
 render() {
    return (
      <form
        onSubmit={this.onSubmit}
        className={this.state.error ? 'error sms-form' : 'sms-form'}
      >
        <div>
          <label htmlFor="to">To:</label>
          <input
            type="tel"
            name="to"
            id="to"
            value={this.state.message.to}
            onChange={this.onHandleChange}
          />
        </div>
        <div>
          <label htmlFor="body">Body:</label>
          <textarea
            name="body"
            id="body"
            value={this.state.message.body}
            onChange={this.onHandleChange}
          />
        </div>
        <button type="submit" disabled={this.state.submitting}>
          Send message
        </button>
       </form>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们需要的全部内容，因此请再次刷新应用，并输入您的手机号码和要发送的消息。提交表单，如果细节正确，您的消息将被发送，如果不正确，表单将显示状态错误。

[![Now when entering incorrect values the form shows an error and when you enter the correct values the message is sent.](img/c17c2c04b4ce69d36b4d8e51a69c2d04.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hYCU4EGH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/success.width-500.gif)

### 发送消息并保护您的凭证安全

从网络应用程序发送短信很酷。从你的 React 应用发送短信而不暴露你的凭证更酷😎。

您可以在 [GitHub repo](https://github.com/philnash/send-sms-react-twilio) 中查看这个示例应用程序的所有代码。

现在你已经有了一个可以发送短信的 React 应用程序的基础，你可以做一些改进。首先可能是更好的验证和错误消息。通过类似的设计，你可以添加[电话号码查询](https://www.twilio.com/docs/lookup/api)、[生成电话呼叫](https://www.twilio.com/docs/voice/api/call)或[实现双因素认证](https://www.twilio.com/docs/authy/api/one-time-passwords)也可以从你的 React 应用程序中实现。

我很想听听你正在用 React 开发的应用。请在下面给我留言，在 Twitter 上给我打电话 [@philnash](https://twitter.com/philnash) ，或者给我发邮件到 philnash@twilio.com 的。