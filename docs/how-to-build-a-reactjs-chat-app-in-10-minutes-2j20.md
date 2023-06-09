# 如何在 10 分钟内搭建一个 React.js 聊天 app

> 原文：<https://dev.to/scrimba/how-to-build-a-reactjs-chat-app-in-10-minutes-2j20>

 [![](img/ce6120aef675512ec7d4fa3779bf3123.png)](https://scrimba.com/g/greactchatkit?utm_source=dev.to&utm_medium=referral&utm_campaign=greactchatkit_10_minute_article) 
在本文中，我将向您展示使用 React.js 创建聊天
应用程序的最简单方法。它将完全不需要服务器端代码来完成，因为
我们将让 [Chatkit API](https://pusher.com/chatkit) 处理后端。

我假设您了解基本的 JavaScript，并且之前已经接触过一点 React.js。除此之外，没有任何先决条件。

> 注意:我还创建了一个关于如何创建 React.js 聊天应用程序的免费完整课程[:](https://scrimba.com/g/greactchatkit?utm_source=dev.to&utm_medium=referral&utm_campaign=greactchatkit_10_minute_article)

 [![https://scrimba.com/g/greactchatkit?utm_source=dev.to&utm_medium=referral&utm_campaign=greactchatkit_10_minute_article](img/d359ce88348672341a41c383d444ad17.png)](https://scrimba.com/g/greactchatkit?utm_source=dev.to&utm_medium=referral&utm_campaign=greactchatkit_10_minute_article) 
如果你按照本教程学习，最终你将拥有自己的聊天应用程序，如果你愿意，你可以进一步开发它。

我们开始吧！

### 第一步:将 UI 分解成组件

React 是围绕组件构建的，因此在创建应用程序时，首先要做的是将其 UI 分解成组件。

让我们从围绕整个应用程序画一个矩形开始。这是您的根组件，也是所有其他组件的共同祖先。姑且称之为`App`:

[![](img/1425a001446d755dbe51ef82b20be3ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8qufEfAI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A66jz6LtljJtOPDouK9PmYA.png)

一旦定义了根组件，您需要问自己以下问题:

> 该组件有哪些直接子组件？

在我们的例子中，给它三个子组件是有意义的，我们称之为:

*   `Title`
*   `MessagesList`
*   `SendMessageForm`

让我们为每一个画一个矩形:

[![](img/aac42747bede50b551f6c6e70055a780.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jg25wXNn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ASUeSr13iO7yJfIf4ipaeFg.png)

这让我们对应用背后的不同组件和架构有了一个很好的概述。

我们可以继续问自己，这些组件又有哪些孩子。因此，我们可以将 UI 分解成更多的组件，例如将每条消息转化成它们自己的组件。然而，为了简单起见，我们就此打住。

### 第二步:设置代码库

现在我们需要设置我们的存储库。我们将尽可能使用最简单的结构:一个* index.html *文件，它链接到一个 JavaScript 文件和一个样式表。我们还引进了 Chatkit SDK 和 Babel，用于改造我们的 JSX:

[![](img/174fa8cac37d24d116327fb8ef45f33a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dkNfS7Nz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AYCcPOlQGBk-dP-UQnyLEMA.png)

这里有一个 Scrimba 操场，上面有教程的最终代码。我建议你在一个新的标签页中打开它，当你感到困惑的时候就可以随意摆弄它。

[![](img/d543bf887fab7c4b4756a474adfff2b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pdrf5Yas--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Axmr7Z2oR1PwJvLq2sHuZbQ.png)

或者，您可以下载 Scrimba 项目的. zip 文件，并运行一个简单的[服务器，让它在本地启动并运行。](https://gist.github.com/willurd/5720255)

### 第三步:创建根组件

有了存储库，我们就可以开始编写一些 React 代码了，我们将在* index.js *文件中完成。

先说主要成分，`App`。这将是我们唯一的“智能”组件，因为它将处理数据和与 API 的连接。下面是它的基本设置(在我们添加任何逻辑之前):

```
 class App extends React.Component {

      render() {
        return (
          <div className="app">
            
            <MessageList />
            <SendMessageForm />
         </div>
        )
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，它简单地呈现了三个子组件:``、`<MessageList>`和`<SendMessageForm>`组件。

不过我们会让它变得复杂一点，因为聊天消息需要存储在这个`App`组件的*状态*中。这将使我们能够通过`this.state.messages`访问消息，从而将它们传递给其他组件。

我们将从使用虚拟数据开始，这样我们可以理解应用程序的数据流。然后，我们将在稍后用来自 [Chatkit](https://pusher.com/chatkit) API 的真实数据替换它。

让我们创建一个`DUMMY_DATA`变量:

```
 const DUMMY_DATA = [
      {
        senderId: "perborgen",
        text: "who'll win?"
      },
      {
        senderId: "janedoe",
        text: "who'll win?"
      }
    ] 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将这个数据添加到`App`的状态中，并将其作为道具传递给`MessageList`组件。

```
 class App extends React.Component {

      constructor() {
        super()
        this.state = {
           messages: DUMMY_DATA
        }
      }

      render() {
        return (
          <div className="app">
            <MessageList messages={this.state.messages}/>
            <SendMessageForm />
         </div>
        )
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们正在初始化`constructor`中的状态，并且我们还将`this.state.messages`向下传递给`MessageList`。

> 注意，我们在构造函数中调用了`super()`。如果您想创建一个有状态的组件，您必须这样做。

### 第四步:渲染虚拟消息

让我们看看如何在`MessageList`组件中呈现这些消息。下面是它的样子:

```
 class MessageList extends React.Component {
      render() {
        return (
          <ul className="message-list">                 
            {this.props.messages.map(message => {
              return (
               <li key={message.id}>
                 <div>
                   {message.senderId}
                 </div>
                 <div>
                   {message.text}
                 </div>
               </li>
             )
           })}
         </ul>
        )
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这就是所谓的傻逼成分。它需要一个道具`messages`，其中包含一个对象数组。然后我们简单地从对象中渲染出`text`和`senderId`属性。

随着我们的虚拟数据流入该组件，它将呈现以下内容:

[![](img/9d172a51bafee6454e9548a0f3c1055e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uz9V5iht--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ANf12vqc4Ti_GWY0FwqmQKw.png)

现在我们有了应用程序的基本结构，我们也能够呈现消息。干得好！

现在让我们用来自聊天室的真实消息替换我们的虚拟数据！

### 第五步:从聊天工具中获取 API 密钥

为了获取 fetch 消息，我们需要连接 Chatkit API。为此，我们需要获得 API 密钥。

在这一点上，我想鼓励你遵循我的步骤，这样你就可以启动并运行你自己的聊天应用程序。你可以使用我的 Scrimba 操场来测试你自己的 API 键。

首先在这里创建一个免费[账户。一旦你这样做了，你会看到你的仪表板。这是创建新的 Chatkit 实例的地方。创建一个并给它起一个您想要的名字:](https://pusher.com/chatkit#sign-up)

 [![](img/0e8dd64009a1d1c50d49dbbe17225fdd.png)](https://pusher.com/chatkit) 
然后您将被导航到您新创建的实例。这里您需要复制四个值:

*   实例定位器
*   测试令牌提供者
*   房间 id
*   用户名

我们将从**实例定位器**开始:

 [![](img/8407a5ed7a2df9a517636ff6b8acdbcd.png)
T4】](https://pusher.com/chatkit)

如果您向下滚动一点，您会发现**测试令牌提供者**:

[![](img/ed7d3dca32c0a63630bb6cda16044419.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zqHO6hDH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AuSvabQgYrppTGsWKXQsJSQ.png)

下一步是创建一个**用户** * *和一个* *房间* *，这是在同一个页面上完成的。请注意，您必须首先创建一个用户*，然后您将能够创建一个房间，这再次允许您访问房间标识符。*

[![](img/600131d7b843a9b6cb0e8bb4af1c5740.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5AUFyBxG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AhCXjDJ3PQJ_emU4WfJRQEQ.png)

现在你已经找到了你的四个标识符。干得好！

然而，在我们回到代码库之前，我希望您也从 Chatkit 仪表板手动发送一条消息，因为这将在下一章中对我们有所帮助。

下面是如何做到这一点:

[![](img/ede017a3310225439f1912385f2fcf7b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yp2nFFG_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AHU3mzUknYj8_MwY7ceK2Ow.png)

这是为了让我们实际上有一个消息在下一步中呈现出来。

### 第六步:渲染真实聊天消息

现在让我们回到我们的 *index.js* 文件，将这四个标识符作为变量存储在文件的顶部。

以下是我的，但我鼓励你创建自己的

```
 const instanceLocator = "v1:us1:dfaf1e22-2d33-45c9-b4f8-31f634621d24"

    const testToken = "https://us1.pusherplatform.io/services/chatkit_token_provider/v1/dfaf1e22-2d33-45c9-b4f8-31f634621d24/token"

    const username = "perborgen"

    const roomId = 9796712 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，我们终于准备好连接 Chatkit 了。这将发生在`App`组件中，更具体地说是在`componentDidMount`方法中。这是将 React.js 组件连接到 API 时应该使用的方法。

首先我们将创建一个`chatManager` :

```
 componentDidMount() {
      const chatManager = new Chatkit.ChatManager({
        instanceLocator: instanceLocator,
        userId: userId,
        tokenProvider: new Chatkit.TokenProvider({
          url: testToken
        })
     }) 
```

Enter fullscreen mode Exit fullscreen mode

…然后我们将执行`chatManager.connect()`来连接 API:

```
 chatManager.connect().then(currentUser => {
          currentUser.subscribeToRoom({
          roomId: roomId,
          hooks: {
            onNewMessage: message => {
              this.setState({
                messages: [...this.state.messages, message]
              })
            }
          }
        })
      })
    } 
```

Enter fullscreen mode Exit fullscreen mode

这使我们可以访问`currentUser`对象，它是与 API 交互的接口。

> 注意:因为我们稍后需要使用`currentUser`，所以我们通过执行`this.currentUser =` `currentUser`将它存储在实例中。

然后，我们调用`currentUser.subscribeToRoom()`并传递给它我们的`roomId`和一个`onNewMessage`钩子。

每次有新消息广播到聊天室，就会触发`onNewMessage`挂钩。所以每次发生这种情况时，我们只需在`this.state.messages`的末尾添加新消息。

这导致应用程序从 API 获取数据，然后将其呈现在页面上。

[![](img/fb5141d7a2a25f439993b0f080bd6d83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5LAUMl6c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AEAi9TyUba39xN3fciic3aA.png)

这太棒了，因为我们现在已经有了客户机-服务器连接的框架。

Woohoo!

### 第七步:处理用户输入

接下来我们需要创建的是`SendMessageForm`组件。这将是一个所谓的*受控组件*，意味着该组件通过其状态控制输入字段中呈现的内容。

看一看`render()`方法，并特别注意我已经突出显示的行:

```
 class SendMessageForm extends React.Component {
      render() {
        return (
          <form
            className="send-message-form">
            <input
              onChange={this.handleChange}
              value={this.state.message}
              placeholder="Type your message and hit ENTER"
              type="text" />
          </form>
        )
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们正在做两件事:

1.  用`onChange`事件监听器监听用户输入，这样我们就可以触发`handleChange`方法
2.  使用`this.state.message`显式设置输入字段的`value`

这两个步骤之间的联系可以在`handleChange`方法中找到。它只是将状态更新为用户在输入字段中键入的内容:

```
 handleChange(e) {
      this.setState({
        message: e.target.value
      })
    } 
```

Enter fullscreen mode Exit fullscreen mode

这触发了重新渲染，由于输入字段是使用`value={this.state.message}`从状态中显式设置的，输入字段将被更新。

因此，尽管当用户在输入字段中输入内容时，应用程序感觉是即时的，但是在 React 更新 UI 之前，数据实际上是经过状态的。

为了总结这个特性，我们需要给组件一个`constructor`。在其中，我们将初始化状态并在`handleChange`方法:
中绑定`this`

```
 constructor() {
        super()
        this.state = {
           message: ''
        }
        this.handleChange = this.handleChange.bind(this)
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们需要绑定`handleChange`方法，这样我们就可以访问其中的`this`关键字。这就是 JavaScript 的工作方式:默认情况下，`this`关键字在函数体内是未定义的。

### 第八步:发送消息

我们的`SendMessageForm`组件差不多完成了，但是我们还需要处理表单提交。我们需要获取信息并发送出去！

为此，我们将在`<form>`中用`onSubmit`事件监听器连接一个`handleSubmit`事件处理程序。

```
 render() {
        return (
          <form
            onSubmit={this.handleSubmit}
            className="send-message-form">
            <input
              onChange={this.handleChange}
              value={this.state.message}
              placeholder="Type your message and hit ENTER"
              type="text" />
        </form>
        )
      } 
```

Enter fullscreen mode Exit fullscreen mode

因为我们将输入字段的值存储在`this.state.message`中，所以实际上很容易将正确的数据与提交一起传递。我们将
简单地做:

```
 handleSubmit(e) {
      e.preventDefault()
      this.props.sendMessage(this.state.message)
      this.setState({
        message: ''
      })
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们调用了`sendMessage` prop，并将`this.state.message`作为参数传入。你可能对此有点困惑，因为我们还没有创建`sendMessage`方法。然而，我们将在下一节中这样做，因为该方法存在于`App`组件中。所以不用担心！

其次，我们通过将`this.state.message`设置为空字符串来清除输入字段。

这是整个`SendMessageForm`组件。注意，我们还将`this`绑定到了`handleSubmit`方法:

```
 class SendMessageForm extends React.Component {
      constructor() {
        super()
        this.state = {
          message: ''
        }
        this.handleChange = this.handleChange.bind(this)
        this.handleSubmit = this.handleSubmit.bind(this)
      }

      handleChange(e) {
        this.setState({
          message: e.target.value
        })
      }

      handleSubmit(e) {
        e.preventDefault()
        this.props.sendMessage(this.state.message)
        this.setState({
          message: ''
        })
      }

      render() {
        return (
          <form
            onSubmit={this.handleSubmit}
            className="send-message-form">
            <input
              onChange={this.handleChange}
              value={this.state.message}
              placeholder="Type your message and hit ENTER"
              type="text" />
          </form>
        )
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 第九步:发送消息到聊天工具

我们现在准备好了，请将消息发送到 Chatkit。这是在`App`组件中完成的，在那里我们将创建一个名为`this.sendMessage` :
的方法

```
 sendMessage(text) {
      this.currentUser.sendMessage({
        text: text,
        roomId: roomId
      })
    } 
```

Enter fullscreen mode Exit fullscreen mode

它接受一个参数(文本)并简单地调用`this.currentUser.sendMessage()`。

最后一步是将其作为道具传递给`<SendMessageForm>`组件:

```
 /* App component */

    render() {
      return (
        <div className="app">
          
          <MessageList messages={this.state.messages} />
          <SendMessageForm sendMessage={this.sendMessage} />
      )
    } 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们传递了处理程序，以便在提交表单时`SendMessageForm`可以调用它。

### 第十步:创建标题组件

最后，让我们创建标题组件。它只是一个简单的函数组件，意思是一个返回 JSX 表达式的函数。

```
 function Title() {
      return <p class="title">My awesome chat app</p>
    } 
```

Enter fullscreen mode Exit fullscreen mode

使用功能组件是一个很好的实践，因为它们比类组件有更多的约束，这使得它们不容易出错。

### 结果

有了它，你就有了自己的聊天应用程序，可以用来和朋友聊天！

[![](img/bf96a4e1aa4a54c4155f2fca1ce46e7c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wa1JJb1Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AKQzdlJJLMGyq5IdZu6cZ1Q.png)

如果你一直坚持到最后，给自己一点鼓励。

如果你想学习如何在这个例子的基础上更进一步，请点击这里查看我的免费课程，学习如何用 React 创建一个聊天应用。

感谢阅读和快乐编码:)