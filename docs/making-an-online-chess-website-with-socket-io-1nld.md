# 用 Socket.io 制作在线象棋网站

> 原文：<https://dev.to/aveeksaha/making-an-online-chess-website-with-socket-io-1nld>

使用套接字进行客户机服务器通信的教程。

如果你想要这个项目的代码，叉这个 GitHub repo - [`Online chess`](https://github.com/Aveek-Saha/Online-Chess)

查看这里的代码，自己玩- [`Live demo`](https://chess0.herokuapp.com)

# 简介

在本教程中，你将学习如何建立一个有多个房间的基本在线国际象棋平台，在这里人们可以互相对战。我们将使用套接字在客户机和服务器之间进行通信。

# 套接字. io

从 socket.io 文档中

> 插座。IO 是一个库，支持浏览器和服务器之间的实时、双向和基于事件的通信。

你需要知道的两个重要方法是-

`emit`方法发出一个事件，只要数据是 JSON 可编码的或二进制数据，就可以随该事件一起发送。

```
socket.emit('test', msg); 
```

Enter fullscreen mode Exit fullscreen mode

`on`方法监听一个事件，它可以接收已经发出的数据。

```
socket.on('test', (msg) => {
    console.log(msg);
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 文件结构

```
 chess
   |----chessboard // A folder for the chessboard.js library
   |
   |----img // Images for the chess pieces
   |
   |--game.js // The actual game
   |--index.js // The node server
   |
   |--full.html
   |--index.html
   |
   |--package.json 
```

Enter fullscreen mode Exit fullscreen mode

# 设置

创建一个新的项目文件夹，cd 到那个目录，然后运行

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

之后进入 [`Chessboard.js`](http://chessboardjs.com/) 下载最新版本。将内容提取到`chessboard`文件夹。将`img`文件夹从`chessboard`复制到根目录。我们将使用这个库来显示棋盘和棋子并与之交互。

现在我们将创建`index.html`和`game.js`文件。到 [`Chessboard.js`](http://chessboardjs.com/) 网站上这个 [`Highlight Legal Moves`](http://chessboardjs.com/examples#5003) 的例子。

为了检查有效的移动、棋子放置和检查/将死检测，我们将使用另一个名为 [`Chess.js`](https://github.com/jhlywa/chess.js) 的库。我们将通过 CDN 在`index.html`中包含此内容

将代码的 JavaScript 部分复制到`game.js`中。您的文件应该如下所示。

```
 game = new Chess();

var removeGreySquares = function () {
    $('#board .square-55d63').css('background', '');
};

var greySquare = function (square) {
    var squareEl = $('#board .square-' + square);
    var background = '#a9a9a9';
    if (squareEl.hasClass('black-3c85d') === true) {
        background = '#696969';
    }

    squareEl.css('background', background);
};

var onDragStart = function (source, piece) {
    // do not pick up pieces if the game is over
    // or if it's not that side's turn
    if (game.game_over() === true ||
        (game.turn() === 'w' && piece.search(/^b/) !== -1) ||
        (game.turn() === 'b' && piece.search(/^w/) !== -1)) {
        return false;
    }
};

var onDrop = function (source, target) {
    removeGreySquares();

    // see if the move is legal
    var move = game.move({
        from: source,
        to: target,
        promotion: 'q'
        // NOTE: always promote to a queen for example simplicity
    });

    // illegal move
    if (move === null) return 'snapback';
};

var onMouseoverSquare = function (square, piece) {
    // get list of possible moves for this square
    var moves = game.moves({
        square: square,
        verbose: true
    });

    // exit if there are no moves available for this square
    if (moves.length === 0) return;

    // highlight the square they moused over
    greySquare(square);

    // highlight the possible squares for this piece
    for (var i = 0; i < moves.length; i++) {
        greySquare(moves[i].to);
    }
};

var onMouseoutSquare = function (square, piece) {
    removeGreySquares();
};

var onSnapEnd = function () {
    board.position(game.fen());
};

var cfg = {
    draggable: true,
    position: 'start',
    onDragStart: onDragStart,
    onDrop: onDrop,
    onMouseoutSquare: onMouseoutSquare,
    onMouseoverSquare: onMouseoverSquare,
    onSnapEnd: onSnapEnd
};
board = ChessBoard('board', cfg); 
```

Enter fullscreen mode Exit fullscreen mode

接下来是`index.html`。

```
 <!doctype html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    Chess 0

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.2/chess.js"></script>
    <script src="./chessboard/js/chessboard-1.0.0.js"></script>
    <link rel="stylesheet" type="text/css" href="./chessboard/css/chessboard-1.0.0.min.css">

</head>

<body>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.1.1/socket.io.js"></script>

    <div class="container">
        <!-- The chess board -->
        <div id="board" style="width: 500px; margin: auto"></div>
    </div>

    <script src="./game.js"></script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果你只是双击`index.html`文件，你应该可以看到一个简单的棋盘和棋子。您可以四处移动棋子，将鼠标悬停在某个棋子上将显示您可以做出的可能移动。这是因为与为我们进行移动生成和有效移动检查的`Chess.js`的集成。

如果一个用户试图加入一个已经有两个玩家的游戏，我们会将他重定向到`full.html`，这只是一个基本的 html 页面，显示游戏已满。

```
 <!doctype html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    Room Full
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css"
        integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js"
        integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy"
        crossorigin="anonymous"></script>

</head>

<body style="margin: 4%">

    <div id="player" style="margin: auto">This room is full</div>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

# 套餐

对于这个项目，我们将只使用`Express`和`Socket.io`。要安装它们，运行

```
npm install --save express
npm install --save socket.io 
```

Enter fullscreen mode Exit fullscreen mode

# 服务器

创建`index.js`并需要以下模块。

```
const express = require('express');
const http = require('http');
const socket = require('socket.io'); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们要声明几个变量

```
 // use 8080 as the default port number, process.env.PORT is
//useful if you deploy to Heroku
const port = process.env.PORT || 8080

var app = express();

// start the server
const server = http.createServer(app)

// initialize a new instance of socket.io by passing the HTTP server object
const io = socket(server)

// keep track of how many players in a game (0, 1, 2)
var players;

// create an array of 100 games and initialize them
var games = Array(100);
for (let i = 0; i < 100; i++) {
    games[i] = {players: 0 , pid: [0 , 0]};
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的路由很简单，我们只需要在访问
时显示`index.html`

```
 // Add the static directory for our js and css files
app.use(express.static(__dirname + "/"));
app.get('/', (req, res) => {
    res.sendFile(__dirname + '/index.html');
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候监听服务器
上的`connection`事件了

```
 io.on('connection', function (socket) {

    // just assign a random number to every player that has connected
    // the numbers have no significance so it
    // doesn't matter if 2 people get the same number
    var playerId = Math.floor((Math.random() * 100) + 1)
    console.log(playerId + ' connected');

    // if a user disconnects just print their playerID
    socket.on('disconnect', function () {
        console.log(playerId + ' disconnected');
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 客户端

让我们把注意力转回到客户身上。我们将添加一个输入框，玩家在其中输入他想要加入的房间号，一旦他点击一个按钮，如果房间没有满，他就会加入房间。

在添加了这个和几个 div 来显示一些信息之后，我们添加了 bootstrap，最终的`index.html`看起来像这样:

```
<!doctype html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    Chess 0

    <!--  Adding bootstrap-->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css"
        integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js"
        integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy"
        crossorigin="anonymous"></script>

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.2/chess.js"></script>
    <script src="./chessboard/js/chessboard-1.0.0.js"></script>
    <link rel="stylesheet" type="text/css" href="./chessboard/css/chessboard-1.0.0.min.css">

    <!-- Adding some style -->
    <style>
        .number::-webkit-inner-spin-button,
        .number::-webkit-outer-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }

        #roomNumbers {
            margin: 7px;
        }

        body {
            margin: 4%
        }
    </style>

</head>

<body>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.1.1/socket.io.js"></script>

    <div class="container">
        <!-- The chess board -->
        <div id="board" style="width: 500px; margin: auto"></div>

        <div style="margin: auto"">
        <!-- Shows the player number and color -->
        <div id=" player"></div>
        <div id="roomNumbers">Enter a room number between 0 and 99</div>
        <form>
            <div class="row">
                <div class="col">
                    <input type="number" id="room" min="0" max="99" class="form-control form-control-md number">
                </div>
                <div class="col">

                    <!-- A button that connects the user to the given valid room number -->
                    <button id="button" class="btn btn-success" onclick="connect()">Connect</button>
                </div>
            </div>
        </form>

        <!-- Displays weather the game is in progress, or over -->
        <div id="state">Join Game</div>
    </div>

    </div>

    <script src="./game.js"></script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须修改`game.js`,以便它与服务器通信。初始化套接字后，我们可以通过前面提到的两种方法进行通信。

我们将通过添加一些变量来修改`game.js`。

```
 // creates a new chess game using the chess.js library
game = new Chess();
//initialize the socket
var socket = io();
// piece color
var color = "white";
// number of players in the current room
var players;
// the room number between 0 and 99
var roomId;
// if the both players have joined then it will be false
var play = true;

// For some DOM manipulation later
var room = document.getElementById("room")
var roomNumber = document.getElementById("roomNumbers")
var button = document.getElementById("button")
var state = document.getElementById('state') 
```

Enter fullscreen mode Exit fullscreen mode

# 更多服务器的东西

我们设置了客户端套接字，我们将能够在客户端和服务器之间进行通信。但是在这之前，我们还需要在这里设置一些东西，在
里面

```
io.on('connection', function (socket) {}) 
```

Enter fullscreen mode Exit fullscreen mode

我们将从客户端发送和接收一些事件，之后，我们也将在客户端设置发送器和接收器。

我们在这里使用的 for 服务器基本上是将消息从一个客户端传递到另一个客户端。所以在大多数的`socket.on()`函数回调中，你会看到一个`socket.emit`函数，这样服务器就可以将事件传递给其余的客户端。因为服务器将向所有客户端发出事件。客户会知道这个事件是否是针对他们的，因为我们在消息中传递了`roomId`。在客户端检查`roomId`。这种方法不安全，但是对于我们的小应用程序来说很好。

这部分在 playerID 生成后添加到`index.js`中的`io.on('connection')`回调中。我们还将在`disconnect`上增加一个新功能，当玩家断开连接时释放房间空间。

```
 var color; // black or white

// 'joined' is emitted when the player enters a room number and clicks
// the connect button the room ID that the player entered gets passed as a message

socket.on('joined', function (roomId) {
    // if the room is not full then add the player to that room
    if (games[roomId].players < 2) {
        games[roomId].players++;
        games[roomId].pid[games[roomId].players - 1] = playerId;
    } // else emit the full event
    else {
        socket.emit('full', roomId)
        return;
    }
    console.log(games[roomId]);
    players = games[roomId].players
    // the first player to join the room gets white
    if (players % 2 == 0) color = 'black';
    else color = 'white';

    // this is an important event because, once this is emitted the game
    // will be set up in the client side, and it'll display the chess board
    socket.emit('player', {
        playerId,
        players,
        color,
        roomId
    })

});

// The client side emits a 'move' event when a valid move has been made.
socket.on('move', function (msg) {
    // pass on the move event to the other clients
    socket.broadcast.emit('move', msg);
});

// 'play' is emitted when both players have joined and the game can start
socket.on('play', function (msg) {
    socket.broadcast.emit('play', msg);
    console.log("ready " + msg);
});

// when the user disconnects from the server, remove him from the game room
socket.on('disconnect', function () {
    for (let i = 0; i < 100; i++) {
        if (games[i].pid[0] == playerId || games[i].pid[1] == playerId)
            games[i].players--;
    }
    console.log(playerId + ' disconnected');

}); 
```

Enter fullscreen mode Exit fullscreen mode

# 回客户端

在设置我们的服务器来来回回转发来自客户机的消息之后，我们设置客户机来响应服务器发出的事件。所以让我们回到`game.js`。

我们要做的第一件事是为“玩家”事件设置一个监听器，这意味着一个玩家已经加入了该事件，棋盘将被设置，还有玩家的颜色。我们在第一步中编写的创建电路板的代码也放在这里。

在去掉旧的`cfg`和`board`变量后，把这个放在`game.js`的底部。

```
 socket.on('player', (msg) => {

    var plno = document.getElementById('player')

    // we're passing an object -
    // { playerId, players, color, roomId } as msg
    color = msg.color;

    // show the players number and color in the player div
    players = msg.players;
    plno.innerHTML = 'Player ' + players + " : " + color;

    // emit the play event when 2 players have joined
    if (players == 2) {
        play = false;
        // relay it to the other player that is in the room
        socket.emit('play', msg.roomId);
        // change the state from 'join room' to -
        state.innerHTML = "Game in Progress"
    }
    // if only one person is in the room
    else
        state.innerHTML = "Waiting for Second player";

    var cfg = {
        orientation: color,
        draggable: true,
        position: 'start',
        onDragStart: onDragStart,
        onDrop: onDrop,
        onMouseoutSquare: onMouseoutSquare,
        onMouseoverSquare: onMouseoverSquare,
        onSnapEnd: onSnapEnd
    };
    board = ChessBoard('board', cfg);
});

var board; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要修改我们从 [`chessboardjs`](http://chessboardjs.com/examples#5003) 的例子中复制的一些函数。

对于`onDragStart`,我们增加了一些条件来检查移动是否有效。我们添加的条件是-

```
play || // check if both players have joined
// if the player is white, he cannot move black pieces and vice versa
(game.turn() === 'w' && color === 'black') ||
(game.turn() === 'b' && color === 'white') 
```

Enter fullscreen mode Exit fullscreen mode

```
 var onDragStart = function (source, piece) {

    // A few more rules have been added
    if (game.game_over() === true || play ||
        (game.turn() === 'w' && piece.search(/^b/) !== -1) ||
        (game.turn() === 'b' && piece.search(/^w/) !== -1) ||
        (game.turn() === 'w' && color === 'black') ||
        (game.turn() === 'b' && color === 'white')) {
        return false;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

功能`onDrop`是指棋子实际移动的时间。因此，当玩家移动并放弃移动时，就会发出“移动”事件。

```
 var onDrop = function (source, target) {
    removeGreySquares();

    // see if the move is legal
    var move = game.move({
        from: source,
        to: target,
        promotion: 'q'
    });
    if (game.game_over()) {
        state.innerHTML = 'GAME OVER';
        socket.emit('gameOver', roomId)
    }

    // illegal move
    if (move === null) return 'snapback';

    // if the move is allowed, emit the move event.
    else
        socket.emit('move', {
            move: move,
            board: game.fen(),
            room: roomId
        });
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要添加一些侦听器，以便通过事件发送的更改能够在客户端得到反映。

```
 // if the room is full (players > 2), redirect the user
// to the full.html page we made earlier
socket.on('full', function (msg) {
    if (roomId == msg)
        window.location.assign(window.location.href + 'full.html');
});

// change play to false when both players have
// joined the room, so that they can start playing
// (when play is false the players can play)
socket.on('play', function (msg) {
    if (msg == roomId) {
        play = false;
        state.innerHTML = "Game in progress"
    }
});

// when a move happens, check if it was meant for the clients room
// if yes, then make the move on the clients board
socket.on('move', function (msg) {
    if (msg.room == roomId) {
        game.move(msg.move);
        board.position(game.fen());
        console.log("moved")
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后一个方法需要实现，即`connect`函数。当我们单击 connect 按钮时，它需要发出一个事件，该事件包含玩家输入的房间 id。

```
 var connect = function () {

    // extract the value of the input field
    roomId = room.value;
    // if the room number is valid
    if (roomId !== "" && parseInt(roomId) <= 100) {
        room.remove();
        roomNumber.innerHTML = "Room Number " + roomId;
        button.remove();

        // emit the 'joined' event which we have set up a listener for on the server
        socket.emit('joined', roomId);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 检查是否工作

如需完整代码，请查看回购， [`Online chess`](https://github.com/Aveek-Saha/Online-Chess) 。

cd 到项目目录，然后运行

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

如果你做的一切都正确，你会在这里看到类似这样的 [`Live demo`](https://chess0.herokuapp.com) 。你可以打开尽可能多的实例，试着和你的朋友一起玩。