# 使用 React、GraphQL 和用户身份验证构建健康跟踪应用程序

> 原文：<https://dev.to/oktadev/build-a-health-tracking-app-with-react-graphql-and-user-authentication-90f>

我想你会喜欢我将要给你讲的故事。我将向您展示如何使用 Vesper framework、TypeORM 和 MySQL 构建一个 GraphQL API。这些是节点框架，我将使用语言的 TypeScript。对于客户端，我将使用 React、reactstrap 和 Apollo 客户端与 API 对话。一旦你有了这个工作环境，并添加了安全的用户认证，我相信你会爱上这种体验！

为什么关注安全认证？嗯，除了我在 Okta 工作的事实，我想我们都同意几乎每个应用程序都依赖于一个安全的身份管理系统。对于大多数开发 React 应用程序的开发人员来说，他们需要在开发自己的身份验证/授权和插入 Okta 这样的服务之间做出选择。在我开始构建 React 应用程序之前，我想告诉你一些关于 Okta 的事情，以及为什么我认为它是所有 JavaScript 开发人员的优秀解决方案。

## 什么是 Okta？

简而言之，我们让[身份管理](https://developer.okta.com/product/user-management/)变得比你过去习惯的更容易、更安全、更可扩展。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

你被卖了吗？[注册一个永久免费的开发者帐户](https://developer.okta.com/signup/)，完成后，请回来，以便我们可以了解更多有关在 React 中构建安全应用的信息！

## 为什么要做健康追踪 App？

2014 年 9 月底至 10 月中旬，我进行了为期 21 天的戒糖，在此期间，我停止吃糖，开始定期锻炼，并戒酒。我有十多年的高血压，当时正在服用降压药。在戒毒的第一周，我用完了降压药。因为新的处方需要去看医生，所以我决定等到戒毒后再去拿。三周后，我不仅瘦了 15 斤，血压也正常了！

在我开始戒毒之前，我想出了一个 21 点系统来看看我每周的健康程度。它的规则很简单:出于以下原因，你每天最多可以获得三个积分:

1.  如果你吃得健康，你得一分。否则为零。
2.  如果你锻炼，你就得到一分。
3.  不喝酒，得一分。

我惊讶地发现，在我使用这个系统的第一周，我得了 8 分。在戒毒期间，我第一周得了 16 分，第二周 20 分，第三周 21 分。在排毒之前，我认为健康饮食意味着吃除了快餐以外的任何东西。戒毒之后，我意识到健康饮食对我来说意味着不吃糖。我也是精酿啤酒的忠实爱好者，所以我修改了酒精规则，允许每天喝两杯更健康的酒精饮料(比如灰狗酒或红酒)。

我的目标是每周挣 15 分。我发现如果我吃得更多，我很可能会减肥，血压也会变好。如果少于 15 天，我就有生病的危险。我从 2014 年 9 月开始这样跟踪自己的健康状况。我体重减轻了，血压恢复并保持在正常水平。从 20 岁出头开始，我的血压就一直不好，所以这改变了我的生活。

我建立了 [21 点健康](https://www.21-points.com/#/about)来追踪我的健康状况。我觉得重新创建一个小应用程序会很有趣，只需跟踪每日积分。

## 用 TypeORM、GraphQL 和 Vesper 构建 API

TypeORM 是一个漂亮的 ORM(对象关系映射器)框架，可以在大多数 JavaScript 平台上运行，包括 Node、浏览器、Cordova、React Native 和 Electron。它深受 Hibernate、Doctrine 和 Entity 框架的影响。全局安装 TypeORM，开始创建您的 API。

```
npm i -g typeorm@0.2.7 
```

Enter fullscreen mode Exit fullscreen mode

创建一个目录来存放 React 客户端和 GraphQL API。

```
mkdir health-tracker
cd health-tracker 
```

Enter fullscreen mode Exit fullscreen mode

使用以下命令用 MySQL 创建一个新项目:

```
typeorm init --name graphql-api --database mysql 
```

Enter fullscreen mode Exit fullscreen mode

编辑`graphql-api/ormconfig.json`自定义用户名、密码和数据库。

```
{
    ...
    "username": "health",
    "password": "pointstest",
    "database": "healthpoints",
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

**提示:**要查看针对 MySQL 执行的查询，请将该文件中的“logging”值改为“all”。许多其他的[日志选项](https://github.com/typeorm/typeorm/blob/master/docs/logging.md)也是可用的。

### 安装 MySQL

如果您还没有安装 MySQL，请安装它。在 Ubuntu 上，可以使用`sudo apt-get install mysql-server`。在 macOS 上，可以用家酿和`brew install mysql`。对于 Windows，可以使用 [MySQL 安装程序](https://dev.mysql.com/downloads/installer/)。

一旦安装了 MySQL 并配置了 root 密码，登录并创建一个`healthpoints`数据库。

```
mysql -u root -p
create database healthpoints;
use healthpoints;
grant all privileges on *.* to 'health'@'localhost' identified by 'points'; 
```

Enter fullscreen mode Exit fullscreen mode

在终端窗口中导航到您的`graphql-api`项目，安装项目的依赖项，然后启动它以确保您可以连接到 MySQL。

```
cd graphql-api
npm i
npm start 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到以下输出:

```
Inserting a new user into the database...
Saved a new user with id: 1
Loading users from the database...
Loaded users: [User { id: 1, firstName: 'Timber', lastName: 'Saw', age: 25 }]
Here you can setup and run express/koa/any other framework. 
```

Enter fullscreen mode Exit fullscreen mode

### 安装 Vesper 集成 TypeORM 和 GraphQL

[Vesper](http://vesper-framework.com/) 是一个集成了 TypeORM 和 GraphQL 的节点框架。要安装它，请使用良好的 ol' npm。

```
npm i vesper@0.1.9 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候创建一些 GraphQL 模型(定义数据的样子)和一些控制器(解释如何与数据交互)。

创建`graphql-api/src/schema/model/Points.graphql` :

```
type Points {
  id: Int
  date: Date
  exercise: Int
  diet: Int
  alcohol: Int
  notes: String
  user: User
} 
```

Enter fullscreen mode Exit fullscreen mode

创建`graphql-api/src/schema/model/User.graphql` :

```
type User {
  id: String
  firstName: String
  lastName: String
  points: [Points]
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，用查询和突变创建一个【T0:】T1

```
type Query {
  points: [Points]
  pointsGet(id: Int): Points
  users: [User]
}

type Mutation {
  pointsSave(id: Int, date: Date, exercise: Int, diet: Int, alcohol: Int, notes: String): Points
  pointsDelete(id: Int): Boolean
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的数据已经有了 GraphQL 元数据，可以创建将由 TypeORM 管理的实体。将`src/entity/User.ts`更改为以下代码，允许将积分与用户相关联。

```
import { Column, Entity, OneToMany, PrimaryColumn } from 'typeorm';
import { Points } from './Points';

@Entity()
export class User {

  @PrimaryColumn()
  id: string;

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @OneToMany(() => Points, points => points.user)
  points: Points[];
} 
```

Enter fullscreen mode Exit fullscreen mode

在同一个`src/entity`目录中，用下面的代码创建一个`Points.ts`类。

```
import { Entity, PrimaryGeneratedColumn, Column, ManyToOne } from 'typeorm';
import { User } from './User';

@Entity()
export class Points {

  @PrimaryGeneratedColumn()
  id: number;

  @Column({ type: 'timestamp', default: () => 'CURRENT_TIMESTAMP'})
  date: Date;

  @Column()
  exercise: number;

  @Column()
  diet: number;

  @Column()
  alcohol: number;

  @Column()
  notes: string;

  @ManyToOne(() => User, user => user.points, { cascade: ["insert"] })
  user: User|null;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意上面`@ManyToOne`注释上的`cascade: ["insert"]`选项。如果实体上有用户，此选项将自动插入用户。创建`src/controller/PointsController.ts`来处理来自 GraphQL 查询和变异的数据转换。

```
import { Controller, Mutation, Query } from 'vesper';
import { EntityManager } from 'typeorm';
import { Points } from '../entity/Points';

@Controller()
export class PointsController {

  constructor(private entityManager: EntityManager) {
  }

  // serves "points: [Points]" requests
  @Query()
  points() {
    return this.entityManager.find(Points);
  }

  // serves "pointsGet(id: Int): Points" requests
  @Query()
  pointsGet({id}) {
    return this.entityManager.findOne(Points, id);
  }

  // serves "pointsSave(id: Int, date: Date, exercise: Int, diet: Int, alcohol: Int, notes: String): Points" requests
  @Mutation()
  pointsSave(args) {
    const points = this.entityManager.create(Points, args);
    return this.entityManager.save(Points, points);
  }

  // serves "pointsDelete(id: Int): Boolean" requests
  @Mutation()
  async pointsDelete({id}) {
    await this.entityManager.remove(Points, {id: id});
    return true;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

改变`src/index.ts`使用 Vesper 的`bootstrap()`来配置一切。

```
import { bootstrap } from 'vesper';
import { PointsController } from './controller/PointsController';
import { Points } from './entity/Points';
import { User } from './entity/User';

bootstrap({
  port: 4000,
  controllers: [
    PointsController
  ],
  entities: [
    Points,
    User
  ],
  schemas: [
    __dirname + '/schema/**/*.graphql'
  ],
  cors: true
}).then(() => {
  console.log('Your app is up and running on http://localhost:4000\. ' +
    'You can use playground in development mode on http://localhost:4000/playground');
}).catch(error => {
  console.error(error.stack ? error.stack : error);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码告诉 Vesper 注册控制器、实体、GraphQL 模式，在端口 4000 上运行，并启用 CORS(跨源资源共享)。

使用`npm start`启动 API 并导航到[http://localhost:4000/playground](http://localhost:4000/playground)。在左窗格中，输入以下突变，然后按播放按钮。您可以尝试键入下面的代码，以便体验 GraphQL 提供的代码补全功能。

```
mutation {
  pointsSave(exercise:1, diet:1, alcohol:1, notes:"Hello World") {
    id
    date
    exercise
    diet
    alcohol
    notes
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你的结果应该和我的相似。

[![GraphQL Playground](../Images/127359c326fcd75dee9a8c251631fe91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pWOMQZxa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/react-graphql-api/graphql-playground-900546fc63871b4bc398ff1dc266f3f4199c9727654a808c4a440d30c6bd5705.png)

您可以单击右边的“SCHEMA”选项卡来查看可用的查询和变化。很圆滑，是吧？！

您可以使用下面的`points`查询来验证数据在您的数据库中。

```
query {
  points {id date exercise diet notes}
} 
```

Enter fullscreen mode Exit fullscreen mode

### 确定日期

您可能会注意到，从`pointsSave`和`points`查询返回的日期的格式对于 JavaScript 客户机来说可能难以理解。你可以解决这个问题，安装 [graphql-iso-date](https://www.npmjs.com/package/graphql-iso-date) 。

```
npm i graphql-iso-date@3.5.0 
```

Enter fullscreen mode Exit fullscreen mode

然后，在`src/index.ts`中添加一个导入，并为各种日期类型配置自定义解析器。这个例子只使用了`Date`，但是知道其他选项是有帮助的。

```
import { GraphQLDate, GraphQLDateTime, GraphQLTime } from 'graphql-iso-date';

bootstrap({
  ...
  // https://github.com/vesper-framework/vesper/issues/4
  customResolvers: {
    Date: GraphQLDate,
    Time: GraphQLTime,
    DateTime: GraphQLDateTime
  },
  ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在运行`points`查询将返回一个更加客户端友好的结果。

```
{
  "data": {
    "points": [
      {
        "id": 1,
        "date": "2018-06-04",
        "exercise": 1,
        "diet": 1,
        "notes": "Hello World"
      }
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你已经用 GraphQL 和 TypeScript 在大约 20 分钟内编写了一个 API。多酷啊。！尽管如此，仍有工作要做。在接下来的几节中，您将为这个 API 创建一个 React 客户机，并添加使用 OIDC 的身份验证。添加身份验证将使您能够获取用户的信息，并将用户与其积分相关联。

## 开始使用 React

开始使用 React 的最快方法之一是使用 [Create React App](https://github.com/facebook/create-react-app) 。使用以下命令安装最新版本。

```
npm i -g create-react-app@1.1.4 
```

Enter fullscreen mode Exit fullscreen mode

导航到创建 GraphQL API 的目录，并创建一个 React 客户端。

```
cd health-tracker
create-react-app react-client 
```

Enter fullscreen mode Exit fullscreen mode

安装将 [Apollo 客户端](https://www.apollographql.com/docs/react/essentials/get-started.html)与 React 集成所需的依赖项，以及引导和【React 陷阱。

```
npm i apollo-boost@0.1.7 react-apollo@2.1.4 graphql-tag@2.9.2 graphql@0.13.2 
```

Enter fullscreen mode Exit fullscreen mode

### 为您的 API 配置 Apollo 客户端

打开`react-client/src/App.js`并从`apollo-boost`导入`ApolloClient`并将端点添加到您的 GraphQL API 中。

```
import ApolloClient from 'apollo-boost';

const client = new ApolloClient({
  uri: "http://localhost:4000/graphql"
}); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！只需三行代码，您的应用程序就可以开始获取数据了。你可以通过从`graphql-tag`导入`gql`函数来证明。这将解析您的查询字符串，并将其转换为查询文档。

```
import gql from 'graphql-tag';

class App extends Component {

  componentDidMount() {
    client.query({
      query: gql`
        {
          points {
            id date exercise diet alcohol notes
          }
        }
      `
    })
    .then(result => console.log(result));
  }
...
} 
```

Enter fullscreen mode Exit fullscreen mode

确保打开浏览器的开发人员工具，以便在进行此更改后可以看到数据。您可以修改`console.log()`来使用`this.setState({points: results.data.points})`，但是您必须在构造函数中初始化默认状态。但是有一个更简单的方法:你可以使用来自`react-apollo`的`ApolloProvider`和`Query`组件！

下面是使用这些组件的`react-client/src/App.js`的修改版本。

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import ApolloClient from 'apollo-boost';
import gql from 'graphql-tag';
import { ApolloProvider, Query } from 'react-apollo';
const client = new ApolloClient({
  uri: "http://localhost:4000/graphql"
});

class App extends Component {

  render() {
    return (
      <ApolloProvider client={client}>
        <div className="App">
          <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <h1 className="App-title">Welcome to React</h1>
          </header>
          <p className="App-intro">
            To get started, edit <code>src/App.js</code> and save to reload.
          </p>
          <Query query={gql`
            {
              points {id date exercise diet alcohol notes}
            }
          `}>
            {({loading, error, data}) => {
              if (loading) return <p>Loading...</p>;
              if (error) return <p>Error: {error}</p>;
              return data.points.map(p => {
                return <div key={p.id}>
                  <p>Date: {p.date}</p>
                  <p>Points: {p.exercise + p.diet + p.alcohol}</p>
                  <p>Notes: {p.notes}</p>
                </div>
              })
            }}
          </Query>
        </div>
      </ApolloProvider>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

您已经构建了一个 GraphQL API 和一个与之对话的 React UI——非常好！然而，还有更多的事情要做。在接下来的小节中，我将向您展示如何添加身份验证来进行反应，用 Vesper 验证 jwt，以及向 UI 添加 CRUD 功能。由于您之前编写的变化，CRUD 功能已经存在于 API 中。

## 添加与 OpenID 连接的 React 认证

您需要配置 React 来使用 Okta 进行身份验证。为此，您需要在 Okta 创建一个 OIDC 应用程序。

登录您的 Okta 开发者账户(如果您没有账户，请注册，然后导航至**应用** > **添加应用**。点击**单页应用**，点击下一个的**，给应用取一个你会记住的名字。将`localhost:8080`的所有实例更改为`localhost:3000`并点击**完成**。你的设置应该类似于下面的截图。**

[![OIDC App Settings](../Images/35d89d61e32e6be390ab7e1f14cfeacf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I_IUz9Yi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/react-graphql-api/oidc-app-settings-2d66eb0c762f4d3b73a21158b61bd0ecb4a8f5441677d504f6f1ba02e37c3c70.png)

Okta 的 React SDK 允许您将 OIDC 集成到 React 应用程序中。要安装，请运行以下命令:

```
npm i @okta/okta-react@1.0.2 react-router-dom@4.2.2 
```

Enter fullscreen mode Exit fullscreen mode

Okta 的 React SDK 依赖于 [react-router](https://www.npmjs.com/package/react-router) ，因此有安装`react-router-dom`的理由。在`client/src/App.tsx`中配置路由是一种常见的做法，所以用下面设置 Okta 认证的 JavaScript 替换它的代码。

```
import React, { Component } from 'react';
import { BrowserRouter as Router, Route } from 'react-router-dom';
import { ImplicitCallback, SecureRoute, Security } from '@okta/okta-react';
import Home from './Home';
import Login from './Login';
import Points from './Points';

function onAuthRequired({history}) {
  history.push('/login');
}

class App extends Component {
  render() {
    return (
      <Router>
        <Security issuer='https://{yourOktaDomain}.com/oauth2/default'
                  client_id='{yourClientId}'
                  redirect_uri={window.location.origin + '/implicit/callback'}
                  onAuthRequired={onAuthRequired}>
          <Route path='/' exact={true} component={Home}/>
          <SecureRoute path='/points' component={Points}/>
          <Route path='/login' render={() => <Login baseUrl='https://{yourOktaDomain}.com'/>}/>
          <Route path='/implicit/callback' component={ImplicitCallback}/>
        </Security>
      </Router>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

确保替换上面代码中的`{yourOktaDomain}`和`{yourClientId}`。你的 Okta 域名应该类似于`dev-12345.oktapreview`。确保 URL 中不会出现两个`.com`值！

`App.js`中的代码引用了两个尚不存在的组件:`Home`、`Login`和`Points`。用下面的代码创建`src/Home.js`。该组件呈现默认路线，提供登录按钮，并在您登录后链接到您的积分和注销。

```
import React, { Component } from 'react';
import { withAuth } from '@okta/okta-react';
import { Button, Container } from 'reactstrap';
import AppNavbar from './AppNavbar';
import { Link } from 'react-router-dom';

export default withAuth(class Home extends Component {
  constructor(props) {
    super(props);
    this.state = {authenticated: null, userinfo: null, isOpen: false};
    this.checkAuthentication = this.checkAuthentication.bind(this);
    this.checkAuthentication();
    this.login = this.login.bind(this);
    this.logout = this.logout.bind(this);
  }

  async checkAuthentication() {
    const authenticated = await this.props.auth.isAuthenticated();
    if (authenticated !== this.state.authenticated) {
      if (authenticated && !this.state.userinfo) {
        const userinfo = await this.props.auth.getUser();
        this.setState({authenticated, userinfo});
      } else {
        this.setState({authenticated});
      }
    }
  }

  async componentDidMount() {
    this.checkAuthentication();
  }

  async componentDidUpdate() {
    this.checkAuthentication();
  }

  async login() {
    this.props.auth.login('/');
  }

  async logout() {
    this.props.auth.logout('/');
    this.setState({authenticated: null, userinfo: null});
  }

  render() {
    if (this.state.authenticated === null) return null;
    const button = this.state.authenticated ?
        <div>
          <Button color="link"><Link to="/points">Manage Points</Link></Button><br/>
          <Button color="link" onClick={this.logout}>Logout</Button>
        </div>:
      <Button color="primary" onClick={this.login}>Login</Button>;

    const message = this.state.userinfo ?
      <p>Hello, {this.state.userinfo.given_name}!</p> :
      <p>Please log in to manage your points.</p>;

    return (
      <div>
        <AppNavbar/>
        <Container fluid>
          {message}
          {button}
        </Container>
      </div>
    );
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

该组件使用 reactstrap 中的`<Container/>`和`<Button/>`。安装 reactstrap，这样一切都可以编译。它依赖于 Bootstrap，所以也包括它。

```
npm i reactstrap@6.1.0 bootstrap@4.1.1 
```

Enter fullscreen mode Exit fullscreen mode

在`src/index.js`中添加 Bootstrap 的 CSS 文件作为导入。

```
import 'bootstrap/dist/css/bootstrap.min.css'; 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到在`Home`组件的`render()`方法中有一个`<AppNavbar/>`。创建`src/AppNavbar.js`，这样你就可以在组件之间使用一个公共的头。

```
import React, { Component } from 'react';
import { Collapse, Nav, Navbar, NavbarBrand, NavbarToggler, NavItem, NavLink } from 'reactstrap';
import { Link } from 'react-router-dom';

export default class AppNavbar extends Component {
  constructor(props) {
    super(props);
    this.state = {isOpen: false};
    this.toggle = this.toggle.bind(this);
  }

  toggle() {
    this.setState({
      isOpen: !this.state.isOpen
    });
  }

  render() {
    return <Navbar color="success" dark expand="md">
      <NavbarBrand tag={Link} to="/">Home</NavbarBrand>
      <NavbarToggler onClick={this.toggle}/>
      <Collapse isOpen={this.state.isOpen} navbar>
        <Nav className="ml-auto" navbar>
          <NavItem>
            <NavLink
              href="https://twitter.com/oktadev">@oktadev</NavLink>
          </NavItem>
          <NavItem>
            <NavLink href="https://github.com/oktadeveloper/okta-react-graphql-example/">GitHub</NavLink>
          </NavItem>
        </Nav>
      </Collapse>
    </Navbar>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我将嵌入 [Okta 的登录小部件](https://github.com/okta/okta-signin-widget)。另一种选择是重定向到 Okta 并使用托管登录页面。使用 npm 安装登录小部件。

```
npm i @okta/okta-signin-widget@2.9.0 
```

Enter fullscreen mode Exit fullscreen mode

创建`src/Login.js`并向其中添加以下代码。

```
import React, { Component } from 'react';
import { Redirect } from 'react-router-dom';
import OktaSignInWidget from './OktaSignInWidget';
import { withAuth } from '@okta/okta-react';

export default withAuth(class Login extends Component {
  constructor(props) {
    super(props);
    this.onSuccess = this.onSuccess.bind(this);
    this.onError = this.onError.bind(this);
    this.state = {
      authenticated: null
    };
    this.checkAuthentication();
  }

  async checkAuthentication() {
    const authenticated = await this.props.auth.isAuthenticated();
    if (authenticated !== this.state.authenticated) {
      this.setState({authenticated});
    }
  }

  componentDidUpdate() {
    this.checkAuthentication();
  }

  onSuccess(res) {
    return this.props.auth.redirect({
      sessionToken: res.session.token
    });
  }

  onError(err) {
    console.log('error logging in', err);
  }

  render() {
    if (this.state.authenticated === null) return null;
    return this.state.authenticated ?
      <Redirect to={{pathname: '/'}}/> :
      <OktaSignInWidget
        baseUrl={this.props.baseUrl}
        onSuccess={this.onSuccess}
        onError={this.onError}/>;
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

`Login`组件有一个对`OktaSignInWidget`的引用。创建`src/OktaSignInWidget.js` :

```
import React, {Component} from 'react';
import ReactDOM from 'react-dom';
import OktaSignIn from '@okta/okta-signin-widget';
import '@okta/okta-signin-widget/dist/css/okta-sign-in.min.css';
import '@okta/okta-signin-widget/dist/css/okta-theme.css';
import './App.css';

export default class OktaSignInWidget extends Component {
  componentDidMount() {
    const el = ReactDOM.findDOMNode(this);
    this.widget = new OktaSignIn({
      baseUrl: this.props.baseUrl
    });
    this.widget.renderEl({el}, this.props.onSuccess, this.props.onError);
  }

  componentWillUnmount() {
    this.widget.remove();
  }

  render() {
    return <div/>;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

创建`src/Points.js`来呈现 API 中的点列表。

```
import React, { Component } from 'react';
import { ApolloClient } from 'apollo-client';
import { createHttpLink } from 'apollo-link-http';
import { setContext } from 'apollo-link-context';
import { InMemoryCache } from 'apollo-cache-inmemory';
import gql from 'graphql-tag';
import { withAuth } from '@okta/okta-react';
import AppNavbar from './AppNavbar';
import { Alert, Button, Container, Table } from 'reactstrap';
import PointsModal from './PointsModal';

export const httpLink = createHttpLink({
  uri: 'http://localhost:4000/graphql'
});

export default withAuth(class Points extends Component {
  client;

  constructor(props) {
    super(props);
    this.state = {points: [], error: null};

    this.refresh = this.refresh.bind(this);
    this.remove = this.remove.bind(this);
  }

  refresh(item) {
    let existing = this.state.points.filter(p => p.id === item.id);
    let points = [...this.state.points];
    if (existing.length === 0) {
      points.push(item);
      this.setState({points});
    } else {
      this.state.points.forEach((p, idx) => {
        if (p.id === item.id) {
          points[idx] = item;
          this.setState({points});
        }
      })
    }
  }

  remove(item, index) {
    const deletePoints = gql`mutation pointsDelete($id: Int) { pointsDelete(id: $id) }`;

    this.client.mutate({
      mutation: deletePoints,
      variables: {id: item.id}
    }).then(result => {
      if (result.data.pointsDelete) {
        let updatedPoints = [...this.state.points].filter(i => i.id !== item.id);
        this.setState({points: updatedPoints});
      }
    });
  }

  componentDidMount() {
    const authLink = setContext(async (_, {headers}) => {
      const token = await this.props.auth.getAccessToken();
      const user = await this.props.auth.getUser();

      // return the headers to the context so httpLink can read them
      return {
        headers: {
          ...headers,
          authorization: token ? `Bearer ${token}` : '',
          'x-forwarded-user': user ? JSON.stringify(user) : ''
        }
      }
    });

    this.client = new ApolloClient({
      link: authLink.concat(httpLink),
      cache: new InMemoryCache(),
      connectToDevTools: true
    });

    this.client.query({
      query: gql`
        {
          points {
              id,
              user {
                  id,
                  lastName
              }
              date,
              alcohol,
              exercise,
              diet,
              notes
          }
        }`
    }).then(result => {
      this.setState({points: result.data.points});
    }).catch(error => {
      this.setState({error: <Alert color="danger">Failure to communicate with API.</Alert>});
    });
  }

  render() {
    const {points, error} = this.state;
    const pointsList = points.map(p => {
      const total = p.exercise + p.diet + p.alcohol;
      return <tr key={p.id}>
        <td style={{whiteSpace: 'nowrap'}}><PointsModal item={p} callback={this.refresh}/></td>
        <td className={total <= 1 ? 'text-danger' : 'text-success'}>{total}</td>
        <td>{p.notes}</td>
        <td><Button size="sm" color="danger" onClick={() => this.remove(p)}>Delete</Button></td>
      </tr>
    });

    return (
      <div>
        <AppNavbar/>
        <Container fluid>
          {error}
          <h3>Your Points</h3>
          <Table>
            <thead>
            <tr>
              <th width="10%">Date</th>
              <th width="10%">Points</th>
              <th>Notes</th>
              <th width="10%">Actions</th>
            </tr>
            </thead>
            <tbody>
            {pointsList}
            </tbody>
          </Table>
          <PointsModal callback={this.refresh}/>
        </Container>
      </div>
    );
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码从`refresh()`和`remove()`方法开始，我一会儿会讲到。重要的部分发生在`componentDidMount()`，访问令牌被添加到一个`Authorization`报头中，用户信息被填充到一个`x-forwarded-user`报头中。用该信息创建一个`ApolloClient`，添加一个缓存，并打开`connectToDevTools`标志。这对于使用 [Apollo 客户端开发工具](https://github.com/apollographql/apollo-client-devtools)进行调试非常有用。

```
componentDidMount() {
  const authLink = setContext(async (_, {headers}) => {
    const token = await this.props.auth.getAccessToken();

    // return the headers to the context so httpLink can read them
    return {
      headers: {
        ...headers,
        authorization: token ? `Bearer ${token}` : '',
        'x-forwarded-user': user ? JSON.stringify(user) : ''
      }
    }
  });

  this.client = new ApolloClient({
    link: authLink.concat(httpLink),
    cache: new InMemoryCache(),
    connectToDevTools: true
  });

  // this.client.query(...);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 Apollo 客户端进行身份验证需要一些新的依赖项。现在安装这些。

```
npm apollo-link-context@1.0.8 apollo-link-http@1.5.4 
```

Enter fullscreen mode Exit fullscreen mode

在页面的 JSX 中，有一个删除按钮，调用`Points`中的`remove()`方法。还有`<pointsmodal></pointsmodal>`的成分。这是每个项目的参考，以及在底部。您会注意到这两个方法都引用了更新列表的`refresh()`方法。

```
<PointsModal item={p} callback={this.refresh}/>
<PointsModal callback={this.refresh}/> 
```

Enter fullscreen mode Exit fullscreen mode

该组件呈现一个编辑组件的链接，或者在没有设置`item`时呈现一个添加按钮。

创建`src/PointsModal.js`并向其中添加以下代码。

```
import React, { Component } from 'react';
import { Button, Form, FormGroup, Input, Label, Modal, ModalBody, ModalFooter, ModalHeader } from 'reactstrap';
import { withAuth } from '@okta/okta-react';
import { httpLink } from './Points';
import { ApolloClient } from 'apollo-client';
import { setContext } from 'apollo-link-context';
import { InMemoryCache } from 'apollo-cache-inmemory';
import gql from 'graphql-tag';
import { Link } from 'react-router-dom';

export default withAuth(class PointsModal extends Component {
  client;
  emptyItem = {
    date: (new Date()).toISOString().split('T')[0],
    exercise: 1,
    diet: 1,
    alcohol: 1,
    notes: ''
  };

  constructor(props) {
    super(props);
    this.state = {
      modal: false,
      item: this.emptyItem
    };

    this.toggle = this.toggle.bind(this);
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  componentDidMount() {
    if (this.props.item) {
      this.setState({item: this.props.item})
    }

    const authLink = setContext(async (_, {headers}) => {
      const token = await this.props.auth.getAccessToken();
      const user = await this.props.auth.getUser();

      // return the headers to the context so httpLink can read them
      return {
        headers: {
          ...headers,
          authorization: token ? `Bearer ${token}` : '',
          'x-forwarded-user': JSON.stringify(user)
        }
      }
    });

    this.client = new ApolloClient({
      link: authLink.concat(httpLink),
      cache: new InMemoryCache()
    });
  }

  toggle() {
    if (this.state.modal && !this.state.item.id) {
      this.setState({item: this.emptyItem});
    }
    this.setState({modal: !this.state.modal});
  }

  render() {
    const {item} = this.state;
    const opener = item.id ? <Link onClick={this.toggle} to="#">{this.props.item.date}</Link> :
      <Button color="primary" onClick={this.toggle}>Add Points</Button>;

    return (
      <div>
        {opener}
        <Modal isOpen={this.state.modal} toggle={this.toggle}>
          <ModalHeader toggle={this.toggle}>{(item.id ? 'Edit' : 'Add')} Points</ModalHeader>
          <ModalBody>
            <Form onSubmit={this.handleSubmit}>
              <FormGroup>
                <Label for="date">Date</Label>
                <Input type="date" name="date" id="date" value={item.date}
                       onChange={this.handleChange}/>
              </FormGroup>
              <FormGroup check>
                <Label check>
                  <Input type="checkbox" name="exercise" id="exercise" checked={item.exercise}
                         onChange={this.handleChange}/>{' '}
                  Did you exercise?
                </Label>
              </FormGroup>
              <FormGroup check>
                <Label check>
                  <Input type="checkbox" name="diet" id="diet" checked={item.diet}
                         onChange={this.handleChange}/>{' '}
                  Did you eat well?
                </Label>
              </FormGroup>
              <FormGroup check>
                <Label check>
                  <Input type="checkbox" name="alcohol" id="alcohol" checked={item.alcohol}
                         onChange={this.handleChange}/>{' '}
                  Did you drink responsibly?
                </Label>
              </FormGroup>
              <FormGroup>
                <Label for="notes">Notes</Label>
                <Input type="textarea" name="notes" id="notes" value={item.notes}
                       onChange={this.handleChange}/>
              </FormGroup>
            </Form>
          </ModalBody>
          <ModalFooter>
            <Button color="primary" onClick={this.handleSubmit}>Save</Button>{' '}
            <Button color="secondary" onClick={this.toggle}>Cancel</Button>
          </ModalFooter>
        </Modal>
      </div>
    )
  };

  handleChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? (target.checked ? 1 : 0) : target.value;
    const name = target.name;
    let item = {...this.state.item};
    item[name] = value;
    this.setState({item});
  }

  handleSubmit(event) {
    event.preventDefault();
    const {item} = this.state;

    const updatePoints = gql`
      mutation pointsSave($id: Int, $date: Date, $exercise: Int, $diet: Int, $alcohol: Int, $notes: String) {
        pointsSave(id: $id, date: $date, exercise: $exercise, diet: $diet, alcohol: $alcohol, notes: $notes) {
          id date
        }
      }`;

    this.client.mutate({
      mutation: updatePoints,
      variables: {
        id: item.id,
        date: item.date,
        exercise: item.exercise,
        diet: item.diet,
        alcohol: item.alcohol,
        notes: item.notes
      }
    }).then(result => {
      let newItem = {...item};
      newItem.id = result.data.pointsSave.id;
      this.props.callback(newItem);
      this.toggle();
    });
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

确保您的 GraphQL 后端已经启动，然后用`npm start`启动 React 前端。文本挤压顶部导航栏，所以通过在`src/index.css`中添加一个规则来添加一些填充。

```
.container-fluid {
  padding-top: 10px;
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到`Home`组件和一个登录按钮。

[![Home Screen](../Images/780bbc1490d91baf1c3003dc349ba515.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0UoKGA38--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/react-graphql-api/home-5a481cae39d18a3043a6e03d9cf84e5be03976661f16a12adcf73bdf796d3e7c.png)

点击**登录**，系统会提示您输入 Okta 凭证。

[![Login Screen](../Images/3d05fc9787b8773580663bf78dd7e6a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tiwSkbp0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/react-graphql-api/login-2ff927fda8724a1654ceb4481d71b31f60f9576182556757ea422724e92c772c.png)

然后你就登录了！

[![Home Screen with Authenticated User](../Images/cf06d9ea2fa1ed494147c622a9598165.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a0FKeiJI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/react-graphql-api/home-authenticated-599ac4b0aa92fd60139800e4878cd5a82f4370e5a2e145a0d569b809f76ef113.png)

点击**管理积分**查看积分列表。

[![Your Points Screen](../Images/80a52b05dabd4e2bab522d1e62f09500.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cO-p1G9A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/react-graphql-api/points-list-f6f1b6417df2a28fdd2a8e151dd5814ad760ac579564e04e1aba8569af0b6e96.png)

看到一切都正常运转很酷，不是吗？！:D

您的 React 前端是安全的，但是您的 API 仍然是开放的。让我们解决这个问题。

### 从 jwt 获取用户信息

在终端窗口中导航到您的`graphql-api`项目，并安装 Okta 的 JWT 验证器。

```
npm i @okta/jwt-verifier@0.0.12 
```

Enter fullscreen mode Exit fullscreen mode

创建`graphql-api/src/CurrentUser.ts`来保存当前用户的信息。

```
export class CurrentUser {
  constructor(public id: string, public firstName: string, public lastName: string) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

在`graphql-api/src/index.ts`中导入`OktaJwtVerifier`和`CurrentUser`，并配置 JWT 验证器使用你的 OIDC 应用的设置。

```
import * as OktaJwtVerifier from '@okta/jwt-verifier';
import { CurrentUser } from './CurrentUser';

const oktaJwtVerifier = new OktaJwtVerifier({
  clientId: '{yourClientId},
  issuer: 'https://{yourOktaDomain}.com/oauth2/default'
}); 
```

Enter fullscreen mode Exit fullscreen mode

在引导配置中，定义`setupContainer`需要一个`authorization`头，并从`x-forwarded-user`头设置当前用户。

```
bootstrap({
  …
  cors: true,
  setupContainer: async (container, action) => {
    const request = action.request;
    // require every request to have an authorization header
    if (!request.headers.authorization) {
      throw Error('Authorization header is required!');
    }
    let parts = request.headers.authorization.trim().split(' ');
    let accessToken = parts.pop();
    await oktaJwtVerifier.verifyAccessToken(accessToken)
      .then(async jwt => {
        const user = JSON.parse(request.headers['x-forwarded-user'].toString());
        const currentUser = new CurrentUser(jwt.claims.uid, user.given_name, user.family_name);
        container.set(CurrentUser, currentUser);
      })
      .catch(error => {
        throw Error('JWT Validation failed!');
      })
  }
  ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

修改`graphql-api/src/controller/PointsController.ts`以将`CurrentUser`作为依赖注入。当你在那里时，调整`points()`方法以按用户 ID 过滤，并修改`pointsSave()`以在保存时设置用户。

```
import { Controller, Mutation, Query } from 'vesper';
import { EntityManager } from 'typeorm';
import { Points } from '../entity/Points';
import { User } from '../entity/User';
import { CurrentUser } from '../CurrentUser';

@Controller()
export class PointsController {

  constructor(private entityManager: EntityManager, private currentUser: CurrentUser) {
  }

  // serves "points: [Points]" requests
  @Query()
  points() {
    return this.entityManager.getRepository(Points).createQueryBuilder("points")
      .innerJoin("points.user", "user", "user.id = :id", { id: this.currentUser.id })
      .getMany();
  }

  // serves "pointsGet(id: Int): Points" requests
  @Query()
  pointsGet({id}) {
    return this.entityManager.findOne(Points, id);
  }

  // serves "pointsSave(id: Int, date: Date, exercise: Int, diet: Int, alcohol: Int, notes: String): Points" requests
  @Mutation()
  pointsSave(args) {
    // add current user to points saved
    if (this.currentUser) {
      const user = new User();
      user.id = this.currentUser.id;
      user.firstName = this.currentUser.firstName;
      user.lastName = this.currentUser.lastName;
      args.user = user;
    }

    const points = this.entityManager.create(Points, args);
    return this.entityManager.save(Points, points);
  }

  // serves "pointsDelete(id: Int): Boolean" requests
  @Mutation()
  async pointsDelete({id}) {
    await this.entityManager.remove(Points, {id: id});
    return true;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

重启 API，您应该可以开始比赛了！

[![Add Points Modal](../Images/823426c4213dcd76df10e1de3dfbb1dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pQZj0mBN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/react-graphql-api/modal-add-b6d3e16a6e8b2664db74bb037745286b5226368692a3e2783c6421d1a147fe0f.png)

[![Edit Points Modal](../Images/ef589a4cd332b81fd756d8f59c7f78cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7vH89kbZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/react-graphql-api/modal-edit-0b8914c491e88f64ca9009f0b9390c3c23f50986776df772505f8c408d6f82b8.png)

### 源代码

你可以在 https://github.com/oktadeveloper/okta-react-graphql-example 找到这篇文章的源代码。

## 了解关于 React、节点和用户认证的更多信息

本文展示了如何用 GraphQL、TypeORM 和 Node/Vesper 构建一个安全的 React 应用程序。我希望你喜欢这次经历！

在 Okta，我们关心的是让 React 和 Node 的认证容易实现。我们有几篇关于这个主题的博文，还有文档！我鼓励您查看以下链接:

*   [用 Node、React 和 Okta 建立用户注册](https://scotch.io/tutorials/add-user-registration-to-your-site-with-node-react-and-okta)
*   [在 15 分钟内构建带有用户认证的 React 应用](https://developer.okta.com/blog/2017/03/30/react-okta-sign-in-widget)
*   [构建 React 原生应用，并通过 OAuth 2.0 认证](https://scotch.io/tutorials/build-a-react-native-app-and-authenticate-with-oauth-20)
*   [将 Okta 认证添加到您的 React 应用程序中](https://developer.okta.com/code/react/okta_react)
*   [用 Vue.js 和节点](https://developer.okta.com/blog/2018/02/15/build-crud-app-vuejs-node)构建一个基本的 CRUD App

我希望你有一个用 React 和 GraphQL 构建应用程序的极好体验。如果你有任何问题，请[在 Twitter](https://twitter.com/mraible) 上联系我，或者在 [@oktadev](https://twitter.com/oktadev) 上联系我的整个海扁王团队。我们的 DMs 是完全开放的！:)