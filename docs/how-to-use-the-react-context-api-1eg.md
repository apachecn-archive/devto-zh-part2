# 如何使用 React 上下文 API

> 原文：<https://dev.to/lukeghenco/how-to-use-the-react-context-api-1eg>

## 对环境做出反应

[![](../Images/4e4edc5e912b163f41956882042b3cb7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B_9zu8TW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Afxn1R_dXIB0yF88pG8M6bg.jpeg)

如果你最近听说了很多关于新的 [**React 上下文 API**](https://reactjs.org/docs/context.html) 的事情，并想了解更多，这篇文章就是为你准备的。本文将展示一些例子，说明如何使用 React 上下文来管理可共享的状态和动作，同时避免 React 应用程序中与 [**道具钻取**](https://dev.to/kentcdodds/prop-drilling-38-temp-slug-4820416) 相关的可怕问题。

[![](../Images/72fe30c903a651a6248361d716343590.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jkMeJMzo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKqjVs_Y1p26VsxGqhncD3g.png) 

<figcaption>来源:【https://www.dictionary.com/browse/context】</figcaption>

## 构建客户档案应用程序

在下面的演练中，我们将构建一个简单的帐户档案应用程序。大概是这样的:

[![](../Images/d87e748a27782d0175cbcc1db73f17e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FhOwMzgN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/348/0%2AALMcT8hm3Ql-jMNZ)

### 初始组件和文件结构

使用上面的模拟，你可以看到我们的应用程序有 3 个部分:

1.  带有主页链接和帐户用户名的导航栏
2.  显示帐户信息的详细信息组件
3.  创建新用户名或更改帐户成员级别的表单

要使用此应用程序中的代码，请运行以下命令来引导项目。

```
$ npx create-react-app account-profile-app
$ cd account-profile-app
$ yarn add react-router-dom // or npm install react-router-dom 
```

我们需要做的第一件事是布局应用程序的基础组件结构。然后，我们将把组件连接到用**上下文 API 创建的 **AccountProvider** 组件。**

设置并创建必要的文件，以形成以下文件结构:

```
| src 
 | NavigationBar.jsx
 | AccountProfile 
 index.jsx 
 AccountDetails.jsx 
 AccountUpdate.jsx 
 App.jsx 
 index.js 
```

对于导航栏，我们需要创建一个名为 **NavigationBar** 的无状态组件。这将显示指向主页和帐户资料页面的链接。

```
// NavigationBar.jsx

import React from 'react'
import { Link } from 'react-router-dom'

const NavigationBar = ({ username }) => (
  <div>
    <Link to="/">Home</Link>
    <Link to="/account/profile">{username}</Link>
  </div>
)

export default NavigationBar 
```

现在，让我们构建 **AccountDetails** 组件，它将显示帐户的用户名、加入日期和会员级别。

```
// AccountProfile/AccountDetails.jsx

import React from 'react'

const AccountDetails = ({ username, dateJoined, membershipLevel }) => (
  <div>
    <p>Username: {username}</p>
    <p>Date Joined: {dateJoined}</p>
    <p>Membership Level: {membershipLevel}</p>
  </div>
)

export default AccountDetails 
```

接下来，我们可以创建 **AccountUpdate** 组件，这是一个允许创建新用户名或更改会员级别的基本表单。该组件还需要少量的内部状态来处理变更和表单提交事件。

```
// AccountProfile/AccountUpdate.jsx

import React from 'react'

class AccountUpdate extends Component {
  state = {
    username: this.props.username,
    membershipLevel: this.props.membershipLevel
  }

  handleOnChange = ({ target: { value, name } }) => {
    this.setState({
      [name]: value
    })
  }

  render () {
    const { membershipLevel, username } = this.state

    return (
      <div>
        <form>
           <label htmlFor="username">New Username</label>
           <div>
             <input 
               type="text" 
               name="username" 
               value={usernameValue} 
               onChange={this.handleOnChange} 
             />
           </div>
           <label htmlFor="membershipLevel">Membership Level</label>
           <div>
             <select 
               value={membershipLevel} 
               name="membershipLevel"
               onChange={this.handleOnChange}
             >
               <option value="Bronze">Bronze</option>
               <option value="Silver">Silver</option>
               <option value="Gold">Gold</option>
             </select>
           </div>
           <button>Save</button>
         </form>
      </div>
    )
  }
}

export default AccountUpdate 
```

既然已经构建了初始的 **AccountUpdate** 和 **AccountInfo** 组件，我们应该将它们放在 **AccountProfile** 组件中。

```
// AccountProfile/index.jsx 

import AccountDetails from './AccountDetails'
import AccountUpdate from './AccountUpdate'

const AccountProfile = ({ account: { username, dateJoined, membershipLevel } }) => (
  <Fragment>
    <AccountDetails username={username} dateJoined={dateJoined} membershipLevel={membershipLevel} />
    <AccountUpdate username={username} membershipLevel={membershipLevel} />
  </Fragment>
)

export default AccountProfile 
```

最后，我们可以将我们创建的组件安装到包装在**路由器**组件中的**应用**组件。我们还需要创建一个**帐户**对象，它将作为帐户信息的临时状态持有者，直到我们添加了**帐户提供者**。

```
// App.jsx

import React from 'react'
import { BrowserRouter as Router, Link } from 'react-router-dom'
import NavigationBar from './NavigationBar'
import AccountProfile from './AccountProfile'

const account = {
  username: 'Crunchy Crunch',
  dateJoined: '9/1/18',
  membershipLevel: 'Silver'
}

const App = () => (
  <Router>
    <Fragment>
      <Navbar username={username} />
      <Switch>
        <Route 
          exact 
          path="/" 
          render={() => <div>Home</div>}
        />
        <Route 
          exact 
          path="/account/profile" 
          render={() => <AccountProfile account={account} />} 
        />
      </Switch>
    </Fragment>
  </Router>
)

export default App 
```

[![](../Images/0c3957df4ae78da342bc35841e62b1ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cfWVwglN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/350/1%2A9smELZjfNRokxCC4QxI_Gw.gif)

## 向我们的应用程序添加上下文

使用我们创建的初始组件，**导航条**和**账户资料**的账户数据被硬编码在**应用**组件中，然后作为道具传递给其他组件。道具的传承还没有失控，但随着我们应用程序的增长，这可能会很麻烦。

目前，我们正在将关于账户的道具从**应用**组件传递到子**导航栏**组件。我们还将道具从 **App** 组件传递到子 **AccountProfile** 组件，然后子组件将道具传递到其子组件。这就是我们 app 里传道具开始成为问题的地方，也是前面提到的**“道具演练】、**的典型案例。我们希望让我们的应用程序更加灵活，在重构过程中不必在这么多地方调整代码，以避免脆弱的代码。这就是可能使用像 **Redux** 或 **MobX** 这样的解决方案的地方，但是现在我们可以使用 **React Context** 来简化这个问题。

我们的解决方案是使用上下文 API 来设置数据提供者。这将允许我们与我们的组件共享状态，同时为我们的数据存储使用一个 [**单例设计模式**](https://www.sitepoint.com/javascript-design-patterns-singleton/) ，我们的所有组件也可以监听/订阅。

因此，对于下一步，让我们通过创建一个新的上下文对象和包含本地状态的**反应**组件来设置一个**帐户提供者**和一个**帐户消费者**。稍后，我们将把这个数据提供者与我们的组件集成起来，以获得动态数据。

```
// src/providers/AccountProvider.jsx

import React from 'react'

// Set Up The Initial Context
const AccountContext = React.createContext()

// Create an exportable consumer that can be injected into components
export const AccountConsumer = AccountContext.Consumer

// Create the provider using a traditional React.Component class 
class AccountProvider extends Component {
  state = {
    username: 'Crunchy Crunch',
    dateJoined: '9/1/18',
    membershipLevel: 'Silver'
  }

  render () {
    return (
      // value prop is where we define what values 
      // that are accessible to consumer components
      <AccountContext.Provider value={this.state}>
        {this.props.children}
      </AccountContext.Provider>
    )
  }
}

export default AccountProvider 
```

现在我们已经创建了**账户提供者&账户消费者**，我们可以在**应用**组件的顶层连接/呈现它。这将使所有子组件都可以访问 **AccountProvider** 值。我们还将删除我们的临时 **account** 对象，并将其作为道具传递给**导航栏**和 **AccountProfile** 组件。

```
// App.jsx — update

// ... previous imports
import AccountProvider from './providers/AccountProvider'

// remove const account

const App = () => (
  <AccountProvider>
    <Router>
      <Fragment>
        <NavigationBar />
        <Switch>
          <Route 
            exact 
            path="/" 
            render={() => <div>Home</div>}
          />
          <Route 
            exact 
            path="/account/profile" 
            component={AccountProfile} 
          />
        </Switch>
      </Fragment>
    </Router>
  </AccountProvider>
) 
```

让我们来谈谈目前为止我们已经完成的工作:

1.  设置我们的组件
2.  使用上下文 API 创建了一个 **AccountProvider**
3.  创建了一个 **AccountConsumer** ，子组件将使用它来访问来自 **AccountProvider** 的值
4.  将 **AccountProvider** 安装为我们的应用程序中的顶级组件
5.  移除了我们的临时账号和道具

到目前为止，一切都很好，但是我们如何让我们的组件订阅我们的 **AccountProvider** ？先用**导航条**拍一张吧。

```
// NavigationBar.jsx — Update

// ... previous imports
import { AccountConsumer } from '../providers/AccountProvider'

const NavigationBar = () => (
  <AccountConsumer>
    {({ details: { username } }) => (
      <div> 
        <Link to="/">Home</Link>
        <Link to="/account/profile">{username}</Link>
      </div>
    )}
  </AccountConsumer>
)

export default NavigationBar 
```

好吧，那是很多。让我们再回顾一下我们的成就:

1.  我们导入了**帐户消费者**
2.  我们将 **AccountConsumer** 呈现为**导航栏**的顶层组件
3.  我们定义了一个回调块，它从本地状态的 **AccountProvider** 传递用户名值
4.  我们渲染了**导航栏**组件的原始代码，该组件现在使用了由**帐户消费者**提供的新用户名属性

这使得**导航条**成为了**账户提供者**的活跃用户。如果用户名状态在 **AccountProvider、**中被更改，它现在会在**导航栏中呈现新的值。**

接下来，让我们使用相同的模式更新 **AccountDetails，**，但是传递值 username、membershipLevel 和 dateJoined。

```
// AccountProfile/AccountDetails.jsx — Update

// ... previous imports 
import { AccountConsumer } from '../providers/AccountProvider'

const AccountDetails = () => (
  <AccountConsumer>
    {({ username, dateJoined, membershipLevel }) => (
      <div>
        <p>Username: {username}</p>
        <p>Date Joined: {dateJoined}</p>
        <p>Membership Level: {membershipLevel}</p>
      </div>
    )}
  </AccountConsumer>
)

export default AccountDetails 
```

干得好！我们正在显示在我们的**导航栏& AccountDetails** 组件内的 **AccountProvider** 中定义的数据。

我们只需对 **AccountProfile** 组件进行一次调整，即可移除未使用的支柱钻孔。

```
// AccountProfile/index.jsx - Update 

import AccountDetails from './AccountDetails'
import AccountUpdate from './AccountUpdate'

const AccountProfile = () => (
  <Fragment>
    <AccountDetails />
    <AccountUpdate />
  </Fragment>
)

export default AccountProfile 
```

耶，我们在申请的中间点。您可以休息一下，阅读一下将道具从上下文提供者传递到子组件(我强烈推荐“ [React 的新上下文 API](https://medium.com/dailyjs/reacts-%EF%B8%8F-new-context-api-70c9fe01596b) ”)。继续看如何在我们的**账户提供者**中动态更新状态。

#### **动态更新提供者上下文状态**

为了完成下面的任务，我们需要在我们的 **AccountProvider** 中创建一个新函数，该函数可以更新我们订阅的组件的本地状态，以便接收。

让我们调用这个函数`updateAccount()`，然后将其绑定到状态对象。

```
// AccountProvider.jsx — update

import React from 'react'

const AccountContext = React.createContext()

export const AccountConsumer = AccountContext.Consumer

class AccountProvider extends Component {
  state = {
    username: 'Crunchy Crunch',
    dateJoined: '9/1/18',
    membershipLevel: 'Silver',
    updateAccount: updatedAccount => this.updateAccount(updatedAccount)
  }

  updateAccount = updatedAccount => {
    this.setState(prevState => ({
      ...prevState,
      ...updatedAccount
    }))
  }

  render () {
    return (
      <AccountContext.Provider value={this.state}>
        {this.props.children}
      </AccountContext.Provider>
    )
  }
}

export default AccountProvider 
```

通过在 **AccountProvider，**中新创建的代码，我们添加了:

1.  引用绑定的`updateAccount()`函数的本地状态的 updateAccount 键
2.  更新**账户提供者**的本地状态的`updateAccount()`函数
3.  将`updateAccount()`设置为可传递给订户的函数

有了这个新函数，我们现在可以更新 **AccountUpdate** 组件的表单来使用它。让我们将它放在 handleOnSubmit 函数中，并从表单中向它传递更新帐户信息。我们还应该从**账户提供者**传递用户名和会员级别。

```
// AccountProfile/AccountUpdate.jsx — update

import React from 'react'
import { AccountConsumer } from '../providers/AccountProvider'

class AccountUpdate extends Component {
  // Updates!!
  state = {
    username: this.props.username,
    membershipLevel: this.props.membershipLevel
  }

  handleOnChange = ({ target: { value, name } }) => {
    this.setState({
      [name]: value
    })
  }

  // New Code!!
  handleOnSubmit = event => {
    event.preventDefault()

    const updatedAccount = { ...this.state }

    this.props.updateAccount(updatedAccount)
  }

  // New Code!!
  // To handle resetting form upon submission success
  componentWillReceiveProps(nextProps, prevProps) {
    if(prevProps !== nextProps) {
      this.setState({
        username: nextProps.username, 
        membershipLevel: nextProps.membershipLevel
      })
    }
  }

  // Updates!!
  render () {
    const { membershipLevel, username } = this.state
    const usernameValue = username === this.props.username ? '' : username

    return (
      <div>
        <form onSubmit={this.handleSubmit}>
          <label htmlFor="username">New Username</label>
          <div>
            <input 
              type="text" 
              name="username" 
              value={usernameValue} 
              onChange={this.handleOnChange} 
            />
          </div>
          <label htmlFor="membershipLevel">Membership Level</label>
          <div>
            <select 
              value={membershipLevel} 
              name="membershipLevel"
              onChange={this.handleOnChange}
            >
              <option value="Bronze">Bronze</option>
              <option value="Silver">Silver</option>
              <option value="Gold">Gold</option>
            </select>
          </div>
          <button>Save</button>
        </form>
      </div>
    )
  }
}

// Added to connect AccountConsumer
// To pass props to AccountUpdate
// Before component initialization
// As the AccountUpdate.state requires
// The new props
const ConnectedAccountUpdate = props => (
  <AccountConsumer>
    {({ username, membershipLevel, updateAccount }) => (
      <AccountUpdate  
        {...props}
        username={username}
        membershipLevel={membershipLevel}
        updateAccount={updateAccount}
      />
    )}
  </AccountConsumer>  
)

export default ConnectedAccountUpdate 
```

瞧啊。在提交表单时，我们现在动态更新用户名和会员级别，现在在**导航栏**和**账户详情**组件中出现了新的变化。

## 结论:

我希望这篇文章能为您提供一些如何在当前项目中使用它的想法。我很享受使用这个新工具的时光，也很感激能对我的 React 代码有更多的控制。

想在一个任务驱动的团队中工作，并喜欢 React 应用程序中的合理的适当管理吗？ [**我们要招聘**](https://flatironschool.com/careers/) **！**

[![Footer top](../Images/14482ccec82c63d0e8096dc6b2c2379b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8yzEjPtS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AcN2uavSo9qa-SVEi6GAp2A.png)

**要了解更多关于熨斗学校的信息，请访问** [**网站**](http://www.flatironschool.com/) **，关注我们的** [**【脸书】**](http://www.facebook.com/flatironschool) **和** [**推特**](http://www.twitter.com/flatironschool) **，并访问我们的** [**即将举办的活动**](https://flatironschool.com/events/) **。**

**熨斗学校是** [**WeWork**](https://www.wework.com/) **家族的骄傲成员。看看我们的姐妹科技博客** [**WeWork 科技**](https://engineering.wework.com/) **和** [**制作 Meetup**](https://medium.com/making-meetup) **。**

[![Footer bottom](../Images/216338eef5099fffac8419af94ff80bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ccTTOxvY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AvDaS9kLQz7Rf42nHubFrgw.png)

* * *