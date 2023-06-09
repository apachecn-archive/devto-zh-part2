# 启用后退按钮来取消 React 应用程序中的确认对话框

> 原文：<https://dev.to/joshtynjala/enabling-the-back-button-to-cancel-confirmation-dialogs-in-react-apps-1ng9>

用户希望当按下 back 按钮时，对话框、动作表、警告、抽屉和其他弹出窗口会关闭。这在 Android 上尤其如此(在 Android 上，后退按钮在所有应用程序中都是全局可用的)，但这种行为对于任何在网络浏览器中运行的应用程序也是有意义的——无论是在移动设备上还是在桌面上。通常，当一个对话框打开时，后退按钮应该像用户选择了“取消”选项一样。可能发生的最糟糕的事情(但不幸的是，这种情况非常普遍)是，对话框将关闭，浏览器将离开打开该对话框的页面！

在 React 应用程序中，我们可以使用浏览器的历史(由我们选择的路由库公开)来确保在对话框打开时按下后退按钮关闭对话框，并将应用程序保留在打开对话框的同一屏幕上。另外，如果用户再次向前导航，我们可以很容易地将对话框恢复到它以前的状态。让我们来看看。

[![](img/c8c1ae6ce36ddc3a4608c27f74f84f1d.png)T2】](https://codesandbox.io/s/n74l8jpv6p)

为了方便起见，我在 CodeSandbox 上创建了一个例子[来演示最终结果。在这个例子中，我添加了](https://codesandbox.io/s/n74l8jpv6p) [Reach Router](https://reach.tech/router/) 和 [Material UI](https://material-ui.com/) 作为依赖项，以提供路由/导航和一些 UI 控件。我在这里演示的概念应该与 [React Router](https://reacttraining.com/react-router/web/) 和其他 UI 控件库一起工作，不需要太多的修改。

 [![Edit React dialogs with back button](img/0b3f0135583496627e3621355d8e9248.png)

T6】](https://codesandbox.io/s/n74l8jpv6p)

在我们的示例应用程序中，我们有一个用户列表。当您单击/点击用户名时，会弹出一个对话框，其中包含一些可以执行的操作。或者您可以按“取消”关闭对话框。我们的目标是确保浏览器的后退按钮将关闭对话框，而不会离开所有用户的列表，就像通过单击/点击选择取消选项一样。

我们可以使用浏览器历史的状态对象将用户传递到对话框。我们将把一个新状态推送到堆栈上(不改变 URL)，对话框将检查当前状态，以知道它应该何时打开或关闭。

让我们从使用 Material UI 中的一些组件来呈现用户列表开始:

```
// inside render()
<List>
  {users.map(user => {
    return (
      <ListItem
        key={user.id}
        button
        onClick={() => this.selectUser(user)}
      >
        <ListItemIcon>
          <Person />
        </ListItemIcon>
        <ListItemText primary={user.name} />
      </ListItem>
    );
  })}
</List> 
```

我们有一组映射到`ListItem`组件的用户。当一个列表项被点击时，我们调用一个名为`selectUser()`的函数来操作浏览器的历史。

作为参考，数组中的一个用户是这样的:

```
{ id: 1, name: "Franklin Nelson" } 
```

在`selectUser()`中，我们使用 Reach Router 提供给组件的`navigate()`函数将用户添加到历史堆栈中。

```
selectUser(user) {
  let newState = { selectedUser: user };
  this.props.navigate(this.props.location.pathname, { state: newState });
}; 
```

注意，我们使用`this.props.location.pathname`(也是由 Reach 路由器提供的)来确保 URL 不会改变。我们停留在相同的位置，但是添加了一个新的历史条目，它具有不同的状态。这意味着当我们回去时，我们将保持一致。

接下来，让我们看看我们的`UserActionsDialog`组件是如何渲染的。

作为第一步，我们应该检查当前历史状态是否有选择的用户。如果没有选择用户，我们将简单地返回`null`，因为我们不想呈现对话框:

```
// inside UserActionsDialog's render()
let user = undefined;
let state = this.props.location.state;
if (state && "selectedUser" in state) {
  user = state.selectedUser;
}
if(!user)
{
  return null;
} 
```

如果选择了一个用户，我们应该呈现我们的对话框。它可能看起来像这样:

```
// continued from above
return (
  <Dialog open>
    <DialogTitle>{user.name}</DialogTitle>
    <List>
      <ListItem
        button
        onClick={() => alert("Option 1 selected for " + user.name)}>
        <ListItemText primary="Option 1" />
      </ListItem>
      <ListItem
        button
        onClick={() => alert("Option 2 selected for " + user.name)}>
        <ListItemText primary="Option 2" />
      </ListItem>
      <ListItem
        button
        onClick={this.cancelItem_onClick}>
        <ListItemText primary="Cancel" />
      </ListItem>
    </List>
  </Dialog>
); 
```

请注意，我们可以访问在父组件中选择的完整用户对象。我们在这里访问它的`name`字段。

为简单起见，前两个动作简单地调用`alert()`。第三个选项名为“Cancel ”,它应该会关闭对话框。点击此项调用`cancelItem_onClick()`，出现如下:

```
cancelItem_onClick = () => {
  window.history.back();
}; 
```

当我们调用`window.history.back()`时，选择的用户从历史堆栈中移除，我们的`UserActionsDialog`组件将重新呈现并返回`null`。这将导致对话框被隐藏。

请注意，除了返回导航之外，我们不做任何取消操作。考虑到这一点，我们不需要做更多的事情来启用后退按钮。就是管用！事实上，在我们向后导航后，浏览器的前进按钮也将工作，对话框将重新打开…包括同一个选择的用户。

回到包含我们的`List`用户的父组件，让我们看看如何添加我们的`UserActionsDialog` :

```
// this goes after the list of all users
<Router>
  <UserActionsDialog path={this.props.location.pathname} />
</Router> 
```

请注意，我们将`UserActionsDialog`放在了`Router`组件中。这确保了`location`属性被传递到对话框中，这样我们就可以找到所选择的用户。您可以嵌套路由器，所以如果在组件树的更高层有另一个路由器也没关系。

正如我之前提到的，路由的`path`与其父路由相同。当选择用户时，我们不需要改变 URL，因为如果用户想要共享包含对话框的页面，这没有多大意义。因为当前状态对象存储用户，所以我们用它来决定对话框是打开还是关闭。

即使在使用某种路由器时，许多 web 应用程序也没有考虑到后退按钮应该以某种方式运行的所有情况。打开对话框来选择一个动作是一个常见的地方，用户可能会按下后退按钮来取消，你不希望你的应用程序意外地导航到不同的页面。在 Android 上，这尤其重要，因为全局后退按钮是用户体验的核心部分。然而，它在任何平台上的 web 浏览器中都一样有用，包括在桌面上。