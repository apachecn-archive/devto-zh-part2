# 在 JavaScript 中访问嵌套对象

> 原文：<https://dev.to/flexdinesh/accessing-nested-objects-in-javascript--9m4>

[![undefined error in JavaScript](img/f00a4029c506917fd8aa124cea005dd9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jGChYAyx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/i3ybGS/undefined_access.jpg)

**tldr；** *以超级酷的方式安全访问 JavaScript 中的嵌套对象。*

JavaScript 很神奇，我们都知道。但是 JavaScript 中的一些东西真的很奇怪，让我们摸不着头脑。其中之一就是当您试图访问一个嵌套对象时会遇到这个错误，

***无法读取未定义的*** 的属性‘foo’

大多数情况下，当我们使用 JavaScript 时，我们会处理嵌套对象，并且经常需要安全地访问最内层的嵌套值。

让我们以这个嵌套对象为例。

```
const user = {
    id: 101,
    email: 'jack@dev.com',
    personalInfo: {
        name: 'Jack',
        address: {
            line1: 'westwish st',
            line2: 'washmasher',
            city: 'wallas',
            state: 'WX'
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了访问我们的用户名，我们将编写

```
const name = user.personalInfo.name;
const userCity = user.personalInfo.address.city; 
```

Enter fullscreen mode Exit fullscreen mode

这既简单又直接。

但是，由于某种原因，如果我们的用户的个人信息不可用，对象结构将是这样的，

```
const user = {
    id: 101,
    email: 'jack@dev.com'
} 
```

Enter fullscreen mode Exit fullscreen mode

现在如果你尝试访问这个名字，你会被抛出*不能读取未定义*的属性‘name’。

```
const name = user.personalInfo.name; // Cannot read property 'name' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

这是因为我们试图从一个不存在的对象访问`name`键。

大多数开发人员处理这种情况的通常方式是，

```
const name = user && user.personalInfo ? user.personalInfo.name : null;
// undefined error will NOT be thrown as we check for existence before access 
```

Enter fullscreen mode Exit fullscreen mode

如果你的嵌套结构很简单，这是没问题的，但是如果你的数据嵌套了 5 或 6 层，那么你的代码看起来会很乱

```
let city;
if (
    data && data.user && data.user.personalInfo &&
    data.user.personalInfo.addressDetails &&
    data.user.personalInfo.addressDetails.primaryAddress
   ) {
    city = data.user.personalInfo.addressDetails.primaryAddress;
} 
```

Enter fullscreen mode Exit fullscreen mode

有一些技巧可以处理这种混乱的对象结构。

## 奥利弗·斯蒂尔的嵌套对象访问模式

这是我个人最喜欢的，因为它让代码看起来*干净*和*简单*。我不久前从 stackoverflow 中挑选了这种风格，一旦你理解了它是如何工作的，它就非常吸引人。

```
const name = ((user || {}).personalInfo || {}).name; 
```

Enter fullscreen mode Exit fullscreen mode

用这种符号，你永远不会遇到*不能读取未定义的*的属性‘name’。你基本上检查用户是否存在，如果不存在，你创建一个空的对象。这样，下一级键**将总是从一个已存在的对象或一个空对象**中被访问，而不是从未定义的对象中被访问。

不幸的是，**你不能用这个技巧**访问嵌套数组

## 使用数组 Reduce 访问嵌套对象

Array reduce 方法非常强大，可以用来安全地访问嵌套对象。

```
const getNestedObject = (nestedObj, pathArr) => {
    return pathArr.reduce((obj, key) =>
        (obj && obj[key] !== 'undefined') ? obj[key] : undefined, nestedObj);
}

// pass in your object structure as array elements
const name = getNestedObject(user, ['personalInfo', 'name']);

// to access nested array, just pass in array index as an element the path array.
const city = getNestedObject(user, ['personalInfo', 'addresses', 0, 'city']);
// this will return the city from the first address item. 
```

Enter fullscreen mode Exit fullscreen mode

## [类型](#typy)

如果你认为以上方法有点太主流，那么你应该试试我写的 [Typy](https://github.com/flexdinesh/typy) 库。除了安全地访问嵌套对象，它还做了许多更棒的事情。🎉

它以 npm 包的形式提供- [类型](https://www.npmjs.com/package/typy)

如果你使用 **Typy** ，你的代码会是这样的，

```
import t from 'typy';

const name = t(user, 'personalInfo.name').safeObject;
const city = t(user, 'personalInfo.addresses[0].city').safeObject;
// address is an array 
```

Enter fullscreen mode Exit fullscreen mode

有一些其他的库，比如 Lodash 和 Ramda，可以做到这一点。但是在轻量级的前端项目中，特别是如果您只需要这些库中的一两个方法，选择一个替代的轻量级库是个好主意，或者更好的是，编写您自己的库。

Happy 安全访问 JavaScript 中的嵌套对象’！💥