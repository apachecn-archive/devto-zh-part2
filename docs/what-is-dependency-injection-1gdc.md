# 什么是依赖注入？

> 原文：<https://dev.to/ellehallal/what-is-dependency-injection-1gdc>

<center>

### 最初发布于 [ellehallal.dev](https://ellehallal.dev) 👩🏽‍💻

</center>

* * *

这是一篇关于我如何使用依赖注入来改进我的 JavaScript 井字游戏的设计的博客。

在下面的例子中，当初始化`Game`类时，`Board`和`Player`类的实例也被创建。

```
class Game {
  constructor() {
    this.board = new Board();
    this.player1 = new HumanPlayer();
    this.player2 = new HumanPlayer();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想在游戏中添加一个新类型的玩家(`ComputerPlayer`)作为选项，上面的代码会让我们很难做到这一点。这是因为播放器类型是“硬编码”在`Game`的构造函数中的。这就是依赖注入的用武之地。

* * *

## 什么事？

> 在软件工程中，依赖注入是一种技术，其中一个对象(或静态方法)提供另一个对象的依赖。
> —维基百科

* * *

让我们把这个应用到我们的例子中。不用“硬编码”要在`Game`的构造函数中初始化的类，我们可以将它们传入。

```
class Game {
  constructor(board, player1, player2) {
    this.board = board;
    this.player1 = player1;
    this.player2 = player2;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们初始化一个新的`Game`实例时，我们可以传递我们的参数。

```
 const board = new Board(['o', 'x', 'x', 'x', 'o', 'o', 'o', 'x', 'x'])
const humanPlayer = new HumanPlayer();
const computerPlayer = new ComputerPlayer();
const game = new Game(board, humanPlayer, computerPlayer) 
```

Enter fullscreen mode Exit fullscreen mode

因此，这提高了我们使用`Game`的依赖项的灵活性。例如，现在我们可以用两个人类玩家，或者两个电脑玩家，或者一个一个来初始化这个类！

* * *

*   [Github 上的 JavaScript Tic Tac Toe Repo](https://github.com/ellehallal/js-tic-tac-toe)
*   [在 Heroku 上井字游戏](http://this-tic-tac-toe.herokuapp.com/)