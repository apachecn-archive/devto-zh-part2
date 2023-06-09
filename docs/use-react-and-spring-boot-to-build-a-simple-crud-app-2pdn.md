# 使用 React 和 Spring Boot 构建一个简单的 CRUD 应用程序

> 原文：<https://dev.to/oktadev/use-react-and-spring-boot-to-build-a-simple-crud-app-2pdn>

React 旨在使创建交互式用户界面变得轻松。它的状态管理是高效的，只在数据改变时更新组件。组件逻辑是用 JavaScript 编写的，这意味着您可以将状态置于 DOM 之外，并创建封装的组件。

开发人员喜欢 CRUD(创建、读取、更新和删除)应用程序，因为它们展示了创建应用程序时所需的许多基本功能。一旦你在一个应用程序中完成了 CRUD 的基础，大部分的客户端-服务器管道就完成了，你可以继续实现必要的业务逻辑。

今天，我将向你展示如何在 React 中用 Spring Boot 创建一个基本的 CRUD 应用程序。你可能还记得我去年为 Angular 写的一篇类似的文章:[用 Angular 5.0 和 Spring Boot 2.0](https://developer.okta.com/blog/2017/12/04/basic-crud-angular-and-spring-boot) 构建一个基本的 CRUD App。该教程使用 OAuth 2.0 的隐式流和我们的 [Okta Angular SDK](https://www.npmjs.com/package/@okta/okta-angular) 。在本教程中，我将使用 OAuth 2.0 授权代码流，并将 React 应用程序打包到 Spring Boot 生产应用程序中。同时，我将向您展示如何保持 React 在本地开发的高效工作流。

你需要安装 [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 、 [Node.js 8](https://nodejs.org/) 和 [Yarn](https://yarnpkg.com/en/docs/install) 来完成本教程。您可以使用 npm 来代替 Yarn，但是您需要将 Yarn 语法翻译成 npm。

## 用 Spring Boot 2.0 创建一个 API App

我经常在世界各地的会议和用户组上发言。我最喜欢演讲的用户组是 Java 用户组(JUGs)。我做了将近 20 年的 Java 开发人员，我热爱 Java 社区。我的一个好朋友詹姆斯·沃德说，做一次 JUG 巡回赛是他过去最喜欢的开发者倡议活动之一。我最近接受了他的建议，把海外会议换成了美国的 JUG 会议。

我为什么要告诉你这些？因为我认为今天创建一个“JUG Tours”应用程序会很有趣，它允许你创建/编辑/删除 JUG，以及查看即将举行的活动。

首先，导航到 [start.spring.io](https://start.spring.io) 并进行以下选择:

*   **组:** `com.okta.developer`
*   **神器:**T0】
*   **所属** : `JPA`，`H2`，`Web`，`Lombok`

[![Spring Initializr](img/3313241a3848e50c2d5a098969a79d66.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s5ck43RF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/spring-initializr-80a20f3f8df2b210ff974b7a22369ced888ccc703bc390ae287f637614bb8583.png)

[https://developer . okta . com/assets/blog/spring-boot-2-react/spring-initializ r-80 a 20 F3 F8 df2 b 210 ff 974 b7a 22369 ced 888 CCC 703 BC 390 AE 287 f 637614 bb 8583 . png](https://developer.okta.com/assets/blog/spring-boot-2-react/spring-initializr-80a20f3f8df2b210ff974b7a22369ced888ccc703bc390ae287f637614bb8583.png)

点击**生成项目**，下载后展开`jugtours.zip`，在自己喜欢的 IDE 中打开项目。

**提示:**如果您使用 IntelliJ IDEA 或 Spring 工具套件，您也可以在创建新项目时使用 Spring Initializr。

### 添加一个 JPA 域模型

您需要做的第一件事是创建一个保存数据的域模型。在高层次上，有一个代表罐子的`Group`，一个与`Group`有多对一关系的`Event`，以及一个与`Group`有一对多关系的`User`。

创建一个`src/main/java/com/okta/developer/jugtours/model`目录并在其中创建一个`Group.java`类。

```
package com.okta.developer.jugtours.model;

import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;

import javax.persistence.*;
import java.util.Set;

@Data
@NoArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name = "user_group")
public class Group {

    @Id
    @GeneratedValue
    private Long id;
    @NonNull
    private String name;
    private String address;
    private String city;
    private String stateOrProvince;
    private String country;
    private String postalCode;
    @ManyToOne(cascade=CascadeType.PERSIST)
    private User user;

    @OneToMany(fetch = FetchType.EAGER, cascade=CascadeType.ALL)
    private Set<Event> events;
} 
```

Enter fullscreen mode Exit fullscreen mode

在同一个包中创建一个`Event.java`类。

```
package com.okta.developer.jugtours.model;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.ManyToMany;
import java.time.Instant;
import java.util.Set;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity
public class Event {

    @Id
    @GeneratedValue
    private Long id;
    private Instant date;
    private String title;
    private String description;
    @ManyToMany
    private Set<User> attendees;
} 
```

Enter fullscreen mode Exit fullscreen mode

还有，一个`User.java`类。

```
package com.okta.developer.jugtours.model;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.Entity;
import javax.persistence.Id;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Entity
public class User {

    @Id
    private String id;
    private String name;
    private String email;
} 
```

Enter fullscreen mode Exit fullscreen mode

创建一个`GroupRepository.java`来管理集团实体。

```
package com.okta.developer.jugtours.model;

import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;

public interface GroupRepository extends JpaRepository<Group, Long> {
    Group findByName(String name);
} 
```

Enter fullscreen mode Exit fullscreen mode

为了加载一些默认数据，在`com.okta.developer.jugtours`包中创建一个`Initializer.java`类。

```
package com.okta.developer.jugtours;

import com.okta.developer.jugtours.model.Event;
import com.okta.developer.jugtours.model.Group;
import com.okta.developer.jugtours.model.GroupRepository;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

import java.time.Instant;
import java.util.Collections;
import java.util.stream.Stream;

@Component
class Initializer implements CommandLineRunner {

    private final GroupRepository repository;

    public Initializer(GroupRepository repository) {
        this.repository = repository;
    }

    @Override
    public void run(String... strings) {
        Stream.of("Denver JUG", "Utah JUG", "Seattle JUG",
                "Richmond JUG").forEach(name ->
                repository.save(new Group(name))
        );

        Group djug = repository.findByName("Denver JUG");
        Event e = Event.builder().title("Full Stack Reactive")
                .description("Reactive with Spring Boot + React")
                .date(Instant.parse("2018-12-12T18:00:00.000Z"))
                .build();
        djug.setEvents(Collections.singleton(e));
        repository.save(djug);

        repository.findAll().forEach(System.out::println);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**提示:**如果你的 IDE 对`Event.builder()`有问题，这意味着你需要打开注释处理和/或安装 Lombok 插件。我不得不卸载/重新安装 IntelliJ IDEA 中的 Lombok 插件来让它工作。

如果您在添加此代码后启动您的应用程序(使用`./mvnw spring-boot:run`),您将看到控制台中显示的群组和事件列表。

```
Group(id=1, name=Denver JUG, address=null, city=null, stateOrProvince=null, country=null, postalCode=null, user=null, events=[Event(id=5, date=2018-12-12T18:00:00Z, title=Full Stack Reactive, description=Reactive with Spring Boot + React, attendees=[])])
Group(id=2, name=Utah JUG, address=null, city=null, stateOrProvince=null, country=null, postalCode=null, user=null, events=[])
Group(id=3, name=Seattle JUG, address=null, city=null, stateOrProvince=null, country=null, postalCode=null, user=null, events=[])
Group(id=4, name=Richmond JUG, address=null, city=null, stateOrProvince=null, country=null, postalCode=null, user=null, events=[]) 
```

Enter fullscreen mode Exit fullscreen mode

添加一个允许你 CRUD 组的`GroupController.java`类(在`src/main/java/.../jugtours/web/GroupController.java`中)。

```
package com.okta.developer.jugtours.web;

import com.okta.developer.jugtours.model.Group;
import com.okta.developer.jugtours.model.GroupRepository;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.net.URI;
import java.net.URISyntaxException;
import java.util.Collection;
import java.util.Optional;

@RestController
@RequestMapping("/api")
class GroupController {

    private final Logger log = LoggerFactory.getLogger(GroupController.class);
    private GroupRepository groupRepository;

    public GroupController(GroupRepository groupRepository) {
        this.groupRepository = groupRepository;
    }

    @GetMapping("/groups")
    Collection<Group> groups() {
        return groupRepository.findAll();
    }

    @GetMapping("/group/{id}")
    ResponseEntity<?> getGroup(@PathVariable Long id) {
        Optional<Group> group = groupRepository.findById(id);
        return group.map(response -> ResponseEntity.ok().body(response))
                .orElse(new ResponseEntity<>(HttpStatus.NOT_FOUND));
    }

    @PostMapping("/group")
    ResponseEntity<Group> createGroup(@Valid @RequestBody Group group) throws URISyntaxException {
        log.info("Request to create group: {}", group);
        Group result = groupRepository.save(group);
        return ResponseEntity.created(new URI("/api/group/" + result.getId()))
                .body(result);
    }

    @PutMapping("/group/{id}")
    ResponseEntity<Group> updateGroup(@PathVariable Long id, @Valid @RequestBody Group group) {
        group.setId(id);
        log.info("Request to update group: {}", group);
        Group result = groupRepository.save(group);
        return ResponseEntity.ok().body(result);
    }

    @DeleteMapping("/group/{id}")
    public ResponseEntity<?> deleteGroup(@PathVariable Long id) {
        log.info("Request to delete group: {}", id);
        groupRepository.deleteById(id);
        return ResponseEntity.ok().build();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您重新启动您的服务器应用程序，并使用浏览器或命令行客户端点击`http://localhost:8080/api/groups`，您应该会看到群组列表。

您可以使用以下 [HTTPie](https://httpie.org) 命令创建、读取、更新和删除组。

```
http POST :8080/api/group name='Dublin JUG' city=Dublin country=Ireland
http :8080/api/group/6
http PUT :8080/api/group/6 name='Dublin JUG' city=Dublin country=Ireland address=Downtown
http DELETE :8080/api/group/6 
```

Enter fullscreen mode Exit fullscreen mode

## 用 Create React App 创建 React UI

Create React App 是一个命令行实用程序，可以为您生成 React 项目。这是一个方便的工具，因为它还提供了构建和优化生产项目的命令。它在幕后使用 webpack 进行构建。如果你想了解更多关于 webpack 的知识，我推荐 [webpack.academy](https://webpack.academy) 。

用 Yarn 在`jugtours`目录下新建一个项目。

```
yarn create react-app app 
```

Enter fullscreen mode Exit fullscreen mode

在应用创建过程完成后，导航到`app`目录并安装[引导](https://getbootstrap.com/)、React 的 cookie 支持、React 路由器和 [Reactstrap](https://reactstrap.github.io/) 。

```
cd app
yarn add bootstrap@4.1.2 react-cookie@2.2.0 react-router-dom@4.3.1 reactstrap@6.3.0 
```

Enter fullscreen mode Exit fullscreen mode

您将使用 Bootstrap 的 CSS 和 Reactstrap 的组件来使 UI 看起来更好，尤其是在移动电话上。如果你想了解更多关于 Reactstrap 的信息，请看[https://reacts trap . github . io](https://reactstrap.github.io)。它有大量关于其各种组件以及如何使用它们的文档。

在`app/src/index.js`中添加 Bootstrap 的 CSS 文件作为导入。

```
import 'bootstrap/dist/css/bootstrap.min.css'; 
```

Enter fullscreen mode Exit fullscreen mode

## 调用您的 Spring Boot API 并显示结果

修改`app/src/App.js`,使用下面的代码调用`/api/groups`,并在 UI 中显示列表。

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  state = {
    isLoading: true,
    groups: []
  };

  async componentDidMount() {
    const response = await fetch('/api/groups');
    const body = await response.json();
    this.setState({ groups: body, isLoading: false });
  }

  render() {
    const {groups, isLoading} = this.state;

    if (isLoading) {
      return <p>Loading...</p>;
    }

    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <div className="App-intro">
          <h2>JUG List</h2>
          {groups.map(group =>
            <div key={group.id}>
              {group.name}
            </div>
          )}
        </div>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

要从`/api`代理到`http://localhost:8080/api`，添加一个代理设置到`app/package.json`。

```
"scripts": {...},
"proxy": "http://localhost:8080" 
```

Enter fullscreen mode Exit fullscreen mode

要了解有关此功能的更多信息，请在`app/README.md`中搜索“代理”。Create React App 在这个文件中附带了各种文档，多酷啊？！

确保 Spring Boot 正在运行，然后在您的`app`目录中运行`yarn start`。您应该会看到默认组的列表。

[![JUG List](img/e44d33ea670874a50ee7f64d6ff821b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HdhWEUpM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/jug-list-bd649c959a684eb0bbc5301614b2335da8c85a7d7a7fcddfa7bf4df6b920e32a.png)

## 构建一个 React GroupList 组件

React 是关于组件的，您不想在主`App`中呈现所有内容，所以创建`app/src/GroupList.js`并用下面的 JavaScript 填充它。

```
import React, { Component } from 'react';
import { Button, ButtonGroup, Container, Table } from 'reactstrap';
import AppNavbar from './AppNavbar';
import { Link } from 'react-router-dom';

class GroupList extends Component {

  constructor(props) {
    super(props);
    this.state = {groups: [], isLoading: true};
    this.remove = this.remove.bind(this);
  }

  componentDidMount() {
    this.setState({isLoading: true});

    fetch('api/groups')
      .then(response => response.json())
      .then(data => this.setState({groups: data, isLoading: false}));
  }

  async remove(id) {
    await fetch(`/api/group/${id}`, {
      method: 'DELETE',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      }
    }).then(() => {
      let updatedGroups = [...this.state.groups].filter(i => i.id !== id);
      this.setState({groups: updatedGroups});
    });
  }

  render() {
    const {groups, isLoading} = this.state;

    if (isLoading) {
      return <p>Loading...</p>;
    }

    const groupList = groups.map(group => {
      const address = `${group.address || ''} ${group.city || ''} ${group.stateOrProvince || ''}`;
      return <tr key={group.id}>
        <td style={{whiteSpace: 'nowrap'}}>{group.name}</td>
        <td>{address}</td>
        <td>{group.events.map(event => {
          return <div key={event.id}>{new Intl.DateTimeFormat('en-US', {
            year: 'numeric',
            month: 'long',
            day: '2-digit'
          }).format(new Date(event.date))}: {event.title}</div>
        })}</td>
        <td>
          <ButtonGroup>
            <Button size="sm" color="primary" tag={Link} to={"/groups/" + group.id}>Edit</Button>
            <Button size="sm" color="danger" onClick={() => this.remove(group.id)}>Delete</Button>
          </ButtonGroup>
        </td>
      </tr>
    });

    return (
      <div>
        <AppNavbar/>
        <Container fluid>
          <div className="float-right">
            <Button color="success" tag={Link} to="/groups/new">Add Group</Button>
          </div>
          <h3>My JUG Tour</h3>
          <Table className="mt-4">
            <thead>
            <tr>
              <th width="20%">Name</th>
              <th width="20%">Location</th>
              <th>Events</th>
              <th width="10%">Actions</th>
            </tr>
            </thead>
            <tbody>
            {groupList}
            </tbody>
          </Table>
        </Container>
      </div>
    );
  }
}

export default GroupList; 
```

Enter fullscreen mode Exit fullscreen mode

在同一个目录下创建`AppNavbar.js`，在组件之间建立一个通用的 UI 特性。

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
    return <Navbar color="dark" dark expand="md">
      <NavbarBrand tag={Link} to="/">Home</NavbarBrand>
      <NavbarToggler onClick={this.toggle}/>
      <Collapse isOpen={this.state.isOpen} navbar>
        <Nav className="ml-auto" navbar>
          <NavItem>
            <NavLink
              href="https://twitter.com/oktadev">@oktadev</NavLink>
          </NavItem>
          <NavItem>
            <NavLink href="https://github.com/oktadeveloper/okta-spring-boot-react-crud-example">GitHub</NavLink>
          </NavItem>
        </Nav>
      </Collapse>
    </Navbar>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

创建`app/src/Home.js`作为你的应用程序的登陆页面。

```
import React, { Component } from 'react';
import './App.css';
import AppNavbar from './AppNavbar';
import { Link } from 'react-router-dom';
import { Button, Container } from 'reactstrap';

class Home extends Component {
  render() {
    return (
      <div>
        <AppNavbar/>
        <Container fluid>
          <Button color="link"><Link to="/groups">Manage JUG Tour</Link></Button>
        </Container>
      </div>
    );
  }
}

export default Home; 
```

Enter fullscreen mode Exit fullscreen mode

此外，将`app/src/App.js`更改为使用 React Router 在组件之间导航。

```
import React, { Component } from 'react';
import './App.css';
import Home from './Home';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import GroupList from './GroupList';

class App extends Component {
  render() {
    return (
      <Router>
        <Switch>
          <Route path='/' exact={true} component={Home}/>
          <Route path='/groups' exact={true} component={GroupList}/>
        </Switch>
      </Router>
    )
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

为了让你的 UI 更宽敞一点，在`app/src/App.css`中为 Bootrap 的容器类添加一个顶部边距。

```
.container, .container-fluid {
  margin-top: 20px
} 
```

Enter fullscreen mode Exit fullscreen mode

您的 React 应用程序应该在您进行更改时自动更新，您应该会在`http://localhost:3000`看到如下屏幕。

[![Home screen with Manage JUG Tour link](img/7efe5f7311d0adbab627b548288bfae6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8jvpwKnZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/home-with-link-49bb04e0813ed34be986fa0ce6e3dc78943a16eb1b02a4531ce38874ddeb9640.png)

点击 **Manage JUG Tour** ，您应该会看到一个默认群组列表。

[![Group List screen](img/67b322bb567432700d66f8341b770e97.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cbs7yYU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/group-list-70bf3821eb163bb0ec6c7b8938f6e150d3bb99a7cebc0974b2891ee15656e442.png)

你可以在你的 React 应用程序中看到你的 Spring Boot API 的数据，这很好，但是如果你不能编辑它，那就不好玩了！

## 添加一个 React GroupEdit 组件

创建`app/src/GroupEdit.js`并使用它的`componentDidMount()`从 URL 获取 ID 为的组资源。

```
import React, { Component } from 'react';
import { Link, withRouter } from 'react-router-dom';
import { Button, Container, Form, FormGroup, Input, Label } from 'reactstrap';
import AppNavbar from './AppNavbar';

class GroupEdit extends Component {

  emptyItem = {
    name: '',
    address: '',
    city: '',
    stateOrProvince: '',
    country: '',
    postalCode: ''
  };

  constructor(props) {
    super(props);
    this.state = {
      item: this.emptyItem
    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  async componentDidMount() {
    if (this.props.match.params.id !== 'new') {
      const group = await (await fetch(`/api/group/${this.props.match.params.id}`)).json();
      this.setState({item: group});
    }
  }

  handleChange(event) {
    const target = event.target;
    const value = target.value;
    const name = target.name;
    let item = {...this.state.item};
    item[name] = value;
    this.setState({item});
  }

  async handleSubmit(event) {
    event.preventDefault();
    const {item} = this.state;

    await fetch('/api/group', {
      method: (item.id) ? 'PUT' : 'POST',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(item),
    });
    this.props.history.push('/groups');
  }

  render() {
    const {item} = this.state;
    const title = <h2>{item.id ? 'Edit Group' : 'Add Group'}</h2>;

    return <div>
      <AppNavbar/>
      <Container>
        {title}
        <Form onSubmit={this.handleSubmit}>
          <FormGroup>
            <Label for="name">Name</Label>
            <Input type="text" name="name" id="name" value={item.name || ''}
                   onChange={this.handleChange} autoComplete="name"/>
          </FormGroup>
          <FormGroup>
            <Label for="address">Address</Label>
            <Input type="text" name="address" id="address" value={item.address || ''}
                   onChange={this.handleChange} autoComplete="address-level1"/>
          </FormGroup>
          <FormGroup>
            <Label for="city">City</Label>
            <Input type="text" name="city" id="city" value={item.city || ''}
                   onChange={this.handleChange} autoComplete="address-level1"/>
          </FormGroup>
          <div className="row">
            <FormGroup className="col-md-4 mb-3">
              <Label for="stateOrProvince">State/Province</Label>
              <Input type="text" name="stateOrProvince" id="stateOrProvince" value={item.stateOrProvince || ''}
                     onChange={this.handleChange} autoComplete="address-level1"/>
            </FormGroup>
            <FormGroup className="col-md-5 mb-3">
              <Label for="country">Country</Label>
              <Input type="text" name="country" id="country" value={item.country || ''}
                     onChange={this.handleChange} autoComplete="address-level1"/>
            </FormGroup>
            <FormGroup className="col-md-3 mb-3">
              <Label for="country">Postal Code</Label>
              <Input type="text" name="postalCode" id="postalCode" value={item.postalCode || ''}
                     onChange={this.handleChange} autoComplete="address-level1"/>
            </FormGroup>
          </div>
          <FormGroup>
            <Button color="primary" type="submit">Save</Button>{' '}
            <Button color="secondary" tag={Link} to="/groups">Cancel</Button>
          </FormGroup>
        </Form>
      </Container>
    </div>
  }
}

export default withRouter(GroupEdit); 
```

Enter fullscreen mode Exit fullscreen mode

底部需要更高阶的组件`withRouter()`来暴露`this.props.history`，这样您可以在添加或保存组后导航回`GroupList`。

修改`app/src/App.js`以导入`GroupEdit`并指定其路径。

```
import GroupEdit from './GroupEdit';

class App extends Component {
  render() {
    return (
      <Router>
        <Switch>
          ...
          <Route path='/groups/:id' component={GroupEdit}/>
        </Switch>
      </Router>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该能够添加和编辑组了！

[![Add Group screen](img/533fd014058b729e4b24c47738db3fbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3oFnrm9E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/add-group-60031f7786d4749bc17f790419b23df9d0d496636bbbfef8655622ffd1df1fda.png)

[![Edit Group screen](img/446202d3ab74a86d6de28333c39b17ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gBfplBsh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/edit-group-9532d22006671154c04028260a8cbd7e6cbcc9f8540e9875c87a0e75f8597c16.png)

## 用 Okta 添加认证

构建一个 CRUD 应用程序很酷，但构建一个安全的应用程序更酷。为此，您需要添加身份验证，以便用户在查看/修改组之前必须登录。为了简单起见，您可以使用 Okta 针对 OIDC 的 API。在 Okta，我们的目标是让[身份管理](https://developer.okta.com/product/user-management/)比你习惯的更容易、更安全、更可扩展。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

你被卖了吗？[注册一个永远免费的开发人员帐户](https://developer.okta.com/signup/)，完成后，请回来，这样您就可以了解更多有关使用 Spring Boot 构建安全应用的信息！

### 春安+ OIDC

[Spring Security 在其 5.0 版本中增加了 OIDC 支持](https://developer.okta.com/blog/2017/12/18/spring-security-5-oidc)。从那以后，他们做了不少改进，简化了所需的配置。我觉得探索最新最好的会很有趣，所以我从用 Spring 的快照库更新`pom.xml`开始，将 Spring Boot 和 Spring 安全升级到夜间版本，并添加必要的 Spring 安全依赖项来进行 OIDC 认证。

```
<?xml version="1.0" encoding="UTF-8"?>
<project>
    ...
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.BUILD-SNAPSHOT</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        ...
        <spring-security.version>5.1.0.BUILD-SNAPSHOT</spring-security.version>
    </properties>

    <dependencies>
        ...
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-oauth2-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-oauth2-jose</artifactId>
        </dependency>
    </dependencies>

    <build...>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshot</name>
            <url>http://repo.spring.io/snapshot</url>
        </repository>
    </repositories>
</project> 
```

Enter fullscreen mode Exit fullscreen mode

### 在 Okta 创建一个 OIDC App

[登录](https://login.okta.com/?SAMLRequest=fc%2B7CsJAEAXQXvAflu1NNJUMeZBGELTx1a%2FrYILJTtyZGD%2FfSBRiYzlw77lMnD3rSj3Qc0ku0YtgrhU6S5fSXRN9PKxmS52l00nMpq6iBvJWCrfDe4ss6vStRe9aDzmGIZfo1jsgwyWDMzUyiIV9vt1AH4XGk5ClSvewUgMNa%2BYW%2FVj5jxhm9NLP67QQaSAMu64L6CYmsFSHlnzT4ZlLwTgcL6Sf8%2FeX9AU%3Dhttps://login.okta.com/?SAMLRequest=fc%2B7CsJAEAXQXvAflu1NNJUMeZBGELTx1a%2FrYILJTtyZGD%2FfSBRiYzlw77lMnD3rSj3Qc0ku0YtgrhU6S5fSXRN9PKxmS52l00nMpq6iBvJWCrfDe4ss6vStRe9aDzmGIZfo1jsgwyWDMzUyiIV9vt1AH4XGk5ClSvewUgMNa%2BYW%2FVj5jxhm9NLP67QQaSAMu64L6CYmsFSHlnzT4ZlLwTgcL6Sf8%2FeX9AU%3D)到你的 Okta 开发者账户(或者[注册](https://developer.okta.com/signup/)如果你没有账户)然后导航到**应用** > **添加应用**。点击**网页**并点击**下一个**。给这个应用取一个你容易记住的名字，并将`http://localhost:8080/login/oauth2/code/okta`指定为登录重定向 URI。点击**完成**，然后点击**编辑**编辑常规设置。添加`http://localhost:3000`和`http://localhost:8080`作为注销重定向 URIs，然后点击**保存**。

将默认授权服务器的 URI、客户端 ID 和客户端密码复制并粘贴到`src/main/resources/application.yml`中。创建这个文件，可以删除同一个目录下的`application.properties`文件。

```
spring:
  security:
    oauth2:
      client:
        registration:
          okta:
            client-id: {clientId}
            client-secret: {clientSecret}
            scope: openid email profile
        provider:
          okta:
            issuer-uri: https://{yourOktaDomain}/oauth2/default 
```

Enter fullscreen mode Exit fullscreen mode

## 为 React 和用户身份配置 Spring 安全性

为了使 Spring Security 反应友好，在`src/main/java/.../jugtours/config`中创建一个`SecurityConfiguration.java`文件。创建`config`目录并将这个类放入其中。

```
package com.okta.developer.jugtours.config;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.web.authentication.LoginUrlAuthenticationEntryPoint;
import org.springframework.security.web.authentication.SavedRequestAwareAuthenticationSuccessHandler;
import org.springframework.security.web.csrf.CookieCsrfTokenRepository;
import org.springframework.security.web.savedrequest.RequestCache;
import org.springframework.security.web.savedrequest.SavedRequest;

import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.util.Collection;
import java.util.List;
import java.util.Locale;
import java.util.Map;

@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    private final Logger log = LoggerFactory.getLogger(SecurityConfiguration.class);

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        RequestCache requestCache = refererRequestCache();
        SavedRequestAwareAuthenticationSuccessHandler handler = new SavedRequestAwareAuthenticationSuccessHandler();
        handler.setRequestCache(requestCache);
        http
            .exceptionHandling()
                .authenticationEntryPoint(new LoginUrlAuthenticationEntryPoint("/oauth2/authorization/okta"))
                .and()
            .oauth2Login()
                .successHandler(handler)
                .and()
            .csrf()
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
                .and()
            .requestCache()
                .requestCache(requestCache)
                .and()
            .authorizeRequests()
                .antMatchers("/**/*.{js,html,css}").permitAll()
                .antMatchers("/", "/api/user").permitAll()
                .anyRequest().authenticated();
    }

    @Bean
    public RequestCache refererRequestCache() {
        return new RequestCache() {
            private String savedAttrName = getClass().getName().concat(".SAVED");

            @Override
            public void saveRequest(HttpServletRequest request, HttpServletResponse response) {
                String referrer = request.getHeader("referer");
                if (referrer != null) {
                    request.getSession().setAttribute(this.savedAttrName, referrerRequest(referrer));
                }
            }

            @Override
            public SavedRequest getRequest(HttpServletRequest request, HttpServletResponse response) {
                HttpSession session = request.getSession(false);

                if (session != null) {
                    return (SavedRequest) session.getAttribute(this.savedAttrName);
                }

                return null;
            }

            @Override
            public HttpServletRequest getMatchingRequest(HttpServletRequest request, HttpServletResponse response) {
                return request;
            }

            @Override
            public void removeRequest(HttpServletRequest request, HttpServletResponse response) {
                HttpSession session = request.getSession(false);

                if (session != null) {
                    log.debug("Removing SavedRequest from session if present");
                    session.removeAttribute(this.savedAttrName);
                }
            }
        };
    }

    private SavedRequest referrerRequest(final String referrer) {
        return new SavedRequest() {
            @Override
            public String getRedirectUrl() {
                return referrer;
            }

            @Override
            public List<Cookie> getCookies() {
                return null;
            }

            @Override
            public String getMethod() {
                return null;
            }

            @Override
            public List<String> getHeaderValues(String name) {
                return null;
            }

            @Override
            public Collection<String> getHeaderNames() {
                return null;
            }

            @Override
            public List<Locale> getLocales() {
                return null;
            }

            @Override
            public String[] getParameterValues(String name) {
                return new String[0];
            }

            @Override
            public Map<String, String[]> getParameterMap() {
                return null;
            }
        };
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这门课发生了很多事情，所以让我解释一些事情。在`configure()`方法的开始，您设置了一种新类型的请求缓存，它缓存 referrer 头(在现实生活中拼错了`referer`),所以 Spring Security 可以在认证后重定向回它。当你在`http://localhost:3000`上开发 React 并想在登录后被重定向回那里时，基于 referrer 的请求缓存就派上了用场。

```
@Override
protected void configure(HttpSecurity http) throws Exception {
    RequestCache requestCache = refererRequestCache();
    SavedRequestAwareAuthenticationSuccessHandler handler = new SavedRequestAwareAuthenticationSuccessHandler();
    handler.setRequestCache(requestCache);
    http
        .exceptionHandling()
            .authenticationEntryPoint(new LoginUrlAuthenticationEntryPoint("/oauth2/authorization/okta"))
            .and()
        .oauth2Login()
            .successHandler(handler)
            .and()
        .csrf()
            .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
            .and()
        .requestCache()
            .requestCache(requestCache)
            .and()
        .authorizeRequests()
            .antMatchers("/**/*.{js,html,css}").permitAll()
            .antMatchers("/", "/api/user").permitAll()
            .anyRequest().authenticated();
} 
```

Enter fullscreen mode Exit fullscreen mode

`authenticationEntryPoint()`线让春安自动重定向到 Okta。在 Spring Security 5.1.0.RELEASE 中，如果只配置了一个 OIDC 提供者，就不需要这一行了；它会自动重定向。

用`CookieCsrfTokenRepository.withHttpOnlyFalse()`配置 CSRF(跨站点请求伪造)保护意味着`XSRF-TOKEN` cookie 不会被标记为 HTTP-only，因此 React 可以读取它，并在试图操作数据时将其发送回来。

`antMatchers`行定义了匿名用户可以访问的 URL。你很快就会配置好，让你的 React 应用由你的 Spring Boot 应用提供服务，这就是允许网页文件和“/”的原因。您可能会注意到还有一个暴露的`/api/user`路径。创建`src/main/java/.../jugtours/web/UserController.java`并用下面的代码填充它。React 将使用该 API 来 1)查明用户是否经过身份验证，以及 2)执行全局注销。

```
package com.okta.developer.jugtours.web;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.oauth2.core.oidc.OidcIdToken;
import org.springframework.security.oauth2.core.user.OAuth2User;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletRequest;
import java.util.HashMap;
import java.util.Map;

@RestController
public class UserController {

    @Value("${spring.security.oauth2.client.provider.okta.issuer-uri}")
    String issuerUri;

    @GetMapping("/api/user")
    public ResponseEntity<?> getUser(@AuthenticationPrincipal OAuth2User user) {
        if (user == null) {
            return new ResponseEntity<>("", HttpStatus.OK);
        } else {
            return ResponseEntity.ok().body(user.getAttributes());
        }
    }

    @PostMapping("/api/logout")
    public ResponseEntity<?> logout(HttpServletRequest request,
                                    @AuthenticationPrincipal(expression = "idToken") OidcIdToken idToken) {
        // send logout URL to client so they can initiate logout - doesn't work from the server side
        // Make it easier: https://github.com/spring-projects/spring-security/issues/5540
        String logoutUrl = issuerUri + "/v1/logout";

        Map<String, String> logoutDetails = new HashMap<>();
        logoutDetails.put("logoutUrl", logoutUrl);
        logoutDetails.put("idToken", idToken.getTokenValue());
        request.getSession(false).invalidate();
        return ResponseEntity.ok().body(logoutDetails);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在创建群组时，您还需要添加用户信息，这样您就可以通过*来过滤您的* JUG 旅程。在与`GroupRepository.java`相同的目录下添加一个`UserRepository.java`。

```
package com.okta.developer.jugtours.model;

import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, String> {
} 
```

Enter fullscreen mode Exit fullscreen mode

给`GroupRepository.java`添加一个新的`findAllByUserId(String id)`方法。

```
List<Group> findAllByUserId(String id); 
```

Enter fullscreen mode Exit fullscreen mode

然后将`UserRepository`注入到`GroupController.java`中，在添加新组的时候用它来创建(或者抓取一个已有的用户)。在那里，修改`groups()`方法以按用户过滤。

```
package com.okta.developer.jugtours.web;

import com.okta.developer.jugtours.model.Group;
import com.okta.developer.jugtours.model.GroupRepository;
import com.okta.developer.jugtours.model.User;
import com.okta.developer.jugtours.model.UserRepository;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.oauth2.core.user.OAuth2User;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.net.URI;
import java.net.URISyntaxException;
import java.security.Principal;
import java.util.Collection;
import java.util.Map;
import java.util.Optional;

@RestController
@RequestMapping("/api")
class GroupController {

    private final Logger log = LoggerFactory.getLogger(GroupController.class);
    private GroupRepository groupRepository;
    private UserRepository userRepository;

    public GroupController(GroupRepository groupRepository, UserRepository userRepository) {
        this.groupRepository = groupRepository;
        this.userRepository = userRepository;
    }

    @GetMapping("/groups")
    Collection<Group> groups(Principal principal) {
        return groupRepository.findAllByUserId(principal.getName());
    }

    @GetMapping("/group/{id}")
    ResponseEntity<?> getGroup(@PathVariable Long id) {
        Optional<Group> group = groupRepository.findById(id);
        return group.map(response -> ResponseEntity.ok().body(response))
                .orElse(new ResponseEntity<>(HttpStatus.NOT_FOUND));
    }

    @PostMapping("/group")
    ResponseEntity<Group> createGroup(@Valid @RequestBody Group group,
                                      @AuthenticationPrincipal OAuth2User principal) throws URISyntaxException {
        log.info("Request to create group: {}", group);
        Map<String, Object> details = principal.getAttributes();
        String userId = details.get("sub").toString();

        // check to see if user already exists
        Optional<User> user = userRepository.findById(userId);
        group.setUser(user.orElse(new User(userId,
                        details.get("name").toString(), details.get("email").toString())));

        Group result = groupRepository.save(group);
        return ResponseEntity.created(new URI("/api/group/" + result.getId()))
                .body(result);
    }

    @PutMapping("/group")
    ResponseEntity<Group> updateGroup(@Valid @RequestBody Group group) {
        log.info("Request to update group: {}", group);
        Group result = groupRepository.save(group);
        return ResponseEntity.ok().body(result);
    }

    @DeleteMapping("/group/{id}")
    public ResponseEntity<?> deleteGroup(@PathVariable Long id) {
        log.info("Request to delete group: {}", id);
        groupRepository.deleteById(id);
        return ResponseEntity.ok().build();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了放大变化，它们在`groups()`和`createGroup()`方法中。很巧的是，Spring JPA 会为你创建`findAllByUserId()`方法/查询，而`userRepository.findById()`会使用 Java 8 的[选项](http://www.baeldung.com/java-optional)。

```
@GetMapping("/groups")
Collection<Group> groups(Principal principal) {
    return groupRepository.findAllByUserId(principal.getName());
}

@PostMapping("/group")
ResponseEntity<Group> createGroup(@Valid @RequestBody Group group,
                                  @AuthenticationPrincipal OAuth2User principal) throws URISyntaxException {
    log.info("Request to create group: {}", group);
    Map<String, Object> details = principal.getAttributes();
    String userId = details.get("sub").toString();

    // check to see if user already exists
    Optional<User> user = userRepository.findById(userId);
    group.setUser(user.orElse(new User(userId,
                    details.get("name").toString(), details.get("email").toString())));

    Group result = groupRepository.save(group);
    return ResponseEntity.created(new URI("/api/group/" + result.getId()))
            .body(result);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 修改反应处理 CSRF 并被身份感知

您需要对 React 组件进行一些修改，使它们能够识别身份。您要做的第一件事是修改`App.js`以将所有内容包装在`CookieProvider`中。该组件允许您读取 CSRF cookie 并将其作为标头发送回去。

```
import { CookiesProvider } from 'react-cookie';

class App extends Component {
  render() {
    return (
      <CookiesProvider>
        <Router...>
      </CookiesProvider>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

修改`app/src/Home.js`调用`/api/user`查看用户是否登录。如果不是，显示一个`Login`按钮。

```
import React, { Component } from 'react';
import './App.css';
import AppNavbar from './AppNavbar';
import { Link } from 'react-router-dom';
import { Button, Container } from 'reactstrap';
import { withCookies } from 'react-cookie';

class Home extends Component {
  state = {
    isLoading: true,
    isAuthenticated: false,
    user: undefined
  };

  constructor(props) {
    super(props);
    const {cookies} = props;
    this.state.csrfToken = cookies.get('XSRF-TOKEN');
    this.login = this.login.bind(this);
    this.logout = this.logout.bind(this);
  }

  async componentDidMount() {
    const response = await fetch('/api/user', {credentials: 'include'});
    const body = await response.text();
    if (body === '') {
      this.setState(({isAuthenticated: false}))
    } else {
      this.setState({isAuthenticated: true, user: JSON.parse(body)})
    }
  }

  login() {
    let port = (window.location.port ? ':' + window.location.port : '');
    if (port === ':3000') {
      port = ':8080';
    }
    window.location.href = '//' + window.location.hostname + port + '/private';
  }

  logout() {
    console.log('logging out...');
    fetch('/api/logout', {method: 'POST', credentials: 'include',
      headers: {'X-XSRF-TOKEN': this.state.csrfToken}}).then(res => res.json())
      .then(response => {
        window.location.href = response.logoutUrl + "?id_token_hint=" +
          response.idToken + "&post_logout_redirect_uri=" + window.location.origin;
      });
  }

  render() {
    const message = this.state.user ?
      <h2>Welcome, {this.state.user.name}!</h2> :
      <p>Please log in to manage your JUG Tour.</p>;

    const button = this.state.isAuthenticated ?
      <div>
        <Button color="link"><Link to="/groups">Manage JUG Tour</Link></Button>
        <br/>
        <Button color="link" onClick={this.logout}>Logout</Button>
      </div> :
      <Button color="primary" onClick={this.login}>Login</Button>;

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
}

export default withCookies(Home); 
```

Enter fullscreen mode Exit fullscreen mode

在这个组件中，您应该知道一些事情:

1.  `withCookies()`将`Home`组件包装在底部，使其能够访问 cookies。然后你可以在构造函数中使用`const {cookies} = props`，用`cookies.get('XSRF-TOKEN')`获取一个 cookie。
2.  使用`fetch()`时，需要包含`{credentials: 'include'}`来传输 cookies。如果你不包括这个选项，你将得到一个 403 禁止。
3.  来自 Spring Security 的 CSRF cookie 的名称与您需要发回的标头不同。cookie 名为`XSRF-TOKEN`，而头名为`X-XSRF-TOKEN`。

更新`app/src/GroupList.js`有类似的变化。好消息是您不需要对`render()`方法做任何修改。

```
import { Link, withRouter } from 'react-router-dom';
import { instanceOf } from 'prop-types';
import { withCookies, Cookies } from 'react-cookie';

class GroupList extends Component {
  static propTypes = {
    cookies: instanceOf(Cookies).isRequired
  };

  constructor(props) {
    super(props);
    const {cookies} = props;
    this.state = {groups: [], csrfToken: cookies.get('XSRF-TOKEN'), isLoading: true};
    this.remove = this.remove.bind(this);
  }

  componentDidMount() {
    this.setState({isLoading: true});

    fetch('api/groups', {credentials: 'include'})
      .then(response => response.json())
      .then(data => this.setState({groups: data, isLoading: false}))
      .catch(() => this.props.history.push('/'))
  }

  async remove(id) {
    await fetch(`/api/group/${id}`, {
      method: 'DELETE',
      headers: {
        'X-XSRF-TOKEN': this.state.csrfToken,
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      },
      credentials: 'include'
    }).then(() => {
        let updatedGroups = [...this.state.groups].filter(i => i.id !== id);
        this.setState({groups: updatedGroups});
      });
  }

  render() {...}
}

export default withCookies(withRouter(GroupList)); 
```

Enter fullscreen mode Exit fullscreen mode

也更新`GroupEdit.js`。

```
import { instanceOf } from 'prop-types';
import { Cookies, withCookies } from 'react-cookie';

class GroupEdit extends Component {
  static propTypes = {
    cookies: instanceOf(Cookies).isRequired
  };

  emptyItem = {
    name: '',
    address: '',
    city: '',
    stateOrProvince: '',
    country: '',
    postalCode: ''
  };

  constructor(props) {
    super(props);
    const {cookies} = props;
    this.state = {
      item: this.emptyItem,
      csrfToken: cookies.get('XSRF-TOKEN')
    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  async componentDidMount() {
    if (this.props.match.params.id !== 'new') {
      try {
        const group = await (await fetch(`/api/group/${this.props.match.params.id}`, {credentials: 'include'})).json();
        this.setState({item: group});
      } catch (error) {
        this.props.history.push('/');
      }
    }
  }

  handleChange(event) {
    const target = event.target;
    const value = target.value;
    const name = target.name;
    let item = {...this.state.item};
    item[name] = value;
    this.setState({item});
  }

  async handleSubmit(event) {
    event.preventDefault();
    const {item, csrfToken} = this.state;

    await fetch('/api/group', {
      method: (item.id) ? 'PUT' : 'POST',
      headers: {
        'X-XSRF-TOKEN': csrfToken,
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(item),
      credentials: 'include'
    });
    this.props.history.push('/groups');
  }

  render() {...}
}

export default withCookies(withRouter(GroupEdit)); 
```

Enter fullscreen mode Exit fullscreen mode

经过所有这些变化，你应该能够重新启动 Spring Boot 和反应，并见证规划自己的 JUG 之旅的荣耀！

[![React Login](img/ea8f4ca11c5ab94b154d6fbdd02cb348.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--seEX9ZeK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/localhost-8080-a84b73c19fb17df9a6c8632cd229cb196b3cb8d0815e5e129952a322bac7b3b7.png)

[![My JUG Tour](img/29e5ae1cc86c560d4ed345d3d1da8057.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8z4U8sqE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/my-jug-tour-f8f3199af776a10cc3798934cfe942ea4dd9e0f3910a76e32700380f9ef07922.png)

## 配置 Maven 构建并打包与 Spring Boot 反应

要用 maven 构建和打包 React 应用，可以使用[前端-maven-plugin](https://github.com/eirslett/frontend-maven-plugin) 和 Maven 的配置文件来激活它。为版本添加属性，并为您的`pom.xml`添加一个`<profiles>`部分。

```
<properties>
    ...
    <frontend-maven-plugin.version>1.6</frontend-maven-plugin.version>
    <node.version>v10.6.0</node.version>
    <yarn.version>v1.8.0</yarn.version>
</properties>

<profiles>
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <spring.profiles.active>dev</spring.profiles.active>
        </properties>
    </profile>
    <profile>
        <id>prod</id>
        <build>
            <plugins>
                <plugin>
                    <artifactId>maven-resources-plugin</artifactId>
                    <executions>
                        <execution>
                            <id>copy-resources</id>
                            <phase>process-classes</phase>
                            <goals>
                                <goal>copy-resources</goal>
                            </goals>
                            <configuration>
                                <outputDirectory>${basedir}/target/classes/static</outputDirectory>
                                <resources>
                                    <resource>
                                        <directory>app/build</directory>
                                    </resource>
                                </resources>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>
                <plugin>
                    <groupId>com.github.eirslett</groupId>
                    <artifactId>frontend-maven-plugin</artifactId>
                    <version>${frontend-maven-plugin.version}</version>
                    <configuration>
                        <workingDirectory>app</workingDirectory>
                    </configuration>
                    <executions>
                        <execution>
                            <id>install node</id>
                            <goals>
                                <goal>install-node-and-yarn</goal>
                            </goals>
                            <configuration>
                                <nodeVersion>${node.version}</nodeVersion>
                                <yarnVersion>${yarn.version}</yarnVersion>
                            </configuration>
                        </execution>
                        <execution>
                            <id>yarn install</id>
                            <goals>
                                <goal>yarn</goal>
                            </goals>
                            <phase>generate-resources</phase>
                        </execution>
                        <execution>
                            <id>yarn test</id>
                            <goals>
                                <goal>yarn</goal>
                            </goals>
                            <phase>test</phase>
                            <configuration>
                                <arguments>test</arguments>
                            </configuration>
                        </execution>
                        <execution>
                            <id>yarn build</id>
                            <goals>
                                <goal>yarn</goal>
                            </goals>
                            <phase>compile</phase>
                            <configuration>
                                <arguments>build</arguments>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
        <properties>
            <spring.profiles.active>prod</spring.profiles.active>
        </properties>
    </profile>
</profiles> 
```

Enter fullscreen mode Exit fullscreen mode

同时，将当前的配置文件设置添加到`src/main/resources/application.yml` :

```
spring:
  profiles:
    active: @spring.profiles.active@
  security: 
```

Enter fullscreen mode Exit fullscreen mode

添加之后，你应该可以运行`./mvnw spring-boot:run -Pprod`了，你的应用可以看到你的应用在`http://localhost:8080`上运行。

[![App Running with Maven](img/f0ca75113f35f101f83e2c130f7143c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PXiB2VIa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/spring-boot-2-react/localhost-8080-bd4c6e484ba4206e9b8e7a56793cc16cfccf5fb61f08833dbd1d97098d8c6cba.png)

**注意:**如果您无法登录，您可以尝试在匿名窗口中打开您的应用程序。

## 春天安全的 OAuth 2.0 vs OIDC 支持

在撰写这篇文章时，我与[罗布·温奇](https://twitter.com/rob_winch) (Spring 安全主管)合作，以确保我有效地使用了 Spring 安全。我开始使用 Spring Security 的 OAuth 2.0 支持及其`@EnableOAuth2Sso`注释。Rob 鼓励我使用 Spring Security 的 OIDC 支持，并帮助我完成了所有工作。

随着 Spring Boot 2.1 和 Spring Security 5.1 的里程碑和版本的发布，我将更新这篇文章，删除不再需要的代码。

## 了解 Spring Boot 并做出反应

我希望你喜欢这篇关于如何用 React、Spring Boot 和 Spring Security 进行 CRUD 的教程。您可以看到 Spring Security 的 OIDC 支持非常健壮，并且不需要太多的配置。添加 CSRF 保护并将你的 Spring Boot + React 应用打包成一个单独的神器也很酷！

您可以在 GitHub 上的[https://GitHub . com/okta developer/okta-spring-boot-react-crud-example](https://github.com/oktadeveloper/okta-spring-boot-react-crud-example)找到本教程中创建的示例。

我们已经写了一些其他很酷的 Spring Boot 和反应教程，如果你感兴趣的话可以看看。

*   [与 Spring Boot 和 React 的良好发展](https://developer.okta.com/blog/2017/12/06/bootiful-development-with-spring-boot-and-react)
*   [构建一个 React 原生应用，并使用 OAuth 2.0 进行认证](https://developer.okta.com/blog/2018/03/16/build-react-native-authentication-oauth-2)
*   [使用 Jenkins X 和 Kubernetes 将 CI/CD 添加到您的 Spring Boot 应用程序中](https://developer.okta.com/blog/2018/07/11/ci-cd-spring-boot-jenkins-x-kubernetes)
*   [在 15 分钟内构建带有用户认证的 React 应用](https://developer.okta.com/blog/2017/03/30/react-okta-sign-in-widget)

如果您有任何问题，请不要犹豫在下面留下评论，或者在我们的 [Okta 开发者论坛](https://devforum.okta.com/)上询问我们。如果你想看更多像这样的教程，请在 Twitter 上关注我们！