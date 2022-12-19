# 学习反应——使用组件和虚拟数据构建用户界面

> 原文：<https://dev.to/jonhilt/learning-react---building-up-the-user-interface-using-components-and-dummy-data--106d>

在上一篇文章中，你看到了我们如何开发一个新的 React 项目，这个项目最终将连接到现有的 ASP.NET 核心 Web API。

我们已经有了一个简单的组件，需要完成我们的基本接口，并将其连接到一些数据。

这里有一个快速提示，提醒我们的目标是什么。

[![](../Images/034a7be904917b44c22c2af6cea1e28e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EO-UCXiY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-2/2018-05-23-20-57-22.png)

我们已经走了这么远。

[![](../Images/9ea4cdc5051952b74f80611548897c90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t8NUHXDm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-2/2018-05-23-20-57-37.png)

在我们继续之前，如果你想看看这个系列的源代码，请点击这里:-)

接下来去哪里？

## 组件一路向下

[![](../Images/9162021966783527e70283adf1e83074.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q8CnAQ8V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-2/2018-05-23-20-50-10.png)

React 的支柱之一是**一切都是组件**。

在 React 中构建任何用户界面都可以归结为决定您需要哪些组件以及构建它们的顺序。

我们简单的用户列表可能是一个大组件，所有的标记和逻辑都在一个文件中，但是 React 鼓励你将它分解。

一个更好的办法是构建几个较小的组件，每个组件都有一个特定的角色，您可以单独开发这些组件，然后将它们组合在一起，形成更大的所有歌唱和舞蹈功能。

当你停下来想一想，这很有意义。

一次性构建一个完整的应用程序是很困难的，但是将它分解成易于管理的部分，你会发现你可以轻松完成所有的需求。

## 建立用户明细行

考虑到这一点，我们开始构建一个简单的组件来显示每个用户的详细信息(作为 My Users 列表中的行)。

[![](../Images/cc2039a0709517b7b7d8a810a4cf1fb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IZ7eswGO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-2/2018-05-23-21-09-18.png)

这是我们最后得到的结果。

**userrow . tsx**T2】

```
import * as React from "react";

function userRow() {
    return (
        <tr>
            <td className="avatar">Avatar</td>
            <td className="name">Jon Hilton</td>
            <td className="summary">36 / Lead Developer</td>
            <td className="actions">Buttons here</td>
        </tr>
    )
}

export default userRow; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们坚持一个简单的原则，即在过多担心外观之前，首先将关键元素放置到位(因此使用了一个简单的表格；我很容易迷失几天，试图使用 CSS 做任何工作！).

我们仍然在使用硬编码的值，在进入真正的数据之前，需要将这些行中的至少一行插入到我们的组件中。

**MyUsers.tsx**

```
import * as React from "react";
import UserRow from "./UserRow";

export default class MyUsers extends React.Component<any, any>{
    public render() {
        return (
            <div>
                <h1>My Users</h1>
                <table className="user-list">
                    <tbody>
                        <UserRow />
                    </tbody>
                </table>
            </div>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里做了一个小小的调整，使用一个表格来呈现所有内容，并引入我们闪亮的新组件 **UserRow** 。

我们在`index.css`中放了一些最小的 css...

```
.user-list {
  width: 600px;
}

.avatar {
  max-width: 100px;
}

.name {
  padding-left: 10px;
  max-width: 600px;
}

.summary {
  padding-left: 10px;
  max-width: 400px;
}

.actions {
  max-width: 100px;
} 
```

Enter fullscreen mode Exit fullscreen mode

检查我们的进展给了我们这个...

[![](../Images/c2b160ce7bedb7e3a816dbe31db77d96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_s82wmIT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-2/2018-05-29-21-43-54.png)

到目前为止一切顺利，但是如何从实际数据中驱动这一行呢？(不仅仅是标记中的硬编码值)。

## 给你道具

我们需要接这个电话...

```
import * as React from "react";

function userRow() {
    return (
        <tr>
            <td className="avatar">Avatar</td>
            <td className="name">Jon Hilton</td>
            <td className="summary">36 / Lead Developer</td>
            <td className="actions">Buttons here</td>
        </tr>
    )
}

export default userRow; 
```

Enter fullscreen mode Exit fullscreen mode

并且可以呈现多行，每一行的每个单元格都有自己的值。

现在，如果你习惯使用 ASP.NET MVC 和 Razor，你无疑会习惯引用`@Model`来呈现值...

```
<p>Hey @Model.FirstName!</p> 
```

Enter fullscreen mode Exit fullscreen mode

React 的对等词是“道具”...

```
import * as React from "react";

export default class UserRow extends React.Component<any, any>{

    public render(){
        return (
            <tr>
                <td className="avatar">Avatar</td>
                <td className="name">{this.props.user.name}</td>
                <td className="summary">{this.props.user.summary}</td>
                <td className="actions">Buttons here</td>
            </tr>
        )
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

假设我们能够以某种方式将用户对象放入我们的组件中，那么“name”和“summary”值就会呈现在标记中正确的位置上。

> **欺骗任何人时要小心**
> 
> 如果你使用过 Typescript，你可能会发现这里有一点欺骗...
> 
> `React.Component<any, any>`
> 
> 第一个`<any,`表示我们的道具是什么`type`。
> 
> 通过使用`any`,我们基本上是说道具可以是任何类型。这样做使我们能够快速启动并运行某些东西，但同时也移除了我们原本可以获得的类型安全。
> 
> 如果我们在那里指定一个类型，typescript 编译器会保护我们，确保我们必须将一个正确“形状”的对象传递给我们的组件。
> 
> 然而，这足以让我们继续下去，我们总是可以添加类型，一旦我们学会了绳子...

现在我们可以向组件传递一个“用户”,如下所示...

```
let someUser = { id: 1, name: 'Jon', summary: '36 / Lead Developer' };

/// code omitted

<UserRow user={someUser} /> 
```

Enter fullscreen mode Exit fullscreen mode

但是我们真正需要的是为多个用户生成多行...

## 地图(有点像 Linq)

循环访问用户对象数组；我们可以使用 javascript 的`map`函数为我们发现的每一个渲染一个`UserRow`。

```
public render() {
    return (
        <div>
            <h1>My Users</h1>
            <table className="user-list">
                <tbody>
                    {this.getUserData().map(user => 
                        <UserRow key={user.id} user={user} />)}
                </tbody>
            </table>
        </div>
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

与 C#中的`Select`没有什么不同，这将遍历从`getUserData()`返回的值，并为每个值呈现一个`UserRow`。

如果你想知道`key`属性是什么，省略它，React 就会爆炸。这是一个特殊的属性，它要求数组中的每一项都有唯一的值。React 使用它来有效地呈现每一行，并且只重新呈现已经改变的行(当源数据改变时)。

最后，我们可以用一个简单的`getUserData()`函数实现来测试这一切。

```
private getUserData() {
    return [
        { id: 1, name: 'Jon', summary: '36 / Lead Developer' },
        { id: 2, name: 'Janine Smith', summary: '32 / Senior Engineer' }
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/e86ee9d47b90c7e478e937b40091798a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5sWp9W9k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-2/2018-05-29-22-18-35.png)

## 总结

现在我们的组件在一起了。

我们可以根据用户的数量来呈现用户行；下一步是将它连接到我们的 ASP.NET 核心 Web API 上。

**记住，如果你想看看这个系列的源代码，你可以[点击这里:-)](https://jonhilton.net/devto/react-source/)**

*图片鸣谢:雷奥雷诺兹[书堆 2017](http://www.flickr.com/photos/49968232@N00/34442537576) via [photopin](http://photopin.com) [(许可证)](https://creativecommons.org/licenses/by-nc-sa/2.0/)*