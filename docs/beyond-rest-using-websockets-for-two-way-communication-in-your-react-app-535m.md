# 超越 REST:在 React 应用中使用 WebSockets 进行双向通信

> 原文：<https://dev.to/bnevilleoneill/beyond-rest-using-websockets-for-two-way-communication-in-your-react-app-535m>

[![](../Images/bc0ed25793c59cdf3b0c0124c6d625b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0G58u2_n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7bp-ucN-bLMdRLWirrhU3A.png)

### 概述

REST 永远是 web APIs 无可争议的冠军。它抛弃了 SOAP 及其冗长的 XML，转而支持比 HTTP 更简洁的 JSON。REST 也有一个基于资源和动词的非常复杂的概念模型。但是，休息并不是解决所有问题的完美方法。开发人员竭尽全力，试图将每一种通信和数据交换模式融入 REST 范式。

最近，潮流发生了转变，出现了几个新的 API 并获得了广泛的关注:WebSockets、GraphQL 和 http2/ + gRPC。

这篇文章是关于 WebSockets 的。您将了解 WebSockets 是什么，它们有什么好处，甚至可以构建一个很酷的客户端-服务器游戏，在后端使用 Node，在前端使用 React+SVG，服务器和客户端之间的所有通信都在 WebSockets 上运行。

### 什么是 WebSockets？

WebSockets 是一种基于连接的通信协议。

你会问，这有什么大不了的？我们从一开始就有了 TCP。

确实如此，但是 TCP 在浏览器中并不适用于 web 开发人员。WebSockets 在浏览器中工作。在 WebSockets 出现之前，你只能做 HTTP 请求-响应。WebSockets 是基于消息的。也就是说你发出一条信息，对方收到一条信息。

注意，WebSockets 是在 TCP 之上实现的。但是，原始 TCP 是基于流的。你发送一串字节(八位字节),另一端必须找出如何累积它们，并把它们分解成连贯的消息。WebSockets 为您做到了这一点，这是一大福音。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### 谁需要 WebSockets？

当服务器想要推送大量数据和/或频繁更新浏览器时(想想多人游戏或聊天服务)，WebSockets 真的很有用。考虑一个游戏，其中一个玩家执行的每个操作都必须尽快传达给其他玩家。如果你试图用 REST 来实现它，你可能会求助于一些轮询机制，所有玩家不断地用更新请求轰炸服务器。

这个场景有几个问题:

*   服务器必须处理大量的请求，即使它没有什么可报告的
*   如果玩家变得更好并且不经常投票，滞后将会存在并增长
*   服务器必须保持最近的状态，直到所有玩家都得到通知，并提出一个解决方案来与客户端协调版本控制
*   如果一个客户端掉线，服务器没有好的方法知道它

有了 WebSockets，所有这些问题都迎刃而解。服务器在控制中。它确切地知道每次有多少客户端被连接。当有重要的事情发生时，它可以立即更新所有连接的客户端。没有滞后。一旦通知了所有客户端，服务器就不需要保持状态。如果客户端掉线，连接也会掉线，服务器会立即得到通知。

聊天服务是 WebSockets 的杀手级应用。如果没有 WebSockets(在浏览器中)，逐字符更新甚至只是消息:“X 正在键入…”都是不可能的。

### connect 4 演示应用

让我们构建一个 Connect4 游戏来演示 WebSockets 是如何工作的。这很简单。将会有一个管理游戏状态的服务器和两个互相对战的玩家。服务器负责管理棋盘，确保玩家只进行有效的移动，告诉每个玩家何时轮到他们，检查胜利条件并再次通知玩家。

客户端是一个基于 React 的应用程序。它显示来自服务器的公告板和消息。当服务器发送更新的公告板或消息时，客户端只需更新其状态，React 负责更新显示。当轮到玩家时，客户端还负责响应板上的点击，并通知服务器。

Connect4 游戏根本没有优化。我发送大量冗余信息(例如，每次发送整个板，而不仅仅是更改)，我发送多个消息，即使它们可以组合在一起。这是故意的。我打算展示 WebSockets 是多么简单和用户友好。代码非常简单易懂。唯一有点复杂的部分是检查胜利，它被隔离在服务器端自己的功能中。

它看起来是这样的:

[![](../Images/db96bba4eb8940c39c38b473d27cc921.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--14vxFkKF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALmESDROZayX8SePRZl1cyQ.png)

### 代码

在深入研究代码之前，让我们快速了解一下在哪里可以找到它以及代码是如何构造的。服务器和客户端的代码都可以在 gitlab 上找到:[https://gitlab.com/the-gigi/connect4](https://gitlab.com/the-gigi/connect4)。

整个服务器位于一个文件中: [server.js](https://gitlab.com/the-gigi/connect4/blob/master/server.js)

客户端是使用 [create-react-app](https://github.com/facebook/create-react-app) 创建的，它创建自己的目录结构。我把所有东西都移到了客户端子目录中。

有两个自述文件。README.md 是一个简洁的文件，对该应用程序及其使用方法进行了简短描述。README2.md 是由 create-react-app 生成的文件，它包含了许多关于使用 create-react-app 启动项目的好处的信息。

### 构建 Connect4 服务器

服务器是一个使用 [socket.io](https://socket.io) 作为 WebSockets 服务器的 [node.js](https://nodejs.org) 应用程序。启动一个监听端口 1337 的 WebSockets 服务器所需要的一切:

```
const io = require('socket.io')()
.
.
.
reset()
const port = 1337
io.listen(port)
console.log('Listening on port ' + port + '...') 
```

服务器超级简单。它一次只能运行一个游戏。游戏状态包括游戏板、两个玩家以及当前玩家是谁。该板是一个 6×8 的 2D 阵列，其中所有单元最初都是白色的。我选择用一个具有“红色”和“黄色”两个属性的物体来代表玩家。这里不需要映射，因为键是字符串，我不需要迭代这些键。每个玩家的值是他们的 WebSocket，最初为空。

```
let board = null
const players = {'red': null, 'yellow': null}
let player = 'red'

function reset() {
  board = Array(6).fill(0).map(x => Array(8).fill('white'))
  players['red'] = null
  players['yellow'] = null
  player = 'red'
} 
```

为什么要保存玩家的对象而不是两个变量？字符串“红色”和“黄色”始终用于在服务器和客户端之间来回传递重要信息。

好的。该说沟通了。在服务器端，一切都发生在`io.on('connection', function(socket) {...}`内部。每当客户端连接时，都会调用这个回调函数。

服务器为各种事件和消息注册回调，我将很快介绍。但是，首先服务器将套接字存储在 players 对象中。第一个连接的客户端是红色玩家，黄色是第二个。服务器将残酷无情地断开任何进一步的连接尝试。服务器也将发送给每个玩家他们的颜色和轮到谁了。

```
if (players['red'] == null) {
    players['red'] = socket
    socket.emit('color', 'red')
  } else if (players['yellow'] == null) {
    players['yellow'] = socket
    socket.emit('color', 'yellow')
    io.emit('turn', 'red')
  } else {
    socket.disconnect()
  } 
```

用于向客户端发送消息的 **emit()** 函数有两种风格。 **io.emit()** 调用让服务器向所有连接的服务器广播相同的消息，而 socket.emit()调用将消息发送给特定的客户端。例如，每个玩家需要得到不同的信息来知道他们的颜色，但是所有玩家都需要得到相同的信息来告诉他们该轮到谁了。

然后，服务器继续为两个事件注册回调:“断开连接”和“单击”。“断开”事件不是很有趣，只是从玩家的对象中移除了断开玩家的插座。

“点击”事件是所有动作发生的地方。当活跃的玩家点击棋盘上的一列时，服务器接收到“点击”事件并开始工作。首先，服务器验证点击是有效的。它忽略了以下情况下的点击:不按顺序点击、全栏点击(顶部位置已被占用)和只有一个玩家连接时的点击(如果没有人连接，则没有人可以点击)。

```
socket.on('click', function (column) {
    // Ignore players clicking when it's not their turn
    if (players[player] !== socket) {
      return
    }

// Ignore clicks on full columns
    if (board[0][column] !== 'white') {
      return
    }

// Ignore clicks before both players are connected
    if ((players['red'] == null) || (players['yellow'] == null)) {
      return
    } 
```

一旦这个令人痛苦的部分完成，服务器知道这是一个正确的点击，并继续处理它。然后，服务器在目标栏的顶部放置一个新棋子，并通过“棋盘”消息将更新后的棋盘发送给所有玩家:

```
// find first open spot in the column
    let row = -1
    for (row = 5; row >= 0; --row) {
      if (board[row][column] === 'white') {
        board[row][column] = player
        break
      }
    }

io.emit('board', board) 
```

现在，服务器需要检查当前玩家是否真的通过放置该棋子而获胜。它用当前放置的棋子的位置调用 **checkVictory()** ，如果返回 true，则表示当前玩家赢了。服务器用获胜玩家的颜色向双方玩家广播“胜利”消息，断开双方玩家的连接并退出。

但是，如果玩家没有赢，玩家切换当前玩家，并以“回合”消息通知两个玩家。

```
// Check victory (only current player can win)
    if (checkVictory(row, column)) {
      io.emit('victory', player)
      // Disconnect players
      players['red'].disconnect()
      players['yellow'].disconnect()
      reset()
      return
    }

// Toggle the player
    player = player === 'red' ? 'yellow' : 'red'
    io.emit('turn', player) 
```

### 检查胜利

服务器最复杂的部分是胜利检查。这不是火箭科学，但是如果你不小心的话，你很容易错过一些关键案例。让我们稍微讨论一下，然后看看一些代码。

要赢，玩家必须将四个相邻的棋子水平、垂直或对角对齐。如果一个玩家在一个回合中获胜，那么刚刚放置的棋子必须是 4 个相邻棋子的一部分。简单的方法是从最后放置的棋子开始，然后在 8 个方向的每一个方向检查玩家颜色的另外 3 个相邻的棋子。

但是，您可能会忽略放置的棋子位于序列中间的情况，如下图所示:

[![](../Images/f52b4e872777d552daddf4652f801c1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---2j14yIi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/680/1%2AMJ4EUyazlsvfqkEskQEiCA.png) 

<figcaption>黄色即将获胜！</figcaption>

所以，正确的检查方法是双向进行，用玩家的颜色数出棋子总数。例如，当检查水平方向时，我们检查左侧和右侧。

这意味着我们只需要检查四个方向:水平、垂直、左上到右下对角线和左下到右上对角线。我们还需要注意，不要超出我们的检查范围。下面是水平检查的部分代码:

```
function checkVictory(i, j) {
  const c = board[i][j]

// Check horizontally
  let count = 0
  // count to the left
  for (let k = 1; k < 4; ++k) {
    if (j - k < 0) {
      break
    }
    if (board[i][j - k] !== c) {
      break
    }
    count++
  }
  // count to the right
  for (let k = 1; k < 4; ++k) {
    if (j + k > 7) {
      break
    }
    if (board[i][j + k] !== c) {
      break
    }
    count++
  }

if (count > 2) {
    return true
  } 
```

我们向左向右最多数三个地方，遇到任何不是当前玩家颜色的东西就破。最后，如果计数大于 2，这意味着我们有一个 4 的序列(包括当前放置的棋子)，这是一个胜利。

垂直和对角线的检查非常相似，只是索引略有不同，在对角线的情况下，I 和 j 都增加。

在这里查看:[https://git lab . com/the gigi/connect 4/blob/master/server . js # L15](https://gitlab.com/the-gigi/connect4/blob/master/server.js#L15)

### 在协议设计上

WebSockets 很牛逼，不过还是说说协议设计吧。客户端连接并向服务器发送“点击”消息。服务器发送多种信息，如“棋盘”、“颜色”和“回合”。

真的有必要吗？不完全是。

服务器可以发送包含所有内容的单个状态消息。但是，如果您只发送一条消息，那么客户端代码将会更加复杂，必须解析并找出发生了什么变化。

“board”消息提供了另一个决策点。我发送整个棋盘，但是我也可以很容易地发送最近放置的棋子的位置。

如果是这种情况，那么客户端将不得不保留该板，并在接收到新放置的块上的消息时正确地更新它，而不是只接收整个板。在这种情况下没什么大不了的。

### 构建 Connect4 客户端

客户端是一个 React 应用程序，所有操作都发生在 App.js 主组件中。它还有两个子组件:board 和 infobar。

#### 信息栏组件

InfoBar 是一个无状态的功能组件，只是用播放器的颜色显示一些信息。它有一些嵌入的样式，它从它的父节点接收消息和颜色作为属性:

```
import React from 'react'

const InfoBar = ({message, color}) => {
  let style = {color: color, 
               backgroundColor: 'black', 
               padding: '5px'};
  return <p style={style}>{message}</p>
}

export default InfoBar 
```

#### 板子组件

棋盘有趣多了。它必须处理点击，但它也是一个无状态的功能组件，对服务器或 WebSockets 一无所知。

它是如何工作的？

父节点传递一个名为 onColumnClick 的回调函数作为属性。董事会简单地用点击的列调用这个回调。棋盘的另一个很酷的地方是它使用 [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG) 来渲染棋盘和棋子。此外，它还根据玩家的回合改变鼠标指针。让我们一点一点地分解它。

董事会接受来自父母的 3 个道具。“棋盘”道具是您在服务器中已经熟悉的 6x8 2D 阵列。“onColumnClick”属性是在单击列时调用的回调，并且“yourTurn”是一个布尔值。

接下来，它为稍后将被填充的单元格定义一个空数组，并根据 yourTurn 属性将鼠标光标设置为指针或不放。

```
import React from 'react'

const Board = ({board, onColumnClick, yourTurn}) => {
  const cells = []
  const style = {cursor: yourTurn? 'pointer' : 'no-drop'} 
```

这里，我们用电路板单元填充单元阵列。每个单元格是一个 SVG 组，有一个 50x50 的蓝色矩形，中间有一个圆。圆圈的颜色来自棋盘道具，可以是红色、黄色或白色。

```
for (let i = 0; i < 6; ++i) {
  for (let j = 0; j < 8; ++j) {
    let cell =  onColumnClick(j)} 
                style={style}>

    cells.push(cell)
  }
} 
```

最后，我们返回一个 440x360 **svg** 元素，上面的单元格代表棋盘，后面是一个蓝色的梯形多边形作为基础。

```
return 
          {cells}
          <polygon points="20,300 0,360 440,360 420,300"
          fill={'blue'}/>
       
}
export default Board 
```

#### App 组件

该应用程序是主要组件，它负责呈现信息栏和板组件，以及处理所有的通信。它还使用了 App.css 中的一点 css。为了与服务器通信，它使用了 socket.io 客户端库，该库提供了 openSocket()函数。

```
import React, {Component} from ‘react’
import ‘./App.css’
import InfoBar from ‘./components/InfoBar’
import Board from ‘./components/Board’
import openSocket from ‘socket.io-client’; 
```

构造函数设置状态，包括:

*   板(与服务器的表示相同)
*   连接到服务器的套接字
*   一条消息总是显示在**信息栏**中
*   轮到你的布尔运算

```
class App extends Component {
  constructor(props) {
    super(props)

    this.state = {
      board: Array(6).fill(0).map(x => Array(8).fill('white')),
      socket: openSocket('http://localhost:1337'),
      message: 'Waiting for another player...',
      yourTurn: false
    } 
```

下一部分(仍在构造函数中)是所有通信发生的地方。首先，this 指针存储为闭包中的 self 变量。这是 WebSockets 访问组件状态所必需的。

然后，应用程序构造函数为以下消息注册处理程序:“棋盘”、“颜色”、“回合”和“胜利”。在每种情况下，它更新状态的相关部分。请注意，处理程序是在构造函数中注册的，但是在游戏进行的过程中会被调用。

```
let self = this
    this.state.socket.on('board', board => {
      this.setState(...self.state, {board: board})
    });
    this.state.socket.on('color', color => {
      this.setState(...self.state, {color: color})
    });
    this.state.socket.on('turn', player => {
      if (player === this.state.color) {
        this.setState(...self.state, 
                      {message: "You're up. What's your move?", 
                       yourTurn: true})
      } else {
        this.setState(...self.state, 
                      {message: player + ' is thinking...', 
                       yourTurn: false})
      }
    });

this.state.socket.on('victory', player => {
      let newState = {yourTurn: false}
      if (player === this.state.color) {
        newState['message'] = 'You win!'
      } else {
        newState['message'] = 'You lose!'
      }
      this.setState(...self.state, newState)
    });
  } 
```

还记得当一列被点击时，面板接收到的 onColumnClick 函数吗？这是一个只向服务器发送“点击”消息的一行程序。

```
onColumnClick = column => this.state.socket.emit('click', column); 
```

render()函数非常简单。它呈现标题，然后是信息栏，然后是从状态传递必要属性的面板。

```
render() {
    return (
      <div className="App">
        <header className="App-header">
          <h1 className="App-title">Connect Four</h1>
        </header>
        <InfoBar color={this.state.color} 
                 message={this.state.message} />
        <Board board={this.state.board} 
               onColumnClick={this.onColumnClick} 
               yourTurn={this.state.yourTurn}/>
      </div>
    )
  } 
```

这是一个带有胜利之箭的插图:

[![](../Images/cc3afba581d3caa531489f75ddcdb201.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RCYEP8JY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/725/1%2AIB8nVY-eIOMBqkrpyrBsuw.png)

### 结论

WebSockets 对于客户端-服务器应用程序来说是一项伟大的技术，在这种应用程序中，服务器需要向客户端发送消息、事件或通知，而不需要不断地被提示或轮询。

在这篇文章中，我们浏览了一个节点服务器和 React 客户端的完整示例，它们通过 WebSockets 进行通信，并一起实现了经典的 Connect4 游戏。作为奖励，我们使用 SVG 来渲染棋盘和棋子。

现在，是时候走出去，用 WebSockets 构建令人敬畏的东西了。

* * *

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[反应化合物成分快速指南](https://blog.logrocket.com/guide-to-react-compound-components-9c4b3eb482e9/)首先出现在[博客](https://blog.logrocket.com)上。