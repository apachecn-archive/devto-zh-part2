# ECMA 脚本导入和导出

> 原文：<https://dev.to/kingrayhan/ecma-script-import-and-export--3d32>

在创建 JavaScript 模块以从模块中导出函数、对象或原始值时，会使用 export 语句，以便其他程序可以使用 import 语句。

该功能目前仅在 Safari 和 Chrome 中实现。它在许多 transpilers 中实现，如 Traceur 编译器、Babel 或 Rollup。
有 4 种类型的出口:

> 1 —命名导出(每个模块几个)
> 
> 2 —默认导出(每个模块一个)
> 
> 3 —混合命名&默认导出
> 
> 4 —循环依赖

## 1。命名出口

您可以导出变量、对象或函数。

命名导出对于导出多个值非常有用。在导入过程中，必须使用相应对象的相同名称。

假设我有一个名为 **lib.js** 的文件，里面有一些 js 变量

```
//------ lib.js ------
export var str1 = 'string 1';
export var str2 = 'string 2'; 
```

Enter fullscreen mode Exit fullscreen mode

在我的**main . js**T2】

```
//------ lib.js ------
import { str1 , str2 } from './lib'
console.log(str2); // Output:  string 1
console.log(str2); // Output:  string 2 
```

Enter fullscreen mode Exit fullscreen mode

我也可以这样导出，

```
var str1 = 'string 1';
var str2 = 'string 2';
var str2 = 'string 3';
export { str1 , str2 , str3 } 
```

Enter fullscreen mode Exit fullscreen mode

然后导入，

```
import { str1 , str2 , str3 } from './lib'
console.log(str1); // Output:  string 1
console.log(str2); // Output:  string 1
console.log(str3); // Output:  string 1 
```

Enter fullscreen mode Exit fullscreen mode

从另一个文件导入对象，我可以给一个别名

```
import { str1 as StringOne , str2 , str3 } from './lib'
console.log(StringOne); // Output:  string 1 
```

Enter fullscreen mode Exit fullscreen mode

不仅是变量，你还可以导出任何函数、类或变量或任何东西

```
//------ lib.js ------
function squire(n){
    return n*n;
}
var myInfo = {
 name: 'King Rayhan',
 age: 21
};
export { squire , myInfo }
//------ main.js ------
import { squire , myInfo } from './lib'
console.log(squire(3)); // output: 9
console.log('Name: ' + myInfo.name); // output: Name : King Rayhan
console.log('Age: ' + myInfo.age); // output: Age: 21 
```

Enter fullscreen mode Exit fullscreen mode

## 2。默认导出

一个模块只能有一个默认导出。如前面的例子所示，我们可以导出一个函数、变量、对象或任何东西，但只能导出一个。

```
//------ myFunc.js ------
export default function () { //... };

//------ main1.js ------
import myFunc from 'myFunc';
myFunc(); 
```

Enter fullscreen mode Exit fullscreen mode

下面是一些例子，

```
export default var info = {
 name: 'Rayhan',
 age: 21
};
// --- or
var info = {
 name: 'Rayhan',
 age: 21
}; 
```

Enter fullscreen mode Exit fullscreen mode

导出默认信息；
现在，我可以将默认导出导入到您的主文件中，

```
import info from './lib'
console.log(info.name); // Rayhan 
```

Enter fullscreen mode Exit fullscreen mode

不一定要使用相应对象的相同名称，您可以随意命名。

```
//------------- lib.js
export default var info = {
    name: 'Rayhan',
    age: 21
};

// ------------- main.js
import MyInformation from './lib'
console.log(MyInformation.name); // Rayhan
console.log(MyInformation.age); // 21 
```

Enter fullscreen mode Exit fullscreen mode

## 3。混合命名&默认出口

我们可以一次导出普通对象和默认对象

```
// ---------- lib.js
var info = {
 name: 'Rayhan',
 age: 21
};
var fullName = 'King Rayhan';
export default fullName;
export { info };
// ----------- main.js
import name , { info } from './lib'
console.log(name); // King Rayhan
console.log(info.age); // 21 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，我们可以用任何名称调用默认对象，但非默认对象必须是准确的名称。

```
Import Syntax
import defaultExport from "module-name";
import * as name from "module-name";
import { export } from "module-name";
import { export as alias } from "module-name";
import { export1 , export2 } from "module-name";
import { export1 , export2 as alias2 , [...] } from "module-name";
import defaultExport, { export [ , [...] ] } from "module-name";
import defaultExport, * as name from "module-name";
import "module-name";
Export Syntax
export { name1, name2, …, nameN };
export { variable1 as name1, variable2 as name2, …, nameN };
export let name1, name2, …, nameN; // also var, function
export let name1 = …, name2 = …, …, nameN; // also var, const

export default expression;
export default function (…) { … } // also class, function*
export default function name1(…) { … } // also class, function*
export { name1 as default, … };

export * from …;
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …; 
```

Enter fullscreen mode Exit fullscreen mode