# React 教程-添加类型脚本

> 原文：<https://dev.to/chrisotto/react-tutorial-add-typescript-4hd9>

这个星期我在看 React [教程](https://reactjs.org/tutorial/tutorial.html)。在经历教程的每个阶段时；过了国家，JSX 和反应组件，我想写在打字稿。我将仔细阅读教程代码，看看为了让它正常工作，我必须做些什么修改。

*   使用 node 安装我们需要的 typescript 依赖项:

`npm install --save typescript @types/node @types/react @types/react-dom @types/jest`

*   更改 Square 的函数定义，pass (props: any)而不是 just (props):

```
function Square(props: any) {} 
```

Enter fullscreen mode Exit fullscreen mode

*   修改棋盘和游戏组件以接受组件定义中的任何内容:

```
class Board extends React.Component<any, any> {}

class Game extends React.Component<any, any> {} 
```

Enter fullscreen mode Exit fullscreen mode

*   将文件类型更改为。tsx 而不是。射流研究…

就像这样，通过一些软件包安装和一些代码更改，您就能够使用 typescript 而不是普通的 JS 来编译项目。您可能希望进行其他修改，以利用 typescript 的其他有用部分。