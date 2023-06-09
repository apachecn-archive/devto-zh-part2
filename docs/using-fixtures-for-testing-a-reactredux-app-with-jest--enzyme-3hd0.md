# 使用夹具测试 React/Redux 应用程序(使用 Jest 和 Enzyme)

> 原文：<https://dev.to/davidimoore/using-fixtures-for-testing-a-reactredux-app-with-jest--enzyme-3hd0>

我喜欢测试，因为它帮助我更好地理解我写的代码。它解决的一个特别的问题是我如何期望我获取的数据被呈现。由于相同的数据经常被传递给多个函数，我发现使用 fixtures a 是一种非常有用的方法，可以确认一切都按预期运行。我把我认为实用的演示放在了下面。

让我们假设以下情况

*   我们有一个端点`GET /users`
*   我们希望呈现一个带有来自端点的响应的用户列表
*   我们将使用 redux 来管理应用程序的状态
*   我们想用[玩笑](https://jestjs.io/)和[酶](https://github.com/airbnb/enzyme)测试所有的东西(减速器、动作、组件和容器)

你需要熟悉 redux，包括[异步动作](https://redux.js.org/advanced/async-actions)和 [thunk](https://github.com/reduxjs/redux-thunk) 。

如果你对这篇文章中涉及 redux 的部分有疑问，那么[文档](https://redux.js.org/)写得真的很好。

### 步骤 1–设置

对于这篇文章，你可以从头开始创建自己的项目，也可以参考 Github repo

1.  安装[纱线](https://yarnpkg.com/lang/en/docs/install/#mac-stable)
2.  安装[创建-反应-应用](https://github.com/facebook/create-react-app)
3.  使用 create-react-app 来[创建你的应用](https://github.com/facebook/create-react-app#yarn)
4.  切换到新项目的根目录，安装 dependencies`yarn add axios redux redux-thunk``yarn add -D axios-mock-adapter enzyme enzyme-adapter-react-16 react-test-renderer redux-mock-store`
5.  创建一个全局设置文件`src/setupTests.js`和以下酶配置:

```
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

Enzyme.configure({ adapter: new Adapter() }); 
```

Enter fullscreen mode Exit fullscreen mode

1.  最后，我们将在项目的根目录下添加一个. env 文件，并添加几个环境变量。
    *   NODE _ PATH–使导入文件更容易。
    *   REACT _ APP _ BASE _ URL——由于我们经常为不同的环境使用不同的服务器，所以我们希望将基本 URL 设置为我们用于开发的任何服务器。我将使用`http://localhost:3001` `NODE_PATH=src/ REACT_APP_BASE_URL=http://localhost:3001`

### 第 2 步–生成静态数据快照

为了在我们的应用程序中获取和呈现数据，我们需要回答几个问题:

–我们从端点

获得什么数据–这些数据是如何在我们的应用程序中呈现的？

我们的端点`GET /users`返回一组用户。

```
[
  {
    "id": 1,
    "first_name": "Diana",
    "last_name": "Prince",
    "email": "dianaprince@flatley.com",
    "nickname": "Wonder Woman",
    "created_at": "2018-07-25T22:18:13.337Z",
    "updated_at": "2018-07-25T22:18:13.337Z"
  },
  {
    "id": 2,
    "first_name": "Bruce",
    "last_name": "Wayne",
    "email": "brucewayne@cummerata.com",
    "nickname": "Batman",
    "created_at": "2018-07-25T22:18:13.340Z",
    "updated_at": "2018-07-25T22:18:13.340Z"
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

让我们基于响应中的一些数据来创建包含静态值的组件:

```
// src/components/UserList.jsx

import React from "react";

const UserList = () => (
    <table>
      <thead>
        <tr>
          <td>Full Name</td>
          <td>Email</td>
          <td>Nickname</td>
        </tr>
      </thead>
      <tbody>
        <tr className="User">
          <td>Diana Prince</td>
          <td>dianaprince@flatley.com</td>
          <td>Wonder Woman</td>
        </tr>
        <tr className="User">
          <td>Bruce Wayne</td>
          <td>brucewayne@cummerata.com</td>
          <td>Batman</td>
        </tr>
      </tbody>
    </table>
  );

export default UserList 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建几个测试。一个告诉我们预期有多少用户行，第二个是快照测试。尽早进行这些测试有助于指导重构，并阻止我们对组件中的“标记”进行任何不必要的更改。

```
// src/ __tests__ /UserList.test.jsx
import React from "react";
import UserList from "components/UserList";

import renderer from "react-test-renderer";

describe("UserList", () => {
  it("displays a list of users", () => {        
    const tree = renderer.create(<UserList/>).toJSON();

    expect(tree).toMatchSnapshot();
  });

  it("renders a list of rows with users", () => {
    const componentWrapper = shallow(<UserList />);
    const numberOfUserRows = componentWrapper.find("tr.User").length;

    expect(numberOfUserRows).toEqual(2);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 3–创建我们的减速器

让我们后退一步，概念化的数据流和事情将如何走到一起。

*   我们将通过分派一个动作来获取一些用户。它将被命名为`fetchUsers`或类似的名字
*   当我们收到用户时，我们会将它们传递给用户缩减器
*   users reducer 将把来自动作的数据转换成一个用户数组，这个用户数组的形状就像我们在测试中使用的用户数组一样
*   这个用户数组最终将被传递给一个`UsersList`组件进行渲染。

让我们构建一个测试来定义我们的 reducers 行为。

```
// __tests__ /usersReducer.test.js 
```

Enter fullscreen mode Exit fullscreen mode

我们有两个重要的数据来帮助我们进一步测试:

*   我们的示例响应
*   基于我们传递给`UserList`组件的响应的用户数组

我们的测试 wUserListContainer 是这样的:

```
import users from "reducers/users";

describe("users reducer", () => {
  it("handles a RECEIVED_USERS action", () => {
    const action = {
      type: "RECEIVED_USERS",
      data: [
        {
          id: 1,
          first_name: "Diana",
          last_name: "Prince",
          email: "dianaprince@flatley.com",
          nickname: "Wonder Woman",
          created_at: "2018-07-25T22:18:13.337Z",
          updated_at: "2018-07-25T22:18:13.337Z"
        },
        {
          id: 2,
          first_name: "Bruce",
          last_name: "Wayne",
          email: "brucewayne@cummerata.com",
          nickname: "Batman",
          created_at: "2018-07-25T22:18:13.340Z",
          updated_at: "2018-07-25T22:18:13.340Z"
        }
      ]
    };

    const result = users(null, action);

    expect(result.users).toEqual([
      {
        id: 1,
        first_name: "Diana",
        last_name: "Prince",
        email: "dianaprince@flatley.com",
        nickname: "Wonder Woman"
      },
      {
        id: 2,
        first_name: "Bruce",
        last_name: "Wayne",
        email: "brucewayne@cummerata.com",
        nickname: "Batman"
      }
    ]);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

还有我们的减速器

```
// src/reducers/user.js
const initialState = {
  users: []
};

const receivedUsers = (state, data) => {
  const users = data.map(user => {
    const { id, first_name, last_name, email, nickname } = user;
    return { id, first_name, last_name, email, nickname };
  });
  return { ...state, users };
};

const users = (state = initialState, action) => {
  switch (action.type) {
    case "RECEIVED_USERS":
      return receivedUsers(state, action.data);
    default:
      return state;  
  }
};

export default users; 
```

Enter fullscreen mode Exit fullscreen mode

让我们也更新我们的`index.js`文件来使用 redux

```
// src/index.js

import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";
import thunkMiddleware from "redux-thunk";
import { applyMiddleware, combineReducers, createStore } from "redux";

import users from "reducers/users";
import "./index.css";
import App from "./components/App";
import registerServiceWorker from "./registerServiceWorker";

const appReducer = combineReducers({
  users
});

let store = createStore(appReducer, applyMiddleware(thunkMiddleware));

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
registerServiceWorker(); 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步提取夹具

你可能已经注意到我们在测试中重复自己

—`UserList`组件得到一个用户数组

——相同的用户数组是我们的 reducer 测试的结果。

让我们将用户数组提取到一个夹具中。

你可以把你的固定装置放在任何你想放的地方，我用的是类似`src/ __fixtures__`的文件夹。

```
// src/ __fixtures__ /reducedUsers.js
const reducedUsers = [
  {
    id: 1,
    first_name: "Diana",
    last_name: "Prince",
    email: "dianaprince@flatley.com",
    nickname: "Wonder Woman"
  },
  {
    id: 2,
    first_name: "Bruce",
    last_name: "Wayne",
    email: "brucewayne@cummerata.com",
    nickname: "Batman"
  }
];

export default reducedUsers; 
```

Enter fullscreen mode Exit fullscreen mode

我们在 reducer 测试中使用了响应数据，稍后我们也会在用户操作测试中使用它。所以我们也应该为它做一个固定装置。

```
// src/ __fixtures__ /getUsersResponse.js

const getUsersResponse = [
  {
    id: 1,
    first_name: "Diana",
    last_name: "Prince",
    email: "dianaprince@flatley.com",
    nickname: "Wonder Woman",
    created_at: "2018-07-25T22:18:13.337Z",
    updated_at: "2018-07-25T22:18:13.337Z"
  },
  {
    id: 2,
    first_name: "Bruce",
    last_name: "Wayne",
    email: "brucewayne@cummerata.com",
    nickname: "Batman",
    created_at: "2018-07-25T22:18:13.340Z",
    updated_at: "2018-07-25T22:18:13.340Z"
  }
];

export default getUsersResponse; 
```

Enter fullscreen mode Exit fullscreen mode

*   让我们更新我们的减速器测试

```
import users from "reducers/users";
import reducedUsers from " __fixtures__ /reducedUsers";
import getUsersResponse from " __fixtures__ /getUsersResponse";

describe("users reducer", () => {
  it("handles a RECEIVED_USERS action", () => {
    const action = {
      type: "RECEIVED_USERS",
      data: getUsersResponse
    };

    const result = users(null, action);

    expect(result.users).toEqual(reducedUsers);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   让我们也更新一下我们的`UserList`测试。同样，这不需要对我们的快照测试进行任何更改。简单的重构不应该呈现不同的东西。

```
import React from "react";
import { shallow } from "enzyme";
import renderer from "react-test-renderer";

import UserList from "components/UserList";
import reducedUsers from " __fixtures__ /reducedUsers";

describe("UserList", () => {
  it("renders correctly", () => {
    const tree = renderer.create(<UserList users={reducedUsers} />).toJSON();

    expect(tree).toMatchSnapshot();
  });

  it("renders a list of rows with users", () => {
    const componentWrapper = shallow(<UserList users={reducedUsers} />);
    const numberOfUserRows = componentWrapper.find("tr.User").length;

    expect(numberOfUserRows).toEqual(2);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可能会想，“但是如果我现在改变 fixture，我必须更新使用它的每个测试”。这正是问题的关键。

如果从减速器返回的信息发生变化，将会影响我们的`UserList`组件。*我们的测试可能会中断，这提示我们可能需要处理数据的变化*。

## 第五步添加 redux 动作

我们的用户动作测试将使我们的 getUsersResponse fixture 的用户

```
import axios from "axios";
import configureMockStore from "redux-mock-store";
import thunk from "redux-thunk";
import MockAdapter from "axios-mock-adapter";

import { fetchUsers } from "actions/users";
import getUsersResponse from " __fixtures__ /getUsersResponse";

const axiosMock = new MockAdapter(axios);
const middlewares = [thunk];
const mockStore = configureMockStore(middlewares);

describe("actions", () => {
  afterEach(() => {
    axiosMock.reset();
  });

  describe("fetchUsers", () => {
    it("should make an http request for users", () => {
      const uri = "http://localhost/users.json";
      axiosMock.onGet(uri).reply(200, getUsersResponse);

      const receiveUsersAction = {
        type: "RECEIVED_USERS",
        data: getUsersResponse
      };

      const store = mockStore({ users: [] });

      store.dispatch(fetchUsers(uri)).then(() => {
        const result = store.getActions();

        expect(result).toMatchObject([receiveUsersAction]);
      });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

以及我们的用户操作

```
// actions/users.js
import axios from "axios";

const fetchUsers = uri => {
  return dispatch =>
    axios.get(uri).then(response => dispatch(receivedUsers(response.data)));
};

const receivedUsers = data => {
  return {
    type: "RECEIVED_USERS",
    data
  };
};

export { fetchUsers }; 
```

Enter fullscreen mode Exit fullscreen mode

## 第六步整合 redux 并反应

这有助于[将用于获取数据的](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)容器与用于呈现获取数据的组件分开。

所以最后一个主要步骤是创建一个`UserListContainer`来获取用户并将结果传递给`UsersList`组件。

我们将导入`UserListContainer`而不是默认导出，默认导出是用 redux 包装的

`UserListContainer`。我们还将模拟出我们的`fetchUsers`

函数，因为我们不想实际测试端点。

我们的示例测试将定义两种场景的预期行为。

–当用户被成功获取并传递到`UserList`组件

时–当用户数组为空时

```
// __tests__ /UserListContainer.test.js
import React from "react";
import {shallow} from "enzyme";

import {UserListContainer} from "containers/UserListContainer";
import reducedUsers from " __fixtures__ /reducedUsers";

describe("UserListContainer", () => {
  it("displays the UsersList component when it has fetched users", () => {

    const props = {
      fetchUsers: jest.fn(),
      users: reducedUsers
    };

    const container = shallow(<UserListContainer {...props} />);
    const userListComponent = container.find('UserList').length;

    expect(userListComponent).toEqual(1)
  });

  it("does not display the UserList when ther are no users", () => {
    const props = {
      fetchUsers: jest.fn(),
      users: []
    };

    const container = shallow(<UserListContainer {...props} />);
    const userListComponentLength = container.find('UserList').length;

    expect(userListComponentLength).toEqual(0)
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后是我们的用户列表容器

```
// src/containers/UserListContainer.jsx

import React from "react";
import { connect } from "react-redux";

import UserList from "components/UserList";
import * as userActions from "actions/users";

// REACT_APP_BASE_URL stored in our .env file
const GET_USERS_URL = `${process.env.REACT_APP_BASE_URL}/users.json`;

export class UserListContainer extends React.Component {
  componentDidMount() {
    const { fetchUsers } = this.props;

    fetchUsers(GET_USERS_URL);
  }

  render() {
    const { users } = this.props;
    return users && users.length > 0 ? (
      <UserList users={users} />
    ) : (
      <div>No Users!</div>
    );
  }
}

const mapStateToProps = ({ users }) => ({ ...users });

export default connect(
  mapStateToProps,
  userActions
)(UserListContainer); 
```

Enter fullscreen mode Exit fullscreen mode

让我们渲染应用组件
中的所有内容

```
import React, { Component } from 'react';

import logo from 'logo.svg';
import UserListContainer from "containers/UserListContainer";

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <div>
          <UserListContainer />
        </div>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

支持和反对在测试中使用固定物都有充分的理由。如果过度使用，它们会变得笨拙和过多。我相信，除了像工厂那样更动态地生成数据的功能之外，固定设备也有一席之地。在后续文章中，我将继续讲述如何将相同的设备用于[故事书](https://github.com/storybooks/storybook)。