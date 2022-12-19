# React -如何使用键来避免使用 getDerivedStateFromProps

> 原文：<https://dev.to/gyandeeps/react---how-to-use-keys-to-avoid-using-getderivedstatefromprops-4h84>

# 背景

直到今天，我只在 react dev 在控制台内部抛出警告时才在`react`中使用`key`。除此之外，我从不关心钥匙，也从不花时间去理解它们。这是我对你们所有读者残酷的坦诚。

# 问题

有一个组件，它根据传递给它的不同名字和 id 显示人名表单。因此，使用该表单，用户可以编辑个人详细信息。

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class NameForm extends Component {
    constructor(props) {
        super(props);
        this.state = {
            userId: props.userId || 0,
            name: props.name || ""
        };
    }

    handleChange = event => {
        this.setState({ name: event.target.value });
    };

    handleSubmit = event => {
        console.log("A name was submitted: " + this.state.name);
        event.preventDefault();
    };

    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    Name:
                    <input
                        type="text"
                        value={this.state.name}
                        onChange={this.handleChange}
                    />
                </label>
                <input type="submit" value="Submit" />
            </form>
        );
    }
}

class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            users: {
                1: "A",
                2: "B",
                3: "C"
            },
            editUserId: "new"
        };
    }

    onChange = event => {
        const value = event.target.value;

        this.setState(() => ({
            editUserId: value
        }));
    };

    render() {
        const { editUserId, users } = this.state;

        return (
            <div>
                <span>Select Edit userId: </span>
                <select onChange={this.onChange}>
                    <option value="new">New User</option>
                    {Object.entries(users).map(([userId, name]) => (
                        <option value={userId}>{name}</option>
                    ))}
                </select>
                <NameForm userId={editUserId} name={users[editUserId] || ""} />
            </div>
        );
    }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

### 观察

*   表单名称字段不显示任何数据，因为默认情况下它被设置为新用户。
*   现在，当您从选择列表中选择用户`A`时，它仍然显示名称为空。
*   因为 react 认为它是同一个组件，所以它不会再次调用`constructor`。
*   在这一点上，你会认为我需要根据`prop`的变化来改变`NameForm`组件的`state`。
    *   这称为将状态与道具同步。
*   现在你会确信你需要使用`getDerivedStateFromProps`静态方法，因为你需要基于适当的改变来改变状态。
*   这就是你出错的地方。

# 解

让我引用 [React 文档](https://reactjs.org/docs/react-component.html#static-getderivedstatefromprops)中的一句话:

> 如果您想在属性改变时“重置”某些状态，可以考虑用一个键来完全控制或完全不控制一个组件。

**通过使用`key`对 React 元素进行控制，使`NameForm`组件。**

就是这样。

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class NameForm extends Component {
    constructor(props) {
        super(props);
        this.state = {
            userId: props.userId || 0,
            name: props.name || ""
        };
    }

    handleChange = event => {
        this.setState({ name: event.target.value });
    };

    handleSubmit = event => {
        console.log("A name was submitted: " + this.state.name);
        event.preventDefault();
    };

    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    Name:
                    <input
                        type="text"
                        value={this.state.name}
                        onChange={this.handleChange}
                    />
                </label>
                <input type="submit" value="Submit" />
            </form>
        );
    }
}

class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            users: {
                1: "A",
                2: "B",
                3: "C"
            },
            editUserId: "new"
        };
    }

    onChange = event => {
        const value = event.target.value;

        this.setState(() => ({
            editUserId: value
        }));
    };

    render() {
        const { editUserId, users } = this.state;

        return (
            <div>
                <span>Select Edit userId: </span>
                <select onChange={this.onChange}>
                    <option value="new">New User</option>
                    {Object.entries(users).map(([userId, name]) => (
                        <option value={userId}>{name}</option>
                    ))}
                </select>
                <NameForm key={editUserId} userId={editUserId} name={users[editUserId] || ""} />
            </div>
        );
    }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

### 观察

*   我们将`key`添加到了`NameForm`组件中。
    *   这告诉 react 基于键创建一个新的`NameForm`实例。
    *   这样，组件`constructor`将被调用，状态被保持。
*   现在，当您更改下拉列表中的值时，表单值将会更改。

# 结论

*   当你考虑用`getDerivedStateFromProps`的时候，退一步想清楚问题，看看能不能用`key`。
*   这就是为什么你会看到人们说你应该避免或很少使用`getDerivedStateFromProps`。
*   当我真正使用它时，这让我大开眼界。在阅读文件的时候，我没有完全理解。

液体错误:内部