# 解开克里特岛迷宫

> 原文：<https://dev.to/alexbender/solving-the-cretan-maze-1p7a>

# 那么我们来玩游戏吧

最近 [Leonora Der](https://dev.to/leonorader) 发表了伟大的作品:[解开克里特岛迷宫](https://dev.to/leonorader/solve-the-cretan-maze-2fdg)。
这个挑战真的很有趣，因为它不需要你写很多代码，但解决和思考它仍然很有趣。额外感谢设计:简单而伟大。所以我决定为这个游戏实现基本的界面，当然是用 Python 写的。
我的第一个想法是写这样的东西:

```
...
class MazeRunner():

    def __init__(self, username, password):
        # For BasicHTTPAuth
        self.username = username
        self.password = password

    def create_maze(self):
        """POST - /api/mazes

        Will let you start the maze
        """

    def start_loop(self):
        ...
... 
```

Enter fullscreen mode Exit fullscreen mode

我甚至做到了。写了十几个方法，大部分还没有实现，但它们只是存根，你知道的。它涵盖了提供的所有 API。几天后，我开始在`pdb`周围挖掘，然后是它的母公司`bdb`，然后回到`pdb`。我注意到一些有趣的事情:这里有一段`Pdb`类的源代码，文件`Lib/pdb.py`

```
...

line_prefix = '\n-> '   # Probably a better default 
class Pdb(bdb.Bdb, cmd.Cmd):

    _previous_sigint_handler = None

... 
```

Enter fullscreen mode Exit fullscreen mode

等等，那是什么？第二个父类。是`cmd`。所以像往常一样，Python 标准库解决了我的一大部分问题。我开始看这个模块上的文档: [cmd](https://docs.python.org/3/library/cmd.html) 。看起来我已经使用这个接口超过五年了，甚至没有注意到:Python 调试器像`pdb`、`ipdb`、`pudb`..这种常见的 REPL 随处可见。

```
>>> import pdb
>>> pdb.set_trace()
--Return--
> <stdin>(1)<module>()->None
(Pdb) ?

Documented commands (type help <topic>):
========================================
EOF    bt         cont      enable  jump  pp       run      unt   
a      c          continue  exit    l     q        s        until 
alias  cl         d         h       list  quit     step     up    
args   clear      debug     help    n     r        tbreak   w     
b      commands   disable   ignore  next  restart  u        whatis
break  condition  down      j       p     return   unalias  where 

Miscellaneous help topics:
==========================
exec  pdb

Undocumented commands:
======================
retval  rv

(Pdb) 
```

Enter fullscreen mode Exit fullscreen mode

## 还是写点有用的吧

我们要解决什么(我们需要什么？)

*   实现所有 API 调用
*   把它们都记录下来
*   让用户清楚该做什么(再次记录并提供帮助)

为了实现`cmd`接口，我们必须知道一些事情:

*   主要功能是`cmdloop`。

```
From the documentation:

Cmd.cmdloop(intro=None)

Repeatedly issue a prompt, accept input,
parse an initial prefix off the received input,
and dispatch to action methods, passing them the
remainder of the line as argument. 
```

Enter fullscreen mode Exit fullscreen mode

*   命令处理程序是名为`do_<command-name>`的方法

```
An interpreter instance will recognize a command name foo if and only
if it has a method do_foo() 
```

Enter fullscreen mode Exit fullscreen mode

*   为了给这个命令提供帮助，我们必须实现`help_<command-name>`方法
*   标准的`do_help`方法调用所有的`help_*`方法。
*   命令有前后处理程序:`precmd`和`postcmd`方法。

```
 Cmd.precmd(line)

    Hook method executed just before the command line line is interpreted, but
after the input prompt is generated and issued. This method is a stub in Cmd;
it exists to be overridden by subclasses. The return value is used as
the command which will be executed by the onecmd() method; the precmd()
implementation may re-write the command or simply return line unchanged.

Cmd.postcmd(stop, line)

    Hook method executed just after a command dispatch is finished.
This method is a stub in Cmd; it exists to be overridden by subclasses.
line is the command line which was executed, and stop is a flag which
indicates whether execution will be terminated after the call to postcmd();
this will be the return value of the onecmd() method. The return value of
this method will be used as the new value for the internal flag which
corresponds to stop; returning false will cause interpretation to continue. 
```

Enter fullscreen mode Exit fullscreen mode

## 好了，说够了

因为比起其他语言我更喜欢使用 Python，所以我将把我的`cmd`类的名字改为 MazeCrawler，因为 snakes 不运行，对吗？但还是有点无聊。我要更进一步，把它重命名为(关于费城总是阳光明媚):[mazek roller](http://itsalwayssunny.wikia.com/wiki/Night_Crawlers)T3】

```
import cmd                                                                                                                                                                                                   

class MazeKroller(cmd.Cmd):
    """Interface for walking through the maze

    https://coding-challanges.herokuapp.com/challanges/maze
    """                                                                                                                                                  

    intro = "Welcome to Maze shell. Type `?' or `help' to list commands.\n"
    prompt = '~ '  # Because it's a snake! 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只有一个方法 init。我们必须实现剩下的部分。
我们需要能够:

*   *创建迷宫*，
*   *浏览它*
*   并且*拿起硬币*获胜(实际上是为了从迷宫中走出来)。为了可用性，还应该实现两个方法:*获取信息*和*放弃*。这是它的存根:

```
class MazeKroller(cmd.Cmd):
    """Interface for crawling through the maze

    https://coding-challanges.herokuapp.com/challanges/maze
    """
    intro = "Welcome to Maze shell. Type `?' or `help' to list commands.\n"
    prompt = "~ "

    def do_init(self, user, password):
        """Credentials for BasicHTTPAuth."""
        self.user = user
        self.password = password

    def do_create_maze(self, size):
        """Create a new maze.
        Now you have 3600 seconds to escape.

        POST /api/mazes\n"""

    def do_info(self, maze_id):
        """Get some general data about your maze

        GET - /api/mazes/{id}\n""" 

    def do_get_cells(self, maze_id):
        """See the cells around you.

        GET - /api/mazes/{id}/steps\n"""

    def do_get_coin(self):
        """Pick up a coin.

        POST - /api/mazes/{id}/coins\n"""

    def do_giveup(self):
        """Gives up the game.

        DELETE /api/mazes/{id}\n""" 
```

Enter fullscreen mode Exit fullscreen mode

所以这里我们有一个基本的类。这个类现在什么都不做，但是可以用
实例化

```
MazeKroller().cmdloop() 
```

Enter fullscreen mode Exit fullscreen mode

用它玩一会儿，在下一篇文章中，我将为你提供这些方法的实现。

敬请期待！