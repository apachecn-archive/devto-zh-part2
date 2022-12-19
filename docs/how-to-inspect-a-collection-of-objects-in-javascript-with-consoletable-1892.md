# 如何用 console.table 检查 Javascript 中的对象集合

> 原文：<https://dev.to/chaseadamsio/how-to-inspect-a-collection-of-objects-in-javascript-with-consoletable-1892>

# 学习如何在浏览器中用 console.table 检查对象的数组或对象。

当用 JavaScript 构建软件时，我经常需要检查对象的数组或对象的对象。

我最喜欢的实现方法之一是使用`console.table()`。`console.table()`不用打开相同“类型”的嵌套对象(在本例中，我们将使用一些奇怪的鱼)，而是以一种清晰的表格格式呈现数据。在[NodeJS 10 . 0 . 0](https://nodejs.org/api/console.html#console_console_table_tabulardata_properties)版本和[几乎所有主流浏览器](https://developer.mozilla.org/en-US/docs/Web/API/Console/table#Browser_compatibility)中都有。

在本教程中，我们将学习如何通过创建一个对象数组并调用`console.table`来使用 NodeJS 和 Google Chrome 检查数据。

# `console.table()`签名

`console.table(tabularData[, properties])`是一个带两个参数的方法:

*   `tabularData`:任意类型的数据集合
*   `properties`:要用来显示表格的字段字符串数组

# 设置代码

首先，让我们为这个名为`weirdfishes.js`的示例代码创建一个文件，并创建一个名为`weirdFishes` :
的奇怪的鱼的数组

```
let weirdFishes = [{
  "common_name": "common fangtooth",
  "scientific_name": "Anoplogaster cornuta",
  "family": "Anoplogastridae",
  "wikipedia_link": "https://en.wikipedia.org/wiki/Fangtooth"
}, {
  "common_name": "vampire squid",
  "scientific_name": "Vampyroteuthis infernalis",
  "family": "Vampyroteuthidae",
  "wikipedia_link": "https://en.wikipedia.org/wiki/Vampire_squid"
}, {
  "common_name": "blobfish",
  "scientific_name": "Psychrolutes marcidus",
  "family": "Anoplogastridae",
  "wikipedia_link": "https://en.wikipedia.org/wiki/Blobfish"
}, {
  "common_name": "dumbo octopus",
  "scientific_name": "Grimpoteuthis abyssicola",
  "family": "Opisthoteuthidae",
  "wikipedia_link": "https://en.wikipedia.org/wiki/Grimpoteuthis_abyssicola"
}]; 
```

Enter fullscreen mode Exit fullscreen mode

# 对 console.table()的简单调用

我们将添加一个对`console.table()`的调用，并将`weirdFishes`作为第一个参数传入:

```
console.table(weirdFishes); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们只使用第一个参数调用`console.table`，它将显示每个对象中的所有数据。

# 检查 NodeJS 中的对象数组

打开你最喜欢的终端模拟器应用程序(截图我用的是 VS Code 的集成终端)，在`weirdfishes.js`的父目录下运行`node weirdfishes.js`。**注意，这需要 nodejs > = v10.0.0)**

当我们运行该命令时，控制台将显示如下表格:

[![Output of running console.table on an array of objects in NodeJS](../Images/744318a9db569cf9d0b800a73ef583a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MOVdkI-A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chaseadams.io/img/nodejs-console-table.png)

# 检查浏览器中的对象数组(Chrome 示例)

要在 Chrome 中看到这一点，最简单的方法是通过 JavaScript 控制台添加对象。要打开 Javascript 控制台，输入`cmd + option + j`，浏览器窗口底部会弹出一个新的窗格。

复制上面的 JavaScript 片段并将其粘贴到 JavaScript 控制台中，按 enter 键，您将看到一个类似于下面截图的表格:

[![](../Images/2096ad3cb9c9535620bb4d8e47ba1db4.png "Output of running console.table on an array of objects in Chrome")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VARKrZr2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chaseadams.io/img/chrome-console-table.png)

# 基于键构造表格

让我们再来看看前面提到的第二个参数:`properties`—一个字符串数组，表示您想要显示的值。继续 JavaScript 控制台中的浏览器示例，用第二个参数`["common_names"]` :
调用`console.table`

```
console.table(weirdFishes, ["common_name"]); 
```

Enter fullscreen mode Exit fullscreen mode

用第二个 argumend 调用`console.table`,构造一个新表，只包含索引和数组中的键值:

[![](../Images/d40a70ed73241db56cd9e8755719ec63.png "Output of running console.table on an array of objects in Chrome with only the common name key")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q4JRJeyu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chaseadams.io/img/console-table-only-one-key.png)

# 使用 console.table 上的一个对象的对象

`console.table`也适用于一个对象的对象(因为数组是一种对象类型),索引将是子对象的键。您可以通过运行`console.table(roleModelsObj)`看到一个这样的例子。

```
let weirdFishes2 = {
    "common fangtooth": {
        "scientific_name": "Anoplogaster cornuta",
        "family": "Anoplogastridae",
        "wikipedia_link": "https://en.wikipedia.org/wiki/Fangtooth"
    },
    "vampire squid": {
        "scientific_name": "Vampyroteuthis infernalis",
        "family": "Vampyroteuthidae",
        "wikipedia_link": "https://en.wikipedia.org/wiki/Vampire_squid"
    },
    "blobfish": {
        "scientific_name": "Psychrolutes marcidus",
        "family": "Anoplogastridae",
        "wikipedia_link": "https://en.wikipedia.org/wiki/Blobfish"
    },
    "dumbo octopus": {
        "scientific_name": "Grimpoteuthis abyssicola",
        "family": "Opisthoteuthidae",
        "wikipedia_link": "https://en.wikipedia.org/wiki/Grimpoteuthis_abyssicola"
    }
};
console.table(weirdFishes2); 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到该表现在将每个对象的键作为索引:

[![](../Images/c7a80d2fd37d1b3885bf30f7717fd8d0.png "Output of running console.table on an object of objects in Chrome")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xqwT3sT---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chaseadams.io/img/console-table-object-of-objects.png)

现在您已经有了一个很好的选择来代替`console.log`以表格形式显示 JavaScript 中的对象，而不是嵌套数据——这个选择将节省您检查和调试的时间！