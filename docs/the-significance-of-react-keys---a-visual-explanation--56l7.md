# 反应键的意义——一个直观的解释

> 原文：<https://dev.to/jtonzing/the-significance-of-react-keys---a-visual-explanation--56l7>

*免责声明:这是一个严重的过度简化，应该作为调解工作的基本指南，而不是一个完美的例子！*

在 React 中处理数组时，利用每个元素上的“key”属性对于避免不必要的重新呈现性能影响至关重要。这篇文章将有希望解释为什么你应该总是清楚地定义你的键，如果你不这样做，你会错过什么。

让我们从一个数组
开始

```
const joshs = [{  Name: "Josh", }, { Name: "Joshina", }, {  Name: "Notjosh", }] 
```

Enter fullscreen mode Exit fullscreen mode

我们的 React 组件的业务端，呈现所述数组

```
<div>
    { joshs.map((person, index) => ( <span key={index}>{person.name}</span>)) }
</div> 
```

Enter fullscreen mode Exit fullscreen mode

以及它输出的 HTML

```
<div>
    <span key=’0’>Josh</span>
    <span key=’1’>Joshina</span>
    <span key=’3’>Notjosh</span>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

非常好用！

...但是！

一个新的乔希来了，他告诉其他人让开。

```
 [{ Name: "Mega Josh"}, {  Name: "Josh", }, { Name: "Joshina", }, {  Name: "Notjosh", }] 
```

Enter fullscreen mode Exit fullscreen mode

我们的组件接收新列表，做它的事情...

```
<div>
    { joshs.map((person, index) => ( <span key={index}>{person.name}</span>)) }
</div> 
```

Enter fullscreen mode Exit fullscreen mode

像以前一样打印出来。

```
<div>
    <span key=’0’>Mega Josh</span>
    <span key=’1’>Josh</span>
    <span key=’2’>Joshina</span>
    <span key=’3’>Notjosh</span>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

非常好用！

...但是！

让我们看看 React 在呈现新列表时所经历的过程中实际发生了什么(以一种非常简单的方式)。

一个 React 组件，当你把它归结为它最原始的形式(即从 JSX 转换而来)时，它只是一个具有一组属性的对象。这些属性定义了它的类型、名称、状态、它收到了什么道具、它是否有孩子等等。

每当数组中发生变化时，就会创建一个新的 Josh `<span>`组件对象列表。React [协调器](https://reactjs.org/docs/reconciliation.html)会将新创建的对象与它在 DOM 中的当前版本进行比较。如果在某些属性之间检测到任何差异，它将重新绘制组件，认为它是同一个对象，但属性已经改变。

因此，在我们的例子中，我们有我们的原始数组(组件)，可以大致翻译成这样...

```
[{
  Type: "span",
  Key: "0",
  Children: "Josh"
}, {
  Type: "span",
  Key: "1",
  Children: "Joshina"
}, {
  Type: "span",
  Key: "2",
  Children: "Notjosh"
}] 
```

Enter fullscreen mode Exit fullscreen mode

协调器将查看键和组件属性(在我们简化的例子中，是内容或子元素)，然后查看它以前的组件列表，看它是否匹配任何以前的组合。

由于我们的列表使用数组索引位置作为其键，当“Mega Josh”到达并将所有组件下移一个位置时，由于 React 注意到键与它们以前的属性不匹配，所以每次比较现在都失败了！

```
[{
  Type: "span",
  Key: "0",                // Expected 0 to match 'Josh'
  Children: "Mega Josh"     // IM DIFFERENT, REDRAW ME
}, {
  Type: "span",
  Key: "1",                // Expected 1 to match 'Joshina'
  Children: "Josh"          // IM DIFFERENT, REDRAW ME
}, {
  Type: "span",
  Key: "2",                // Expected 2 to match 'Notjosh'
  Children: "Joshina"       // IM DIFFERENT, REDRAW ME
}, {
  Type: "span",   
  Key: "3",                // IM NEW
  Children: "Notjosh"       // DRAW ME
}] 
```

Enter fullscreen mode Exit fullscreen mode

但是！我们可以防止这种情况。如果我们清楚地定义一个静态的、唯一的、*和*唯一地与它相关的属性相关联的键，React 可以确认它是同一个组件，即使它已经改变了它的位置。

让我们用惟一的键重新构建我们的组件

*注意:在这个例子中，我使用了`name`属性来保持我们的 josh 对象简单，但是这不是最佳实践，因为两个组件拥有相同键的可能性非常高。在可能的情况下，您应该始终使用数据对象中的某种主键。*T3】

```
<div>
    { people.map((person, index) => ( <span key={`key-${person.name}`}>{person.name}</span>)) }
</div> 
```

Enter fullscreen mode Exit fullscreen mode

导出的 HTML 现在看起来像

```
<div>
    <span key=’key-Josh’>Josh</span>
    <span key=’key-Joshina’>Joshina</span>
    <span key=’key-Notjosh’>Notjosh</span>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

以及更新后的数组 HTML

```
<div>
    <span key='key-Mega Josh'>Josh</span>
    <span key=’key-Josh’>Josh</span>
    <span key=’key-Joshina’>Joshina</span>
    <span key=’key-Notjosh’>Notjosh</span>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在键对于它们的数据对象是唯一的(而不是它们的数组位置)，所以当我们进行对象比较时

```
[{
  Type: "span",
  Key: "key-Josh",
  Children: "Josh"
}, {
  Type: "span",
  Key: "key-Joshina",
  Children: "Joshina"
}, {
  Type: "span",
  Key: "key-Notjosh",
  Children: "Notjosh"
}] 
```

Enter fullscreen mode Exit fullscreen mode

协调器将看到一些组件没有改变，它们只是简单地移动了，只有新的组件将被创建并插入到列表的前面，而不影响后面的组件。太棒了。

```
[{
  Type: "span",
  Key: "key-Mega Josh",    // IM NEW
  Children: "Mega Josh"     // DRAW ME
}, {
  Type: "span",
  Key: "key-Josh",         // Expected 'key-Josh' to match 'Josh'
  Children: "Josh"          // IM THE SAME, DONT REDRAW ME
}, {
  Type: "span",
  Key: "key-Joshina",      // Expected 'key-Joshina' to match 'Joshina'
  Children: "Joshina"       // IM THE SAME, DONT REDRAW ME
}, {
  Type: "span",
  Key: "key-Notjosh",      // Expected 'key-Notjosh' to match 'Notjosh'
  Children: "Notjosh"       // IM THE SAME, DONT REDRAW ME
}] 
```

Enter fullscreen mode Exit fullscreen mode

对于某些用途，性能影响可能很小(如果阵列从不改变顺序，甚至不存在)。添加键的好处对于非常大的数组来说是显而易见的，因为它消除了渲染大部分列表的需要。神奇！