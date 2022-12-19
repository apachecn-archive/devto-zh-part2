# VS 代码中的自定义多态代码片段

> 原文：<https://dev.to/bnevilleoneill/custom-polymorphic-code-snippets-in-vs-code-1g6i>

[![](../Images/150c184b6088eda7a295d6fba875a01e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lE9B1Cfl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOlWyRD9XGR4abzRdHbBx_A.jpeg)

如今，几乎所有的代码编辑器中都有代码片段。它们可以节省大量的时间，允许您快速方便地插入任何编程语言的常用代码块。

VS 代码也不例外，我们将看到如何创建自己的定制代码片段来极大地改善您的工作流程。不仅如此，您还将了解什么是多态代码片段，以及它们与标准代码片段相比有多有用。我保证值得等待！

在其他编辑器中，一些代码片段的实现看起来有点神秘，尤其是在第一次使用时。然而，在 VS 代码中，它们相对容易掌握。我惊喜地发现，即使是动态代码片段也很容易设置。

所以，让我们开始吧！

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 创建基本片段

在 VS 代码中创建代码片段的过程对于所有编程语言都是一样的。所有定制代码片段都存储在 JSON 文件中(每种编程语言一个)。

您可以通过以下方式在 VS 代码中访问它们:

文件>首选项>用户片段(Windows)

代码>首选项>用户片段(macOS)

这将显示您可以为其定义代码片段的可用语言的下拉列表。如果您已经为某些语言添加了自定义代码段，为了方便起见，它们会首先出现在列表中。

从列表中选择 PHP，一个 php.json 文件将在编辑器窗口的一个新选项卡中打开。这是您为 PHP 语言添加自定义代码片段的地方。

每个编程语言 JSON 文件在注释中都有一个默认的例子来演示代码片段的用法。这个例子对于所有的编程语言都是一样的，所以除了作为你的第一个代码片段的起点，如果你想节省一些输入的话，这个例子不是很有用。

要创建新的代码片段，请使用以下属性向 php.json 添加一个命名的 JSON 对象:

*   前缀-触发代码片段的文本
*   描述-在您键入触发器前缀时显示在可用代码段列表中
*   正文代码段内容

下面是一个输出 PHP 函数体的简单例子:

```
{
"Basic PHP Function": {
"prefix": "func",
"body": [
"function test() {",
"\techo \"Hello World!\";",
"}"
],
"description": "Outputs a basic PHP function."
}
} 
```

代码片段名“Basic PHP Function”只是为了方便起见，不会出现在 JSON 文件之外，但是前缀和描述字段是可见的，所以选择有意义的值是个好主意。

如果您只想代码片段输出一行，那么主体可以是一个简单的字符串。但大多数情况下，您会希望它跨越多行，在这种情况下，将主体定义为字符串数组，如上例所示。

此外，如果您希望生成的代码很好地缩进，则根据需要在每一行的开头添加制表符。请注意，我们也对双引号字符进行了转义，以便可以在代码片段中使用它们。

那么，现在我们已经定义了我们的代码片段，我们如何使用它呢？

首先，不需要重新启动编辑器。我们可以马上开始使用新的代码片段。打开一个现有的 PHP 文件或创建一个新文件，在

每个匹配的代码片段都将显示在弹出窗口中。但是也显示其他匹配，比如 PHP 语言的内置匹配。您可以很容易地分辨出哪些是代码片段，因为这些代码片段的前缀是一个带有白色边框的黑框(底部边框是虚线)。

要展开代码片段，请从列表中选择它，然后按 Enter 或 Tab 键。

您是否注意到在插入代码片段时，您只看到了适用于您当前正在编辑的编程语言的代码片段？这使得搜索代码片段变得非常方便，因此您不必费力地寻找大量不相关的匹配。

这也是实现多态代码片段的关键，我们将在后面看到。

### 用代码片段更进一步

输出静态代码块当然非常有用，可以节省你大量的打字时间，但是我们可以通过使 VS 代码片段交互来做更多的事情。

### 制表符

基于我们前面的 PHP 函数片段的例子，我们可以使用制表位导航到代码片段中预定义的位置，并添加我们自己的值。

要定义制表位，只需在代码段的任何地方插入一个美元字符，后跟一个数字。

如果我们回到前面的 PHP 函数示例，那么我们可以为参数和字符串值添加制表位。

```
{
"Basic PHP Function": {
"prefix": "func",
"body": [
"function test( $$1 ) {",
"\techo \"$2\";",
"}",
"",
"$3"
],
"description": "Outputs a basic PHP function."
}
} 
```

现在，当代码片段展开时，光标会跳到第一个选项卡 top $1，这样您就可以添加一个参数变量。再次按 tab 键会将光标从字符串值跳转到制表位$2。

制表位顺序在这里很重要，所以如果我们颠倒制表位的编号，那么光标将首先跳转到字符串，然后是函数参数。

请注意，该参数的$$1 不是输入错误。我们只是在制表位前面加上一个$字符，这样就不用每次都输入了。如果你愿意，当然可以不考虑这个。

为了方便起见，我们还添加了第三个跳至函数外部的制表位，这样我们就可以轻松地在函数体外部继续添加新的 PHP 语句。

### 占位符

除了简单地将光标跳转到预定义的点，我们还可以添加编号的占位符文本，默认情况下，这些文本会插入到代码片段中。

带编号的制表位占位符定义为:

```
${1:item} 
```

您可以将它作为默认文本项的第一个制表位。当您在每个制表位之间循环时，您可以选择在跳转到下一个位置之前更新插入的文本，或者保留默认值。

```
{
"Basic PHP Function": {
"prefix": "func",
"body": [
"function test($${1:name}, $${2:age}, $${3:gender}) {",
"\techo \"Output data: {$${4:name}} {$${5:age}} {$${6:gender}}\";",
"}",
"",
"$0"
],
"description": "Outputs a basic PHP function."
}
} 
```

如果您不改变任何默认占位符文本，那么函数将输出为:

```
function test($name, $age, $gender) {
    echo "Output data: {$name} {$age} {$gender}";
} 
```

如果您喜欢默认的占位符文本，这是没问题的，但是如果您想要更改任何变量，那么您必须键入文本两次，以便它们在两个位置都匹配。

如果你有更复杂的代码段，并且在代码段的几个地方使用了相同的变量，那么这很快就会变得乏味。接下来我们将看到如何解决这个问题。

### 变量占位符

除了使用编号为的*制表位，您还可以使用可变占位符制表位。当您在多个位置定义了同一个变量时，这非常有用。每次你更新一个变量占位符，它也会在所有其他位置更新。*

让我们修改上一节的例子，使用变量占位符。

```
{
"Basic PHP Function": {
"prefix": "func",
"body": [
"function test($${name}, $${age}, $${gender}) {",
"\techo \"Output data: {$${name}} {$${age}} {$${gender}}\";",
"}",
"",
"$0"
],
"description": "Outputs a basic PHP function."
}
} 
```

现在，当您触发代码片段时，如果您更新了任何占位符，它也会在其他位置自动更新，这正是我们想要的！

### 占位符选择

如果您使用带编号的占位符，那么您可以有选择地为用户提供可以插入的值。

其格式为:

```
${1|one,two,three|} 
```

选项以逗号分隔的列表形式插入，并用竖线字符括起来。

使用占位符选项的一个例子是:

```
{
"Favorite Color": {
"prefix": "favcol",
"body": [
"echo \"My favorite color is ${1|red,green,orange,blue,indigo|}\";",
"$0"
],
"description": "Outputs your favorite color."
}
} 
```

当您触发这个代码片段时，会出现一个下拉选择列表。只需选择一个你想要的，然后按 tab 键转到下一个制表位。

### 多态代码片段

既然我们已经介绍了如何在 VS 代码中实现代码片段，让我们把注意力转向如何让它们更有效地工作。

不过，首先让我们谈谈多态性。这个伟大的想法与可重用性有关。它通常出现在一些事情以多种形式出现，但是可以通过一个公共接口使用的情况下。

多态在面向对象编程(OOP)中是一件大事，有很多书专门讨论这个主题。然而，出于我们的目的，我们可以采用这种可重用性的思想，并在实现通过公共触发器调用的不同编程语言的代码片段时应用它。

假设你有用几种不同的编程语言定义的代码片段，它们做同样的事情。即每个代码段的语法不同，但是代码段的目的是相同的。

一个这样的例子可以是出于调试目的输出变量值。

我们将在 PHP 和 JavaScript 中实现这一点，但你也可以很容易地将其扩展到其他语言，如 C++、Python、Java、Objective-C 等等。

**PHP**T2】

```
{
"Output PHP value": {
"prefix": "pout",
"body": [
"echo \"<pre>\";",
"print\_r($${value});",
"echo \"</pre>\";",
"$0"
],
"description": "Outputs a PHP value to the screen."
}
} 
```

**JavaScript**

```
{
"Output JavaScript value": {
"prefix": "jout",
"body": [
"console.log(${value});",
"$0"
],
"description": "Outputs a PHP value to the screen."
}
} 
```

当我们继续为其他编程语言添加输出代码片段时，我们必须记住如何为每种语言命名它们。

但诀窍是故意给他们完全相同的触发。

**PHP**T2】

```
{
"Output PHP value": {
"prefix": "out",
"body": [
"echo \"<pre>\";",
"print\_r($${value});",
"echo \"</pre>\";",
"$0"
],
"description": "Outputs a PHP value to the screen."
}
} 
```

**JavaScript**

```
{
"Output JavaScript value": {
"prefix": "out",
"body": [
"console.log(${value});",
"$0"
],
"description": "Outputs a PHP value to the screen."
}
} 
```

所以现在我们有了一个单一的触发器，它根据您触发代码片段的文件类型，根据上下文输出代码片段。整洁吗？

你自己试试吧。开始在 PHP 文件中输入内容。正如你所看到的，这触发了 php.json 的代码片段，同样，如果你从 JavaScript 文件中执行同样的操作，那么就会使用 javascript.json out 代码片段！

这是另一个从多种语言输出相同 HTML 标签的例子。制表位被定义为允许在需要时改变 HTML 标签。

**HTML**

```
{
"Output HTML": {
"prefix": "tag",
"body": [
"<${h2}>Heading</${h2}>",
"<${p}>I wandered lonely as a cloud.</${p}>",
"$0"
],
"description": "Outputs HTML."
}
} 
```

**PHP**T2】

```
{
"Output HTML Tag": {
"prefix": "tag",
"body": [
"echo \"<${h2}>Heading</${h2}>\";",
"echo \"<${p}>I wandered lonely as a cloud.</${p}>\";",
"$0"
],
"description": "Outputs HTML via PHP."
}
} 
```

**JavaScript**

```
{
"Output HTML Tag": {
"prefix": "tag",
"body": [
"var heading = \"<${h2}>Heading</${h2}>\";",
"var body = \"<${p}>I wandered lonely as a cloud.</${p}>\";",
"document.querySelector(\"#${id}\").innerHTML = heading + body;",
"$0"
],
"description": "Outputs HTML via JavaScript."
}
} 
```

**JSX**T2】

```
"Output HTML Tag": {
"prefix": "tag",
"body": [
"class ${Component} extends React.Component {",
"\trender() {",
"\t\treturn (",
"\t\t\t<Fragment>",
"\t\t\t\t<${h1}>Heading</${h1}>",
"\t\t\t\t<${p}>I wandered lonely as a cloud.</${p}>"
"\t\t\t</Fragment>",
"\t\t)",
"\t}",
"}",
"$0"
],
"description": "Outputs HTML via JSX."
}
} 
```

和以前一样，只需开始输入触发文本(在本例中是标记)，您将看到与您当前编辑的文件类型相关的代码片段。

恭喜你，你现在已经进入多态代码片段的世界了！

这种开发代码片段的方法非常有效，可以让您不必记住大量不同的代码片段触发器。现在，您只需要记住执行一项常见任务的代码片段的单个触发器。

更重要的是，您可以创建任意多的多态代码片段！

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *