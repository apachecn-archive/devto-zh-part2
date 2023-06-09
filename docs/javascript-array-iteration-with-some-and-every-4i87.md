# 带有“some()”和“every()”的 Javascript 数组迭代

> 原文：<https://dev.to/wangonya/javascript-array-iteration-with-some-and-every-4i87>

如果你在你的代码中使用一个数组，很有可能你需要迭代数组中的值。有几种方法可以做到这一点，有些方法比其他方法更好、更有效，这取决于您想要实现的目标。

对于这篇文章，我将重点介绍两种方式:`some()`和`every()`。

#### `some()`

`some()`方法测试**数组中是否至少有一个**元素通过了由提供的函数实现的测试。它逐个检查元素，如果找到函数返回真值的数组元素，`some()`返回`true`，不检查剩余的值。否则返回`false`。

假设您想查看联系人列表中是否有联系人:

```
const contacts = ['Stewie', 'Meg', 'Quagmire', 'Cleveland'];

function checkContacts(arr, val) {
  return arr.some(arrVal => val === arrVal);
}

checkContacts(contacts, 'Lois');   // false
checkContacts(contacts, 'Meg'); // true 
```

Enter fullscreen mode Exit fullscreen mode

当检查`Lois`时，`some()`检查从`Stewie`开始到结束的数组元素，如果没有找到匹配，则返回`false`。当检查`Meg`时，它停止在`Meg`并返回`true`，忽略其余的元素。

#### `every()`

该方法测试数组中的**所有**元素是否通过了由提供的函数实现的测试。它逐个检查元素，如果找到函数返回 falsy 值的数组元素，`every()`返回`false`，不检查剩余的值。否则它返回`true`。

让我们检查一下联系人列表中的所有名字是否都超过 3 个字符:

```
['Stewie', 'Meg', 'Quagmire', 'Cleveland'].every(contact => contact.length >= 4); // false
['Stewie', 'Megan', 'Quagmire', 'Cleveland'].every(contact => contact.length >= 4); // true 
```

Enter fullscreen mode Exit fullscreen mode

第一个测试返回`false`，因为`Meg`只有 3 个字符。记住:对于`every()`，*来说，数组中的所有*元素都必须为真，它才能返回`true`。因此，在第二次测试中将`Meg`改为`Megan`会返回`true`。

如果您需要执行如上所述的类似任务，这两种方法会很方便。但是当然，它们不是唯一的方法。希望有人觉得这有用！:)