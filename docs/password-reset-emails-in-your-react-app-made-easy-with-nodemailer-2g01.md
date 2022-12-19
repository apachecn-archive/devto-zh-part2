# 使用 Nodemailer 简化 React 应用程序中的密码重置电子邮件

> 原文：<https://dev.to/paigen11/password-reset-emails-in-your-react-app-made-easy-with-nodemailer-2g01>

[![Logos of React, Node.js, and Nodemailer](../Images/0538d812fa7680e108b3a0c7857d7df9.png "Logos of React, Node.js, and Nodemailer")T2】](///static/a7064890108db3d198b09d01e8a2a004/2cefc/nodemailer-hero.png)

## MERN 应用程序中的密码重置

在我真正尝试为我的 MERN 应用程序内置一个基于电子邮件的密码重置之前，我认为这将更难做到。根据我之前听到的一切，在 JavaScript 应用程序中发送电子邮件是痛苦的，但我仍然想尝试一下。

几个月来，为了磨练我的全部 JavaScript 技能，我一直在慢慢构建并添加一个用户注册服务。

首先，我用 React 前端、Express / Node.js 后端和 Docker 支持的 MySQL 数据库构建了它。我用了一个`docker-compose.yml`用一个命令启动整个 app(如果你想了解更多我使用 Docker 进行开发的情况，可以看 [**这篇博文**](https://www.paigeniedringhaus.com/blog/using-docker-docker-compose-to-improve-your-full-stack-app-development) )。

在我完成这项工作后，我使用 Passport.js 和 JSON Web token(jwt)向应用程序添加了授权。如果你好奇的话，你可以在这里 了解那个 [**的乐趣(痛苦)。这花了我一段时间——我遇到了一堆路障和障碍，多次让我止步不前。但是，一旦问题在我的大脑中扎根，我的勇气和我无法放弃它的能力占了上风，我找到了它并继续前进。**](https://www.paigeniedringhaus.com/blog/implementing-json-web-tokens-passport-js-in-a-react-app)

当我决定通过电子邮件发送密码重置链接时(就像真实的网站一样，包括我在内的用户不可避免地会忘记他们的密码)，我想我会更加痛苦。这太简单了，尽管几乎每个网站都有这个功能。但我错了。我很高兴我是。

今天，我将向你展示如何在 Nodemailer 的帮助下建立自己的密码重置系统。

* * *

## 节点邮件程序:神奇的子弹

[![Nodemailer logo](../Images/dc3ef4007432a53edaa61c7a6dbd1093.png "Nodemailer logo")T2】](///static/b637191000560597aaff4e696545c70f/8aea6/nodemailer-logo.png)

当我开始在谷歌上寻找我的密码重置功能的解决方案时，我看到了一些推荐 [Nodemailer](https://nodemailer.com/about/) 的文章。

当我访问该网站时，我读到的第一行是:

> Nodemailer 是 Node.js 应用程序的一个模块，可以轻松发送电子邮件。该项目始于 2010 年，当时没有发送电子邮件的合理选项，今天它是大多数 Node.js 用户默认采用的解决方案。—节点邮件程序

你知道吗？这不是开玩笑。易如反掌不会错太多。

当然，在我开始之前，我做了更多的调查，以确保我对一项像样的技术抱有信心，我在 [npm](https://www.npmjs.com/package/nodemailer) 和 [GitHub](https://github.com/nodemailer/nodemailer) 上看到的东西让我放心。

节点邮件程序具有:

*   来自 NPM 的每周下载超过 615，000 次，
*   Github 上超过 10，000 颗星星，
*   迄今为止发行了 206 次，
*   超过 2500 个依赖包，
*   自 2010 年以来，它一直以某种形式或时尚存在。

好吧，这似乎足够让我在自己的项目中尝试一下。

### 我的代码中 Nodemailer 的实现(前端和后端)

我的密码重置不需要任何花哨的东西，只需要:

*   向用户地址发送电子邮件的方式。
*   该电子邮件将包含一个链接，将他们重定向到我的网站上的一个受保护的页面，他们可以在那里重置密码。
*   然后他们可以使用新密码登录。
*   我还希望密码重置链接在一段时间后过期，以获得更好的安全性。

我是这样做的。

### 前端代码(客户端文件夹):发送重置邮件

我将首先从 React 代码开始，因为我必须有一个页面，用户可以在其中输入他们的电子邮件地址并发送带有重置链接的电子邮件。

[**`ForgotPassword.js`**](https://github.com/paigen11/mysql-registration-passport/blob/master/client/src/containers/ForgotPassword.js)T5】

```
/* eslint-disable no-console */
import React, { Component } from 'react';
import TextField from '@material-ui/core/TextField';
import axios from 'axios';
import {
  LinkButtons,
  SubmitButtons,
  registerButton,
  homeButton,
  forgotButton,
  inputStyle,
  HeaderBar,
} from '../components';

const title = {
  pageTitle: 'Forgot Password Screen',
};

class ForgotPassword extends Component {
  constructor() {
    super();

    this.state = {
      email: '',
      showError: false,
      messageFromServer: '',
      showNullError: false,
    };
  }

  handleChange = name => (event) => {
    this.setState({
      [name]: event.target.value,
    });
  };

  sendEmail = async (e) => {
    e.preventDefault();
    const { email } = this.state;
    if (email === '') {
      this.setState({
        showError: false,
        messageFromServer: '',
        showNullError: true,
      });
    } else {
      try {
        const response = await axios.post(
          'http://localhost:3003/forgotPassword',
          {
            email,
          },
        );
        if (response.data === 'recovery email sent') {
          this.setState({
            showError: false,
            messageFromServer: 'recovery email sent',
            showNullError: false,
          });
        }
      } catch (error) {
        console.error(error.response.data);
        if (error.response.data === 'email not in db') {
          this.setState({
            showError: true,
            messageFromServer: '',
            showNullError: false,
          });
        }
      }
    }
  };

  render() {
    const { email, messageFromServer, showNullError, showError } = this.state;

    return (
      <div>
        <HeaderBar title={title} />
        <form className="profile-form" onSubmit={this.sendEmail}>
          <TextField
            style={inputStyle}
            id="email"
            label="email"
            value={email}
            onChange={this.handleChange('email')}
            placeholder="Email Address"
          />
          <SubmitButtons
            buttonStyle={forgotButton}
            buttonText="Send Password Reset Email"
          />
        </form>
        {showNullError && (
          <div>
            <p>The email address cannot be null.</p>
          </div>
        )}
        {showError && (
          <div>
            <p>
              That email address isn't recognized. Please try again or
              register for a new account.
            </p>
            <LinkButtons
              buttonText="Register"
              buttonStyle={registerButton}
              link="/register"
            />
          </div>
        )}
        {messageFromServer === 'recovery email sent' && (
          <div>
            <h3>Password Reset Email Successfully Sent!</h3>
          </div>
        )}
        <LinkButtons buttonText="Go Home" buttonStyle={homeButton} link="/" />
      </div>
    );
  }
}

export default ForgotPassword; 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我知道这是一个很大的代码片段，但我会打破它。

> 如果你想复制/粘贴实际代码，你可以在这里 看到整个回购 [**。**](https://github.com/paigen11/mysql-registration-passport)

这里你真正应该关注的是组件的`sendEmail()`函数和`render()`方法。剩下的只是设置初始状态和变量，以及按钮和元素的样式。

**render()方法**

```
render() {
    const { email, messageFromServer, showNullError, showError } = this.state;

    return (
      <div>
        <HeaderBar title={title} />
        <form className="profile-form" onSubmit={this.sendEmail}>
          <TextField
            style={inputStyle}
            id="email"
            label="email"
            value={email}
            onChange={this.handleChange('email')}
            placeholder="Email Address"
          />
          <SubmitButtons
            buttonStyle={forgotButton}
            buttonText="Send Password Reset Email"
          />
        </form>
        {showNullError && (
          <div>
            <p>The email address cannot be null.</p>
          </div>
        )}
        {showError && (
          <div>
            <p>
              That email address isn&apos;t recognized. Please try again or
              register for a new account.
            </p>
            <LinkButtons
              buttonText="Register"
              buttonStyle={registerButton}
              link="/register"
            />
          </div>
        )}
        {messageFromServer === 'recovery email sent' && (
          <div>
            <h3>Password Reset Email Successfully Sent!</h3>
          </div>
        )}
        <LinkButtons buttonText="Go Home" buttonStyle={homeButton} link="/" />
      </div>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

注意在`render()`方法中，我有一个简单的输入框供用户输入他/她的电子邮件地址，还有一个提交按钮，当点击时会触发`this.sendEmail()`功能。除此之外，我还内置了一点错误和成功处理功能，它基于用户是否没有输入电子邮件，服务器是否回复电子邮件已成功发送，或者它不是一个可识别的地址。

**send email()函数**

```
 sendEmail = async (e) => {
    e.preventDefault();
    const { email } = this.state;
    if (email === '') {
      this.setState({
        showError: false,
        messageFromServer: '',
        showNullError: true,
      });
    } else {
      try {
        const response = await axios.post(
          'http://localhost:3003/forgotPassword',
          {
            email,
          },
        );
        if (response.data === 'recovery email sent') {
          this.setState({
            showError: false,
            messageFromServer: 'recovery email sent',
            showNullError: false,
          });
        }
      } catch (error) {
        console.error(error.response.data);
        if (error.response.data === 'email not in db') {
          this.setState({
            showError: true,
            messageFromServer: '',
            showNullError: false,
          });
        }
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

对于我所有的 HTTP 请求，我使用的是 [Axios 库](https://www.npmjs.com/package/axios)，这使得 AJAX 调用服务器变得非常容易，在我看来，甚至比内置的 fetch() web API 更容易。

当用户输入他/她的电子邮件时，我向服务器发出一个`POST`请求，然后等待响应。如果找不到电子邮件地址，我可以告诉用户他们输入错误，或者如果他们是新用户，他们可以进入注册页面并创建一个帐户，如果该地址与我的数据库中的地址匹配，他们将返回一条成功消息，说明密码重置链接已发送到他们的电子邮件地址。

现在让我们转到后端代码。

### 后端代码(API 文件夹):发送重置邮件

[**`forgotPassword.js`**](https://github.com/paigen11/mysql-registration-passport/blob/master/api/routes/forgotPassword.js)T5】

```
/* eslint-disable max-len */
/* eslint-disable no-console */
import crypto from 'crypto';
import User from '../sequelize';

require('dotenv').config();

const nodemailer = require('nodemailer');

module.exports = (app) => {
  app.post('/forgotPassword', (req, res) => {
    if (req.body.email === '') {
      res.status(400).send('email required');
    }
    console.error(req.body.email);
    User.findOne({
      where: {
        email: req.body.email,
      },
    }).then((user) => {
      if (user === null) {
        console.error('email not in database');
        res.status(403).send('email not in db');
      } else {
        const token = crypto.randomBytes(20).toString('hex');
        user.update({
          resetPasswordToken: token,
          resetPasswordExpires: Date.now() + 3600000,
        });

        const transporter = nodemailer.createTransport({
          service: 'gmail',
          auth: {
            user: `${process.env.EMAIL_ADDRESS}`,
            pass: `${process.env.EMAIL_PASSWORD}`,
          },
        });

        const mailOptions = {
          from: 'mySqlDemoEmail@gmail.com',
          to: `${user.email}`,
          subject: 'Link To Reset Password',
          text:
            'You are receiving this because you (or someone else) have requested the reset of the password for your account.\n\n'
            + 'Please click on the following link, or paste this into your browser to complete the process within one hour of receiving it:\n\n'
            + `http://localhost:3031/reset/${token}\n\n`
            + 'If you did not request this, please ignore this email and your password will remain unchanged.\n',
        };

        console.log('sending mail');

        transporter.sendMail(mailOptions, (err, response) => {
          if (err) {
            console.error('there was an error: ', err);
          } else {
            res.status(200).json('recovery email sent');
          }
        });
      }
    });
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

后端代码稍微复杂一些。这就是 Nodemailer 发挥作用的地方。

当用户使用他们输入的电子邮件地址点击后端的`forgotPassword`路线时，第一件事 [Sequelize](https://www.paigeniedringhaus.com/blog/sequelize-like-mongoose-but-for-sql) (我的 SQL ORM)就是检查该电子邮件是否存在于我的数据库中。如果它不存在，用户会得到通知，他们可能输入错误，如果它确实存在，那么一系列其他事件就会开始。

接下来的步骤没有一个是非常困难的，只是一开始把它们连接在一起有点棘手。

**1。为用户**
生成过期令牌

```
 const token = crypto.randomBytes(20).toString('hex');
        user.update({
          resetPasswordToken: token,
          resetPasswordExpires: Date.now() + 3600000,
        }); 
```

Enter fullscreen mode Exit fullscreen mode

在确认电子邮件属于数据库中的用户后，第一步是生成一个可以附加到用户帐户的令牌，并设置该令牌的有效时间限制。

Node.js 有一个名为 [Crypto](https://nodejs.org/api/crypto.html#crypto_crypto) 的内置模块，它提供加密功能，这是一种奇特的说法，我可以使用 JavaScript `crypto.randomBytes(20).toString('hex');`轻松生成唯一的哈希令牌。然后，我将新令牌保存到数据库中我的用户的概要文件中，列名为`resetPasswordToken`。我还为令牌的有效期设置了时间戳。在发送了`Date.now() + 3600000`的链接后，我把我的设为一小时有效。

**2。创建节点邮件传输**

```
 const transporter = nodemailer.createTransport({
          service: 'gmail',
          auth: {
            user: `${process.env.EMAIL_ADDRESS}`,
            pass: `${process.env.EMAIL_PASSWORD}`,
          },
        }); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我创建了 transporter，它实际上是发送密码重置电子邮件链接的帐户。

我选择使用 Gmail，因为我个人使用 Gmail，我创建了一个新的虚拟帐户来发送电子邮件。因为我不想把这个账户的凭证给任何人，所以我把凭证放在一个包含在我的`.gitignore`中的`.env`文件中，这样它就不会被提交给 GitHub 或其他任何地方。

npm 包 [dotenv](https://www.npmjs.com/package/dotenv) 用于读取文件内容，并插入电子邮件地址和密码，供 Nodemailer 的【createTransport()】函数拾取。

**3。创建邮件选项**

```
 const mailOptions = {
          from: 'mySqlDemoEmail@gmail.com',
          to: `${user.email}`,
          subject: 'Link To Reset Password',
          text:
            'You are receiving this because you (or someone else) have requested the reset of the password for your account.\n\n'
            + 'Please click on the following link, or paste this into your browser to complete the process within one hour of receiving it:\n\n'
            + `http://localhost:3031/reset/${token}\n\n`
            + 'If you did not request this, please ignore this email and your password will remain unchanged.\n',
        }; 
```

Enter fullscreen mode Exit fullscreen mode

第三步是创建用户将看到的电子邮件模板或 Nodemailer 所称的`mailOptions`(这也是使用他们从前端输入传递的经过验证的电子邮件地址的地方)。

有很多第三方库可以用来制作好看的电子邮件来搭配 Nodemailer 模块，但是我只想要一个基本的电子邮件，所以我自己做了这个。

它包含`from`电子邮件地址(对我来说是 mysqldememail[at]Gmail . com)，用户的电子邮件放在`to`框中，`subject`行是重置密码链接，而`text`是一个简单的字符串，包含一些信息和网站的 URL 重置路径，包括我之前创建的令牌，附加在末尾。这将允许我验证用户是谁，他们说，当他们点击链接，并前往该网站重置他们的密码。

**4。发送邮件**

```
 transporter.sendMail(mailOptions, (err, response) => {
          if (err) {
            console.error('there was an error: ', err);
          } else {
            res.status(200).json('recovery email sent');
          }
        }); 
```

Enter fullscreen mode Exit fullscreen mode

这个文件的最后一步实际上是把我创建的片段放在一起:即`transporter`、`mailOptions`、`token`，并使用 Nodemailer 的`sendMail()`函数。如果成功，我将得到一个 200 响应，然后用它来触发对客户端的成功调用，如果失败，我将记录错误以查看哪里出错了。

### 启用 Gmail 发送重置邮件

至少在使用 Gmail 时，在设置发送所有电子邮件的传送器电子邮件时，需要注意一个额外的问题。

为了能够从一个帐户发送电子邮件，必须禁用两步验证，并且必须将标题为“允许不太安全的应用程序”的设置切换到打开。见下面截图。要做到这一点，我去我的设置[这里](https://myaccount.google.com/lesssecureapps)，并打开它。

现在，我可以毫无问题地发送重置电子邮件。如果您遇到问题，请查看 Nodemailer 的常见问题以获得更多帮助。

[![Make Gmail less secure to practice sending reset emails](../Images/d46ed3a402b6e4f4469ffbabd15e4947.png "Make Gmail less secure to practice sending reset emails")T2】](///static/89536a04dbfcb64b2a66621911600fe7/2cefc/gmail-less-secure.png)

*这是您应该看到的屏幕，您可以在这里打开不太安全的应用程序。又多了一个使用虚拟邮箱账户而不是真正的 Gmail 账户的理由。*

### 前端代码:更新密码画面

很好，现在用户应该会在他们的收件箱里收到类似这样的重置邮件。

[![Password reset email](../Images/dd8d7c31e4affcc55163b9f4bf4bf7c2.png "Password reset email")T2】](///static/169b423e51e73dbe48b108462bc97b6c/2cefc/password-reset-email.png)

这是一封简单的电子邮件，但它做了我需要它做的事情。

如果你注意到，第三行是一个到我的网站(在本地端口 3031 上运行)的链接，到另一个名为“Reset”的页面，后面是我在步骤 1 中用 Node.js `crypto`模块生成的散列令牌。

当用户点击这个链接时，他们会被定向到应用程序中一个名为“密码重置屏幕”的新页面，只有使用有效的令牌才能访问该页面。如果令牌已过期或无效，用户将看到一个错误屏幕，其中包含返回主页或尝试发送新密码重置电子邮件的链接。

这是重置屏幕的反应代码。

[**`ResetPassword.js`**](https://github.com/paigen11/mysql-registration-passport/blob/master/client/src/containers/ResetPassword.js)T5】

```
/* eslint-disable no-console */
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import axios from 'axios';
import TextField from '@material-ui/core/TextField';

import {
  LinkButtons,
  updateButton,
  homeButton,
  loginButton,
  HeaderBar,
  forgotButton,
  inputStyle,
  SubmitButtons,
} from '../components';

const loading = {
  margin: '1em',
  fontSize: '24px',
};

const title = {
  pageTitle: 'Password Reset Screen',
};

export default class ResetPassword extends Component {
  constructor() {
    super();

    this.state = {
      username: '',
      password: '',
      updated: false,
      isLoading: true,
      error: false,
    };
  }

  async componentDidMount() {
    const {
      match: {
        params: { token },
      },
    } = this.props;
    try {
      const response = await axios.get('http://localhost:3003/reset', {
        params: {
          resetPasswordToken: token,
        },
      });
      if (response.data.message === 'password reset link a-ok') {
        this.setState({
          username: response.data.username,
          updated: false,
          isLoading: false,
          error: false,
        });
      }
    } catch (error) {
      console.log(error.response.data);
      this.setState({
        updated: false,
        isLoading: false,
        error: true,
      });
    }
  }

  handleChange = name => (event) => {
    this.setState({
      [name]: event.target.value,
    });
  };

  updatePassword = async (e) => {
    e.preventDefault();
    const { username, password } = this.state;
    const {
      match: {
        params: { token },
      },
    } = this.props;
    try {
      const response = await axios.put(
        'http://localhost:3003/updatePasswordViaEmail',
        {
          username,
          password,
          resetPasswordToken: token,
        },
      );
      if (response.data.message === 'password updated') {
        this.setState({
          updated: true,
          error: false,
        });
      } else {
        this.setState({
          updated: false,
          error: true,
        });
      }
    } catch (error) {
      console.log(error.response.data);
    }
  };

  render() {
    const {
 password, error, isLoading, updated 
} = this.state;

    if (error) {
      return (
        <div>
          <HeaderBar title={title} />
          <div style={loading}>
            <h4>Problem resetting password. Please send another reset link.</h4>
            <LinkButtons
              buttonText="Go Home"
              buttonStyle={homeButton}
              link="/"
            />
            <LinkButtons
              buttonStyle={forgotButton}
              buttonText="Forgot Password?"
              link="/forgotPassword"
            />
          </div>
        </div>
      );
    }

    if (isLoading) {
      return (
        <div>
          <HeaderBar title={title} />
          <div style={loading}>Loading User Data...</div>
        </div>
      );
    }

    return (
      <div>
        <HeaderBar title={title} />
        <form className="password-form" onSubmit={this.updatePassword}>
          <TextField
            style={inputStyle}
            id="password"
            label="password"
            onChange={this.handleChange('password')}
            value={password}
            type="password"
          />
          <SubmitButtons
            buttonStyle={updateButton}
            buttonText="Update Password"
          />
        </form> 
        {updated && (
          <div>
            <p>
              Your password has been successfully reset, please try logging in
              again.
            </p>
            <LinkButtons
              buttonStyle={loginButton}
              buttonText="Login"
              link="/login"
            />
          </div>
        )}
        <LinkButtons buttonText="Go Home" buttonStyle={homeButton} link="/" />
      </div>
    );
  }
}

ResetPassword.propTypes = {
  // eslint-disable-next-line react/require-default-props
  match: PropTypes.shape({
    params: PropTypes.shape({
      token: PropTypes.string.isRequired,
    }),
  }),
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是这个组件的三个主要部分，它们起着举足轻重的作用。

**初始 componentDidMount()生命周期方法**

```
 async componentDidMount() {
    const {
      match: {
        params: { token },
      },
    } = this.props;
    try {
      const response = await axios.get('http://localhost:3003/reset', {
        params: {
          resetPasswordToken: token,
        },
      });
      if (response.data.message === 'password reset link a-ok') {
        this.setState({
          username: response.data.username,
          updated: false,
          isLoading: false,
          error: false,
        });
      }
    } catch (error) {
      console.log(error.response.data);
      this.setState({
        updated: false,
        isLoading: false,
        error: true,
      });
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

该方法在到达页面时立即触发。它从 URL 查询参数中提取令牌，并将其传递回服务器的`reset`路由，以验证令牌是合法的。

然后，服务器要么回答“a-ok”，这个令牌是有效的并且与用户相关联，要么回答“no”，这个令牌由于某种原因不再有用。

**update password()函数**

```
 updatePassword = async (e) => {
    e.preventDefault();
    const { username, password } = this.state;
    const {
      match: {
        params: { token },
      },
    } = this.props;
    try {
      const response = await axios.put(
        'http://localhost:3003/updatePasswordViaEmail',
        {
          username,
          password,
          resetPasswordToken: token,
        },
      );
      if (response.data.message === 'password updated') {
        this.setState({
          updated: true,
          error: false,
        });
      } else {
        this.setState({
          updated: false,
          error: true,
        });
      }
    } catch (error) {
      console.log(error.response.data);
    }
  }; 
```

Enter fullscreen mode Exit fullscreen mode

这是当用户通过身份验证并被允许重置密码时将触发的函数。它还访问服务器上一个名为`updatePasswordViaEmail()`的特定路径(我这样做是因为我为用户提供了一个单独的路径来在登录应用程序时更新他们的密码)，一旦更新的密码被保存到数据库，一个成功消息响应将被发送回客户端。

**render()方法**

```
 render() {
    const { password, error, isLoading, updated } = this.state;

    if (error) {
      return (
        <div>
          <HeaderBar title={title} />
          <div style={loading}>
            <h4>Problem resetting password. Please send another reset link.</h4>
            <LinkButtons
              buttonText="Go Home"
              buttonStyle={homeButton}
              link="/"
            />
            <LinkButtons
              buttonStyle={forgotButton}
              buttonText="Forgot Password?"
              link="/forgotPassword"
            />
          </div>
        </div>
      );
    }

    if (isLoading) {
      return (
        <div>
          <HeaderBar title={title} />
          <div style={loading}>Loading User Data...</div>
        </div>
      );
    }

    return (
      <div>
        <HeaderBar title={title} />
        <form className="password-form" onSubmit={this.updatePassword}>
          <TextField
            style={inputStyle}
            id="password"
            label="password"
            onChange={this.handleChange('password')}
            value={password}
            type="password"
          />
          <SubmitButtons
            buttonStyle={updateButton}
            buttonText="Update Password"
          />
        </form> 
        {updated && (
          <div>
            <p>
              Your password has been successfully reset, please try logging in again.
            </p>
            <LinkButtons
              buttonStyle={loginButton}
              buttonText="Login"
              link="/login"
            />
          </div>
        )}
        <LinkButtons buttonText="Go Home" buttonStyle={homeButton} link="/" />
      </div>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个组件的最后一部分是`render()`方法。最初，在验证令牌的有效性时，会显示`loading`消息。

如果链接以某种方式无效，屏幕上会显示`error`消息，其中包含返回主屏幕或忘记密码页面的链接。

如果用户被授权重置他们的密码，他们将获得附加有`updatePassword()`功能的新密码输入，一旦服务器成功更新密码，布尔型的`updated`将被设置为真，同时显示`Your password has been successfully reset...`消息和一个登录按钮。

### 后端代码:重置密码&更新密码

好的，我现在在最后冲刺阶段。这里是您需要的服务器端的最后两条路线。这些对应于我刚才在客户端 React `ResetPassword.js`组件中走过的两个方法。

[**`resetPassword.js`**](https://github.com/paigen11/mysql-registration-passport/blob/master/api/routes/resetPassword.js)T5】

```
/* eslint-disable no-console */
/* eslint-disable max-len */
import Sequelize from 'sequelize';
import User from '../sequelize';

// eslint-disable-next-line prefer-destructuring
const Op = Sequelize.Op;

module.exports = (app) => {
  app.get('/reset', (req, res) => {
    User.findOne({
      where: {
        resetPasswordToken: req.query.resetPasswordToken,
        resetPasswordExpires: {
          [Op.gt]: Date.now(),
        },
      },
    }).then((user) => {
      if (user == null) {
        console.error('password reset link is invalid or has expired');
        res.status(403).send('password reset link is invalid or has expired');
      } else {
        res.status(200).send({
          username: user.username,
          message: 'password reset link a-ok',
        });
      }
    });
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是在客户端的`componentDidMount()`生命周期方法中调用的路线。它检查从链接的查询参数和日期时间戳传递的`resetPasswordToken`,以确保一切正常。

你会注意到`resetPasswordExpires`参数有一个奇怪的`$gt: Date.now()`参数。这是一个[操作符别名比较器](http://docs.sequelizejs.com/manual/tutorial/querying.html#operators-aliases)，它[序列](http://docs.sequelizejs.com/)允许我使用，所有的`$gt:`代表“大于”它所比较的任何东西。在这种情况下，它将当前时间与发送重置密码电子邮件时保存到数据库中的过期时间戳进行比较，以确保在电子邮件发送后不到一小时就重置了密码。

只要这两个参数对该用户都有效，就会向客户端发送一个成功的响应，用户就可以继续重置密码。

[**`updatePasswordViaEmail.js`**](https://github.com/paigen11/mysql-registration-passport/blob/master/api/routes/updatePasswordViaEmail.js)T5】

```
/* eslint-disable no-console */
import bcrypt from 'bcrypt';
import Sequelize from 'sequelize';
import User from '../sequelize';

// eslint-disable-next-line prefer-destructuring
const Op = Sequelize.Op;

const BCRYPT_SALT_ROUNDS = 12;

module.exports = app => {
  app.put('/updatePasswordViaEmail', (req, res) => {
    User.findOne({
      where: {
        username: req.body.username,
        resetPasswordToken: req.body.resetPasswordToken,
        resetPasswordExpires: {
          [Op.gt]: Date.now(),
        },
      },
    }).then(user => {
      if (user == null) {
        console.error('password reset link is invalid or has expired');
        res.status(403).send('password reset link is invalid or has expired');
      } else if (user != null) {
        console.log('user exists in db');
        bcrypt
          .hash(req.body.password, BCRYPT_SALT_ROUNDS)
          .then(hashedPassword => {
            user.update({
              password: hashedPassword,
              resetPasswordToken: null,
              resetPasswordExpires: null,
            });
          })
          .then(() => {
            console.log('password updated');
            res.status(200).send({ message: 'password updated' });
          });
      } else {
        console.error('no user exists in db to update');
        res.status(401).json('no user exists in db to update');
      }
    });
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是当用户提交他/她的密码进行更新时调用的第二个途径。

我再次在数据库中找到用户，并再次检查`resetPasswordToken`是否也存在并且有效(从上面的重置路径将`username`传递回客户端，并保持在应用程序的状态，直到更新函数被调用)， 我使用我的`bcrypt`模块散列新密码(就像我的 Passport.js 中间件在一个新用户最初被写入数据库时所做的那样)，用新散列更新数据库中那个用户的`password`，并将`resetPasswordToken`和`resetPasswordExpires`列都设置回 null，所以同一个链接不能被使用多次。

一旦完成，服务器就向客户机发回一个带有成功消息的 200 响应。

您已经通过电子邮件成功重置了用户密码。不太难。

* * *

## 结论

乍一看，通过电子邮件链接重置用户密码似乎有点令人生畏。但是 Nodemailer 有助于简化一个主要因素(电子邮件)。完成后，只需在服务器端发送几条路由，在客户端输入内容，就可以为用户更新密码。

过几周再来看看，我会写关于使用 Puppeteer 和 headless Chrome 进行端到端测试或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

感谢您的阅读，我希望这能让您了解如何使用 Nodemailer 为 MERN 应用程序发送密码重置电子邮件。

* * *

## 参考文献&进一步资源

*   [节点邮件程序](https://nodemailer.com/about/)
*   节点邮件程序， [GitHub](https://github.com/nodemailer/nodemailer)
*   Nodemailer， [npm](https://www.npmjs.com/package/nodemailer)
*   [带有节点邮件程序报告的 MERN 应用程序](https://github.com/paigen11/mysql-registration-passport)