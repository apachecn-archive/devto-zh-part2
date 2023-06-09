# TypeScript 为什么这么棒？

> 原文：<https://dev.to/rkoutnik/why-is-typescript-so-great-17am>

在我的开发者生涯中，我认为我还没有遇到过像 [TypeScript](https://www.typescriptlang.org/) 这样革命性的东西。TypeScript 正站在许多人的肩膀上(每个努力让 JS 开发人员接受构建系统的人！)但 TypeScript 是那项工作的出色积累，它从根本上改变了我的工作方式。以至于我最终将我心爱的 Sublime 文本切换到 Visual Studio 代码，以获得更好的类型脚本支持。

(顺便说一句，我使用微软编辑器编写微软语言，而且两者都是开源的，这一事实仍然让人感觉很奇怪)

对于那些刚接触前端开发的人(或者那些在中断很长时间后回来的人)，TypeScript 是一种新的语言，它是 JavaScript 的超集，在混合中添加了可选的类型。这里有一段打字稿:

```
let currentUserId = 7;
let users = [{
  id: 7,
  firstName: 'Robert',
  lastName: 'Smith'
}, {
  id: 12,
  firstName: 'Dana',
  lastName: 'Jones'
}];

let currentUser = users.filter(u => u.id === currentUserId);

console.log('Hello,', currentUser.firstName, '!'); 
```

Enter fullscreen mode Exit fullscreen mode

最重要的是，您会注意到在这个代码片段中没有任何类型注释或其他奇怪的东西。*所有 JavaScript 也是有效的 TypeScript* 。这是 TypeScript 最大的资产。将代码库转换成 TypeScript 只需要一个命令:

```
find . -name "*.js" -exec bash -c 'mv "$1" "${1%.js}".ts' - '{}' \; 
```

Enter fullscreen mode Exit fullscreen mode

(好吧，好吧，所以不止这些。我们稍后会谈到这一点)。

所以你所有的 JavaScript 都是有效的类型脚本。那又怎样？答案在于打字稿到底有多聪明。这不仅仅是“我们把一堆类型塞进了 JavaScript”，而是围绕这一点的整个工具生态系统，都是以开发人员为中心构建的。我们花了一些时间才走到今天(在`v0.x`的黑暗日子里，我几次放弃了 TypeScript)。

TypeScript 查看了上面的代码片段，知道`users`是一个对象数组，而`filter`是一个数组上的方法，它返回另一个数组。因此，`currentUser`是一个数组，等等！数组没有`firstName`属性！然后，TypeScript 在这个明显的问题下面画了一条红色的曲线，允许您在编辑器中直接修复它。不需要内容切换到浏览器，盯着一个不应该在那里的奇怪的`undefined`，并最终追踪到一个应该是`find`的`filter`。

在打字之前，我总是犯这种错误，并且在它们进入生产之前就发现了大多数错误。现在我仍然经常犯这种错误，但它们会被立即发现，而不是产生难以置信的古怪错误。

TypeScript 改变了游戏，甚至根本不需要修改我们的代码。即使在开始，它也是一个超级短柄，允许我们将大量关于代码库和 JavaScript 的工作内存卸载到一个工具上，允许我们将这些脑细胞花在更好的架构上，并编写伪装成关于所述工具的博客帖子的糊状情书。

TypeScript 作为一个超级 linter 功能强大得令人难以置信，如果这就是它所做的一切，那就好了。下一次我们将讨论类型，如何在 TypeScript 中定义它们，以及为什么这很有用。