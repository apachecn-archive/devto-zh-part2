# React 中的简单 Hello World

> 原文：<https://dev.to/rajaomalalasendra/simple-hello-world-in-react-4nhe>

首先要做的是安装节点 js，如果你还没有的话。

下面是链接:[安装节点 js](https://nodejs.org/en/download/)

然后在命令行中查看节点版本:

```
 $node --version 
```

或者，您也可以安装纱线:

```
 $npm install yarn -g # -g stands for global 
```

然后通过命令安装“create-react-app ”:

```
 $npm install create-react-app -g 
```

安装 create-react-app 后，您需要做的下一件事是:

```
 $create-react-app hello-world 
```

安装后会有一个帮助:

```
 $cd hello-world 
```

要查看所有目录，您可以使用命令:

```
 $ls for Mac users and GNU/Linux users
     $dir for Widows users 
```

您可能会找到目录:“usr/”

之后你可以使用文本编辑器，比如:sublim text，atom 或者 visual studio code。
命令行是:

```
$subl . for sublim text
$atom . for atom
$code . for visual studio code 
```

但是你也可以通过右键点击来打开它。

返回到命令行，您可以使用以下命令:

```
$npm start
or
$yarn start 
```

恭喜你！！！如果没有错误的话。您刚刚在 reactjs 中创建了第一个应用程序。

接下来要做的事情是修改 App.js

当我们进入它的时候，你可以看到这几行代码。别担心。看了这个帖子就知道了。

```
import React from "react";
class App extends React.Component{
render{ return( /.../ )}
}
export default App; 
```

然后，要获得 Hello World 应用程序，您可以在/中添加 *jsx* .../.
想了解更多关于 jsx 的信息，你可以点击链接:[更多关于 jsx 的信息](https://reactjs.org/docs/introducing-jsx.html)

但是我们可以使用的 hello world 的 jsx 是:

```
<h1> Hello World 
</h1> 
```

之后，谷歌浏览器会刷新，你会看到 hello world 应用程序。

要从社区获得更多文档和帮助:
您可以访问以下网站:

[codepen](codepen.io)
T3】codesandboxT5】或者在 [github](github.com)

非常感谢您的阅读。下次见。