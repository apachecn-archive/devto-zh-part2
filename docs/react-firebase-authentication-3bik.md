# React Firebase 认证

> 原文：<https://dev.to/mahmoudelmahdi/react-firebase-authentication-3bik>

我们将在 React 和 Firebase 身份验证 SDK 中构建一个简单的身份验证和安全应用程序。用户将能够**创建账户**、**登录**和**退出**。我们还将确保某些路径(私人页面)的安全，并保护其仅由经过身份验证的用户使用。希望对你有帮助！

### 应用安装&设置

首先，我们将创建一个应用程序，它将由脸书官方的 React 样板文件 [create-react-app](https://github.com/facebook/create-react-app) 引导。

> 运行`npm i -g create-react-app`将它安装在您的本地机器上

```
# Creating an App
  create-react-app react-firebase-auth
# Change directory
  cd react-firebase-auth
# Additional packages to install
  yarn add firebase react-router-dom react-props 
```

Enter fullscreen mode Exit fullscreen mode

现在已经生成了初始项目结构，并且成功安装了所有依赖项。让我们来看看我们的项目层次和它的文件夹结构，如下所示:

```
# Make sub-directories under src/ path
  mkdir -p src/{components,firebase,shared}
# Move App component under src/components
  mv src/App.js src/components
# Create desired files to work with
  touch src/components/{AppProvider,Navbar,FlashMessage,Login,Signup}.js
  touch src/firebase/{firebase,index,auth,config}.js
  touch src/shared/Form.js 
```

Enter fullscreen mode Exit fullscreen mode

我们需要通过命令行`cd src/ && ls * -r`
列出所有创建的文件和子目录，以确保一切都井然有序

```
# terminal

react-firebase-auth % cd src/ && ls * -r
components:
App.js   AppProvider.js  FlashMessage.js Login.js  Navbar.js  Signup.js

firebase:
auth.js  config.js   firebase.js index.js

shared:
Form.js 
```

Enter fullscreen mode Exit fullscreen mode

## 火基

我们不打算深入研究 Firebase 本身。
如果你不熟悉 Firebase，请务必查看他们的指南
如何[将 Firebase 添加到你的 JavaScript 项目中](https://firebase.google.com/docs/web/setup)

### 火基配置

```
// src/firebase/config.js

const devConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "AUTH_DOMAIN",
  databaseURL: "DATABASE_URL",
  projectId: "PROJECT_ID",
  storageBucket: "STORAGE_BUCKET",
  messagingSenderId: "MESSAGING_SENDER_ID"
};

const prodConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "AUTH_DOMAIN",
  databaseURL: "DATABASE_URL",
  projectId: "PROJECT_ID",
  storageBucket: "STORAGE_BUCKET",
  messagingSenderId: "MESSAGING_SENDER_ID"
};

export {
  devConfig,
  prodConfig
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置细分

*   用于开发环境的 devConfig
*   用于生产环境的 **prodConfig**

📌为您项目准备一个预定义设置的配置模板文件总是好的(*，如上图*所示)，以避免将敏感数据推送到存储库中。您或您的团队中的任何一个人以后都可以使用正确的文件扩展名来制作该模板的副本。示例(*基于这篇文章*):创建一个文件`firebase.config`打开你的`.gitignore`和**添加** `app/config.js`，然后运行`cp app/firebase.config app/config.js`复制那个配置模板。

### Firebase 初始化

```
// src/firebase/firebase.js

import * as firebase from 'firebase';
import { devConfig } from './config';

!firebase.apps.length && firebase.initializeApp(devConfig);

const auth = firebase.auth();

export {
  auth
} 
```

Enter fullscreen mode Exit fullscreen mode

### 认证模块

```
// src/firebase/auth.js

import { auth } from './firebase';

/**
 * Create user session
 * @param {string} action - createUser, signIn
 * @param {string} email 
 * @param {string} password 
 */
const userSession = (action, email, password) => auth[`${action}WithEmailAndPassword`](email, password);

/**
 * Destroy current user session
 */
const logout = () => auth.signOut();

export {
  userSession,
  logout
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 认证模块故障

*   一个接受三个参数动作的函数:决定用户是否创建一个帐户或登录，电子邮件和密码
*   **注销**销毁当前用户会话，并将用户从系统中注销

### Firebase 模块

```
// src/firebase/index.js

import * as auth from './auth';
import * as firebase from './firebase';

export {
  auth,
  firebase
} 
```

Enter fullscreen mode Exit fullscreen mode

## 组件

### 提供商组件

```
// src/components/AppProvider.js

import React, {
  Component,
  createContext
} from 'react';
import { firebase } from '../firebase';
export const {
  Provider,
  Consumer
} = createContext();

class AppProvider extends Component {
  state = {
    currentUser: AppProvider.defaultProps.currentUser,
    message: AppProvider.defaultProps.message
  }

  componentDidMount() {
    firebase.auth.onAuthStateChanged(user => user && this.setState({
      currentUser: user
    }))
  }

  render() {
    return (
      <Provider value={{
        state: this.state,
        destroySession: () => this.setState({ 
          currentUser: AppProvider.defaultProps.currentUser 
        }),
        setMessage: message => this.setState({ message }),
        clearMessage: () => this.setState({ 
          message: AppProvider.defaultProps.message 
        })
      }}>
        {this.props.children}
      </Provider>
    )
  }
}

AppProvider.defaultProps = {
  currentUser: null,
  message: null
}

export default AppProvider; 
```

Enter fullscreen mode Exit fullscreen mode

#### AppProvider 细分

AppProvider 是一个 React 组件，它提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递 props，并允许**消费者**订阅*上下文变化*。

*   组件挂载后，我们检查用户是否存在。

### 导航条组件

```
// src/components/Navbar.js

import React from 'react';
import {
  Link,
  withRouter
} from 'react-router-dom';
import { auth } from '../firebase';
import { Consumer } from './AppProvider';

const Navbar = props => {
  const handleLogout = context => {
    auth.logout();
    context.destroySession();
    props.history.push('/signedOut');
  };

  return <Consumer>
    {({ state, ...context }) => (
      state.currentUser ?
        <ul>
          <li><Link to="/dashboard">Dashboard</Link></li>
          <li><a onClick={() => handleLogout(context)}>Logout</a></li>
        </ul>
        :
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/login">Login</Link></li>
          <li><Link to="/signup">Create Account</Link></li>
        </ul>
    )}
  </Consumer> };

export default withRouter(Navbar); 
```

Enter fullscreen mode Exit fullscreen mode

#### 导航条细分

Navbar 组件按如下方式处理 UI 逻辑:

1.  如果系统以用户身份登录，我们将显示仪表板(受保护的页面)和注销按钮，该按钮将用户踢出并重定向到`/signedOut`页面。
2.  如果没有找到用户，那么我们显示主页，登录和创建和帐户链接。

### FlashMessage 组件

```
// src/components/FlashMessage.js

import React from 'react';
import { Consumer } from '../components/AppProvider';

const FlashMessage = () => <Consumer>
  {({ state, ...context }) => state.message && <small className="flash-message">
    {state.message}
    <button type="button" onClick={() => context.clearMessage()}>Ok</button>
  </small>} </Consumer>; 
export default FlashMessage; 
```

Enter fullscreen mode Exit fullscreen mode

#### FlashMessage 故障

FlashMessage 是一个由订阅上下文变化的**消费者**包装的无状态组件。当出现问题时(如表单验证、服务器错误等),它会显示出来...).FlashMessage 有“确定”按钮，可以清除和关闭/隐藏它。

### 形成构件

```
// src/shared/Form.js

import React, {
  Component,
  createRef
} from 'react';
import PropTypes from 'prop-types';
import { auth } from '../firebase';

class Form extends Component {
  constructor(props) {
    super(props);

    this.email = createRef();
    this.password = createRef();
    this.handleSuccess = this.handleSuccess.bind(this);
    this.handleErrors = this.handleErrors.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSuccess() {
    this.resetForm();
    this.props.onSuccess && this.props.onSuccess();
  }

  handleErrors(reason) {
    this.props.onError && this.props.onError(reason);
  }

  handleSubmit(event) {
    event.preventDefault();
    const {
      email,
      password,
      props: { action }
    } = this;

    auth.userSession(
      action,
      email.current.value,
      password.current.value
    ).then(this.handleSuccess).catch(this.handleErrors);
  }

  resetForm() {
    if (!this.email.current || !this.password.current) { return }
    const { email, password } = Form.defaultProps;
    this.email.current.value = email;
    this.password.current.value = password;
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <h1>{this.props.title}</h1>
        <input
          name="name"
          type="email"
          ref={this.email}
        />
        <input
          name="password"
          type="password"
          autoComplete="none"
          ref={this.password}
        />
        <button type="submit">Submit</button>
      </form>
    )
  }
}

Form.propTypes = {
  title: PropTypes.string.isRequired,
  action: PropTypes.string.isRequired,
  onSuccess: PropTypes.func,
  onError: PropTypes.func
}

Form.defaultProps = {
  errors: '',
  email: '',
  password: ''
}

export default Form; 
```

Enter fullscreen mode Exit fullscreen mode

#### 表格细分

*   两个**电子邮件，密码**创建一个引用`createRef()`，我们稍后通过引用属性将它附加到 React 元素。
*   **handleSuccess** 方法执行`resetForm`方法，并从给定的道具中回调函数(如果找到的话！).
*   **handleErrors** 方法执行来自给定道具的回调函数(如果找到任何！)以*的理由*。
*   **的 handleSubmit** 方法阻止了默认的`form`行为，并执行`auth.userSession`来创建和帐号或登录一个用户。

### 登录组件

```
// src/components/Login.js

import React from 'react';
import { withRouter } from 'react-router-dom';
import Form from '../shared/Form';
import { Consumer } from './AppProvider';

const Login = props => <Consumer>
  {({ state, ...context }) => (
    <Form
      action="signIn"
      title="Login"
      onSuccess={() => props.history.push('/dashboard')}
      onError={({ message }) => context.setMessage(`Login failed: ${message}`)}
    />
  )}
</Consumer>; 
export default withRouter(Login); 
```

Enter fullscreen mode Exit fullscreen mode

#### 登录明细

登录是一个由**消费者**包装的无状态组件，订阅上下文变化。如果成功登录，用户将被重定向到一个受保护的页面(仪表板)，否则将弹出错误消息。

### 注册组件

```
// src/components/Signup.js

import React from 'react';
import { withRouter } from 'react-router-dom';
import Form from '../shared/Form';
import { auth } from '../firebase';
import { Consumer } from './AppProvider';

const Signup = props => <Consumer>
  {({ state, ...context }) => (
    <Form
      action="createUser"
      title="Create account"
      onSuccess={() => auth.logout().then(() => {
        context.destroySession();
        context.clearMessage();
        props.history.push('/accountCreated');
      })}
      onError={({ message }) => context.setMessage(`Error occured: ${message}`)}
    />
  )}
</Consumer>; 
export default withRouter(Signup); 
```

Enter fullscreen mode Exit fullscreen mode

#### 签约明细

注册是一个由**消费者**包装的无状态组件，订阅上下文变化。默认情况下，一旦成功创建帐户，Firebase 会自动让用户登录。我改变了这个实现，让用户在创建帐户后手动登录。一旦`onSuccess`回调触发，我们将用户注销，并重定向到`/accountCreated`页面，该页面带有自定义消息和一个“前进到仪表板”链接以登录。如果帐户创建失败，将弹出错误消息。

### App 组件(容器)

```
// src/components/App.js

import React, {
  Component,
  Fragment
} from 'react';
import {
  BrowserRouter as Router,
  Route,
  Link
} from 'react-router-dom';

import AppProvider, {
  Consumer
} from './AppProvider';
import Login from './Login';
import Signup from './Signup';

import Navbar from '../shared/Navbar';
import FlashMessage from '../shared/FlashMessage';

class App extends Component {
  render() {
    return (
      <AppProvider>
        <Router>
          <Fragment>
            <Navbar />
            <FlashMessage />
            <Route exact path="/" component={() => 
              <h1 className="content">Welcome, Home!</h1>} />
            <Route exact path="/login" component={() => <Login />} />
            <Route exact path="/signup" component={() => <Signup />} />
            <Router exact path="/dashboard" component={() => <Consumer>
              {
                ({ state }) => state.currentUser ?
                  <h1 className="content">Protected dashboard!</h1> :
                  <div className="content">
                    <h1>Access denied.</h1>
                    <p>You are not authorized to access this page.</p>
                  </div>
              }
            </Consumer>} />
            <Route exact path="/signedOut" component={() => 
              <h1 className="content">You're now signed out.</h1>} />
            <Route exact path="/accountCreated" component={() => 
              <h1 className="content">Account created. <Link to="/login">
              Proceed to Dashboard</Link></h1>} />
          </Fragment>
        </Router>
      </AppProvider>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

#### App(容器)细分

这里非常简单！由 AppProvider 包装的导航组件路由器通过组件树传递数据。`/dashboard`路由组件有一个受保护的内容(页面),该内容仅提供给经过验证的用户，没有用户登录。我们显示**拒绝访问**消息，而不是我们的私有内容/页面。

## 演示

点击查看[演示-gif](https://goo.gl/rNV9bS)

* * *

**欢迎反馈**如果您有任何建议或修改，请随时给我留言/评论。