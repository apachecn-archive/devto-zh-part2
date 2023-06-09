# ES 模块介绍

> 原文：<https://dev.to/flaviocopes/introduction-to-es-modules-2om>

[![ES Modules](img/318f9c0447536d70c06d0cc0f826cd31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZAv-Tbv3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flaviocopes.com/es-modules/banner.jpg)

*   ES 模块介绍
*   ES 模块语法
*   其他导入/导出选项
*   克-奥二氏分级量表
*   不支持模块的浏览器怎么办？
*   结论# # ES 模块介绍

ES 模块是使用模块的 ECMAScript 标准。

虽然 [Node.js](https://dev.to/flaviocopes/introduction-to-nodejs-3can) 多年来一直使用 CommonJS 标准，但浏览器从未有过模块系统，因为每个重大决策(如模块系统)都必须首先由 ECMAScript 标准化，然后由浏览器实现。

这一标准化过程随着 [ES6](https://dev.to/flaviocopes/the-complete-ecmascript-2015-2017-guide-14n8-temp-slug-2553173) 而完成，浏览器开始实施这一标准，试图让一切保持一致，以同样的方式工作，现在 ES 模块在 Chrome、Safari、Edge 和 Firefox 中得到支持(从版本 60 开始)。

模块非常酷，因为它们可以让你封装各种功能，并将这些功能作为库暴露给其他 JavaScript 文件。

[![ES Modules Support](img/e048daac824cd84f4e8a7e61d188de8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A3JyU1pF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flaviocopes.com/es-modules/browser-support.png)

## ES 模块语法

导入模块的语法是:

```
import package from 'module-name' 
```

Enter fullscreen mode Exit fullscreen mode

而 CommonJS 使用

```
const package = require('module-name') 
```

Enter fullscreen mode Exit fullscreen mode

模块是一个 JavaScript 文件，**使用`export`关键字导出**一个或多个值(对象、函数或变量)。例如，此模块导出一个返回大写字符串的函数:

> uppercase.js

```
export default (str) => str.toUpperCase() 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，模块定义了一个单独的，**默认导出**，所以它可以是一个匿名函数。否则，它将需要一个名字来区别于其他出口。

现在，**任何其他 JavaScript 模块**都可以通过导入 uppercase.js 提供的功能。

HTML 页面可以通过使用带有特殊属性`type="module"`的`<script>`标签来添加模块:

```
<script type="module" src="index.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:这个模块导入的行为类似于`defer`脚本加载。参见[使用延迟和异步有效加载 JavaScript】](https://dev.to/flaviocopes/efficiently-load-javascript-with-defer-and-async-agm-temp-slug-9543671)

需要注意的是，任何用`type="module"`加载的脚本都是以[严格模式](https://dev.to/flaviocopes/javascript-strict-mode-5b1g-temp-slug-822367)加载的。

在这个例子中，`uppercase.js`模块定义了一个**默认导出**，所以当我们导入它时，我们可以给它指定一个我们喜欢的名字:

```
import toUpperCase from './uppercase.js' 
```

Enter fullscreen mode Exit fullscreen mode

而且我们可以用:

```
toUpperCase('test') //'TEST' 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用绝对路径来导入模块，以引用在另一个域上定义的模块:

```
import toUpperCase from 'https://flavio-es-modules-example.glitch.me/uppercase.js' 
```

Enter fullscreen mode Exit fullscreen mode

这也是有效的导入语法:

```
import {foo} from '/uppercase.js';
import {foo} from '../uppercase.js'; 
```

Enter fullscreen mode Exit fullscreen mode

这不是:

```
import {foo} from 'uppercase.js';
import {foo} from 'utils/uppercase.js'; 
```

Enter fullscreen mode Exit fullscreen mode

要么是绝对的，要么名字前有个`./`或者`/`。

## 其他导入/导出选项

我们在上面看到了这个例子:

```
export default (str) => str.toUpperCase() 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个默认导出。然而，在一个文件中，你可以使用以下语法导出多个内容:

```
const a = 1
const b = 2
const c = 3

export { a, b, c } 
```

Enter fullscreen mode Exit fullscreen mode

另一个模块可以使用
导入所有这些导出

```
import * from 'module' 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用[析构赋值](https://flaviocopes.com/ecmascript/#destructuring-assignments) :
来导入其中的一些导出

```
import { a } from 'module'
import { a, b } from 'module' 
```

Enter fullscreen mode Exit fullscreen mode

为了方便起见，您可以使用`as` :
来重命名任何导入

```
import { a, b as two } from 'module' 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过名称导入默认导出和任何非默认导出，就像这个常见的 React 导入:

```
import React, { Component } from "react" 
```

Enter fullscreen mode Exit fullscreen mode

你可以在[https://glitch.com/edit/#!查看一个 ES 模块的例子/flavio-es-modules-示例？path=index.html](https://glitch.com/edit/#!/flavio-es-modules-example?path=index.html)

## CORS

使用 [CORS](https://dev.to/flaviocopes/a-quick-guide-to-cors-18im-temp-slug-4845164) 获取模块。这意味着如果你引用来自其他域的脚本，它们必须有一个有效的 CORS 头，允许跨站点加载(像`Access-Control-Allow-Origin: *`)

## 不支持模块的浏览器怎么办？

使用`type="module"`和`nomodule`的组合:

```
<script type="module" src="module.js"></script>
<script nomodule src="fallback.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

ES 模块是现代浏览器中引入的最大特性之一。它们是 ES6 的一部分，但是实现它们的道路是漫长的。

我们现在可以使用它们了！但是我们也必须记住，拥有过多的模块会影响页面的性能，因为这是浏览器在运行时必须执行的又一个步骤。

即使 ES 模块登陆浏览器，Webpack 可能仍将是一个巨大的玩家，但在语言中直接构建这样一个功能对于统一客户端和 Node.js 上的模块工作方式来说是巨大的。