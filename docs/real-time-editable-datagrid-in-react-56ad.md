# React 中的实时可编辑数据网格

> 原文：<https://dev.to/pmbanugo/real-time-editable-datagrid-in-react-56ad>

数据网格使您能够显示和编辑数据。这在许多数据驱动或业务线应用程序中至关重要。我在自己构建的一些应用程序中使用过它。你可能已经在你的 React 应用中实现了这一点，并且使用了像 [react-bootstrap-table](https://react-bootstrap-table.github.io/react-bootstrap-table2/) 、 [react-grid](https://github.com/eddyson-de/react-grid) 或 [react-table](https://react-table.js.org) 这样的库。有了这些，你就可以在 React 应用程序中添加数据网格，但是如果你想实时完成更改，并在连接的设备/浏览器之间同步更新，该怎么办呢？

在本文中，我将向您展示如何使用 [react-table](https://react-table.js.org) 和 [Hamoni Sync](https://www.hamoni.tech/) 在 React 中构建实时数据网格。react-table 是一个轻量级的快速库，用于在 react 中呈现表格，它支持分页和许多其他功能。Hamoni Sync 是一个实时状态同步服务，使您能够实时同步您的应用程序状态。我将向您展示如何用人们的名字和姓氏构建数据网格。

如果您想继续学习，您应该对 React 和以下安装有所了解:

1.  [节点 j](//nodejs.org)
2.  [NPM](http://npmjs.com/)&T2】npx。如果您安装了 npm 版本 5.2.0 或更高版本，它会将 npx 与 npm 一起安装。
3.  [创建-反应-应用](https://github.com/facebook/create-react-app)

# 创建 React app

我们将使用 create-react-app 创建一个新的 React 项目。打开命令行，运行`npx create-react-app realtime-react-datatable`。这将通过创建一个包含构建 React 应用程序所需文件的新目录`realtime-react-datatable`来引导 React 应用程序。

创建了 React 应用程序后，我们需要安装 react-table 和 Hamoni Sync。仍然在命令行上，运行`cd realtime-react-datatable`切换到应用程序的目录。在命令行中运行`npm i react-table hamoni-sync`来安装这两个包。

## 渲染数据网格

为了呈现数据网格，我们将使用 react-table 组件。打开文件`src/App.js`，用下面的代码更新它:

```
import React, { Component } from "react";
import logo from "./logo.svg";
import "./App.css";
// Import React Table
import ReactTable from "react-table";
import "react-table/react-table.css";
// Import Hamoni Sync
import Hamoni from "hamoni-sync";

class App extends Component {
  constructor() {
    super();
    this.state = {
      data: [],
      firstName: "",
      lastName: ""
    };
  }

  handleChange = event => {
    if (event.target.name === "firstName")
      this.setState({ firstName: event.target.value });
    if (event.target.name === "lastName")
      this.setState({ lastName: event.target.value });
  };

  handleSubmit = event => {
    event.preventDefault();
  };

  renderEditable = cellInfo => {
    return (
      <div
        style={{ backgroundColor: "#fafafa" }}
        contentEditable
        suppressContentEditableWarning
        onBlur={e => {
          const data = [...this.state.data];
          data[cellInfo.index][cellInfo.column.id] = e.target.innerHTML;
          this.setState({ data });
        }}
        dangerouslySetInnerHTML={{
          __html: this.state.data[cellInfo.index][cellInfo.column.id]
        }}
      />
    );
  };

  render() {
    const { data } = this.state;

    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <p className="App-intro">
          <form onSubmit={this.handleSubmit}>
            <h3>Add new record</h3>
            <label>
              FirstName:
              <input
                type="text"
                name="firstName"
                value={this.state.firstName}
                onChange={this.handleChange}
              />
            </label>{" "}
            <label>
              LastName:
              <input
                type="text"
                name="lastName"
                value={this.state.lastName}
                onChange={this.handleChange}
              />
            </label>  
            <input type="submit" value="Add" />
          </form>
        </p>
        <div>
          <ReactTable
            data={data}
            columns={[
              {
                Header: "First Name",
                accessor: "firstName",
                Cell: this.renderEditable
              },
              {
                Header: "Last Name",
                accessor: "lastName",
                Cell: this.renderEditable
              },
              {
                Header: "Full Name",
                id: "full",
                accessor: d => (
                  <div
                    dangerouslySetInnerHTML={{
                      __html: d.firstName + "  " + d.lastName
                    }}
                  />
                )
              }
            ]}
            defaultPageSize={10}
            className="-striped -highlight"
          />
        </div>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码呈现了一个表单和一个可编辑的 react-table 组件。`<ReactTable />`用`data`、`columns`和`defaultPageSize`道具渲染一个组件。`data`道具保存要显示的数据，而`columns`道具用于列定义。`columns` props 中的`accessor`属性表示保存为该列显示的值的属性。`Cell: this.renderEditable`props 中的属性告诉 react-table 该列是可编辑的。其他函数(`handleSubmit` & `handleChange`)允许从页面上的表单中获取新的数据条目。

## Add Hamoni Sync

将使用 Hamoni Sync 实时检索和更新数据网格的数据。我们已经在`App.js`的第 18 行导入了 Hamoni 库；

```
import Hamoni from "hamoni-sync"; 
```

Enter fullscreen mode Exit fullscreen mode

我们需要初始化它，并连接到哈莫尼服务器。为此，我们需要一个帐户和应用程序 ID。按照以下步骤在 Hamoni 中创建一个应用程序。

1.  注册并登录 Hamoni [仪表盘](https://dashboard.hamoni.tech/)
2.  在文本字段中输入您首选的应用程序名称，然后单击创建按钮。这将创建应用程序，并将其显示在应用程序列表部分。
3.  展开**账户 ID** 卡获取您的账户 ID

[![Hamoni dashboard](img/4e41967431b35548bc44f1dd5b14ddc5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yUCiqUkM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.hamoni.tech/assets/dashboard.png)

将以下代码添加到`App.js`以初始化并连接到 Hamoni 同步服务器。

```
async componentDidMount() {
   const accountId = "YOUR_ACCOUNT_ID";
  const appId = "YOUR_APP_ID";
  let hamoni;

  const response = await fetch("https://api.sync.hamoni.tech/v1/token", {
    method: "POST",
    headers: {
    "Content-Type": "application/json; charset=utf-8"
    },
    body: JSON.stringify({ accountId, appId })
  });
  const token = await response.json();

    hamoni = new Hamoni(token);
    hamoni
    .connect()
    .then(() => {

    })
    .catch(error => console.log(error));
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将客户端设备或浏览器连接到 Hamoni 同步服务器。从仪表板复制您的帐户和应用程序 ID，并用字符串占位符分别替换它们。建议从后端调用同步令牌服务器，并将响应令牌发送给客户端应用程序。对于这个例子，我把它们都放在一个地方。

将以下内容添加到`then()`块中的函数中，当该函数成功连接到服务器时执行:

```
hamoni
    .get("datagrid")
    .then(listPrimitive => {
      this.listPrimitive = listPrimitive;

      this.setState({
        data: [...listPrimitive.getAll()]
      });

      listPrimitive.onItemAdded(item => {
        this.setState({ data: [...this.state.data, item.value] });
      });

      listPrimitive.onItemUpdated(item => {
        let data = [
        ...this.state.data.slice(0, item.index),
        item.value,
        ...this.state.data.slice(item.index + 1)
        ];

        this.setState({ data: data });
      });

      listPrimitive.onSync(data => {
        this.setState({ data: data });
      });
    })
    .catch(console.log); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码调用`hamoni.get("datagrid")`来获取数据，用`datagrid`作为 Hamoni Sync 上应用程序状态的名称。Hamoni Sync 允许您存储三种状态，称为同步原语。它们是:

1.  **值原语**:这种状态保存简单的信息，用字符串、布尔或数字等数据类型表示。它最适合于未读消息计数、切换等情况。

2.  **对象原语**:对象状态表示可以被建模为 JavaScript 对象的状态。一个示例用途可以是存储游戏的分数。

3.  **List 原语**:保存状态对象的列表。状态对象是 JavaScript 对象。您可以根据项目在列表中的索引来更新项目。

如果状态可用，它解析并返回一个带有状态原语对象的承诺。这个对象为我们提供了实时更新状态和获取状态更新的方法。

在第 36 行，我们使用了`getAll()`方法来获取数据并为 React 组件设置状态。此外，方法`onItemAdded()`和`onItemUpdated()`用于在添加或更新项目时获取更新。`onSync()`方法在设备或浏览器失去连接的情况下很有用，当它重新连接时，它试图从服务器获取最新状态，并更新本地状态(如果有的话)。

## 添加&更新物品

从上一节中，我们能够获得 datagrid 的数据，并在添加或更新项目时更新状态。让我们添加代码，以便在编辑完一列后添加新项和更新一项。将以下代码添加到`handleSubmit`方法中:

```
handleSubmit = event => {
    this.listPrimitive.add({
        firstName: this.state.firstName,
        lastName: this.state.lastName
    });
    this.setState({ firstName: "", lastName: "" });
    event.preventDefault();
}; 
```

Enter fullscreen mode Exit fullscreen mode

这段代码从表单中获取名字和姓氏，并通过调用`add()`方法将其添加到 Hamoni Sync 上的列表状态原语中。这将触发`onItemAdded()`方法。

为了在数据网格中编辑项目时更新它们，我们将更新传递给第 84 行的`onBlur`属性的函数，如下所示:

```
onBlur={e => {
    let row = this.state.data[cellInfo.index];
    row[cellInfo.column.id] = e.target.innerHTML;
    this.listPrimitive.update(cellInfo.index, row);
}} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码更新从`cellInfo`对象中检索到的索引处的项目。要在 Hamoni Sync 中更新一个列表状态原语，可以用项目的索引和要更新的值调用`update()`方法。在最后一次更改之后，`renderEditable`方法现在看起来应该是这样的:

```
 renderEditable = cellInfo => {
    return (
      <div
        style={{ backgroundColor: "#fafafa" }}
        contentEditable
        suppressContentEditableWarning
        onBlur={e => {
          let row = this.state.data[cellInfo.index];
          row[cellInfo.column.id] = e.target.innerHTML;
          this.listPrimitive.update(cellInfo.index, row);
        }}
        dangerouslySetInnerHTML={{
          __html: this.state.data[cellInfo.index][cellInfo.column.id]
        }}
      />
    );
  }; 
```

Enter fullscreen mode Exit fullscreen mode

此时，除了将在数据网格上呈现的初始数据之外，我们几乎拥有了运行应用程序所需的所有内容。我们需要创建状态，并在 Hamoni Sync 上给它一些数据。在你的工作目录的根目录下添加一个新的文件 **seed.js** ，并在其中添加以下代码:

```
const Hamoni = require("hamoni-sync");
const https = require("https");

const accountId = "YOUR_ACCOUNT_ID";
const appId = "YOUR_APP_ID";
let hamoni;

const data = JSON.stringify({ accountId, appId });

const options = {
  hostname: "api.sync.hamoni.tech",
  path: "/v1/token",
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Content-Length": data.length
  }
};

const req = https.request(options, res => {
  console.log(`statusCode: ${res.statusCode}`);

  res.on("data", token => {
    hamoni = new Hamoni(token);

    hamoni
      .connect()
      .then(response => {
        hamoni
          .createList("datagrid", [
            { firstName: "James", lastName: "Darwin" },
            { firstName: "Jimmy", lastName: "August" }
          ])
          .then(() => console.log("create success"))
          .catch(error => console.log(error));
      })
      .catch(error => console.log(error));
  });
});

req.on("error", error => {
  console.error(error);
});

req.write(data);
req.end(); 
```

Enter fullscreen mode Exit fullscreen mode

这将在 Hamoni Sync 上创建一个列表原语状态，名称为`datagrid`。用您的帐户和应用程序 ID 替换`AccountID`和`APP_ID`字符串。打开命令行，运行`node seed.js`。这应该成功并打印出`create success`的消息。

现在，我们可以启动 React 应用程序，并查看我们的应用程序的运行情况！在命令行中运行命令`npm start`，它会在你的默认浏览器中打开应用程序。

[![React app running](img/5c9893c43bf7f1a2a11a025bf51eb0aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S5mkAUy9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/O6PAXFmSCeYHhTn8BfRA)

万岁！我们有一个带分页的实时可编辑数据网格！

# 结论

我们在 React 中使用 [react-table](https://react-table.js.org) 和 [Hamoni Sync](https://www.hamoni.tech/) 构建了一个实时数据网格。react-table 为数据网格提供动力，Hamoni Sync 处理数据网格的状态。这一切都是通过几行代码和较少的工作设计实时状态逻辑实现的。你可以得到我们在 [GitHub](https://github.com/pmbanugo/realtime-react-datatable) 上完成的应用。可以跟踪哪个单元格正在被编辑，或者锁定其他用户当前正在编辑的单元格。我会把它留给你作为周末的消遣。

如果有任何不清楚的地方，或者在尝试添加锁定或突出显示正在编辑的单元格时遇到问题，请随时发表评论。

快乐编码🎉