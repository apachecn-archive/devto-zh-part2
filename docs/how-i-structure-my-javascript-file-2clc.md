# 我如何构建我的 JavaScript 文件

> 原文：<https://dev.to/antjanus/how-i-structure-my-javascript-file-2clc>

很多人问我如何写我的 JavaScript——好吧，这是一个谎言，没有人问我这个问题，但如果他们问了，我想让他们看看这篇文章。在阅读了 [Clean Code](https://amzn.to/2GHMKKD) (以及其他书籍)之后，在使用了多年的 PHP 之后，我采用了自己多年的代码风格。是的，PHP，不要挑剔它，它有一个很棒的社区和很棒的编码标准。当然，多年来与其他人一起编写 JS，并遵循来自不同公司的风格指南。

这个结构不依赖于 JS 模块，但是我现在倾向于只写 JS 模块，所以我会使用它们。

其结构，概括起来如下:

```
 //imports
    import fs from 'fs';
    import utils from 'utils';

    import db from '../../../db';

    import { validatePath } from './readerHelpers';

    // constants
    const readDir = utils.promisify(fs.readDir);
    const knex = db.knex;

    // main exports
    export async function fileReader(p) {
      validatePath(p);

      return await readFile(p);
    }

    // core logic
    function readFile(p) {
     // logic
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 进口

文件的顶部是导入。有道理，他们被提升到高于一切。导入的顺序并不重要，除非你使用一些钩子(比如巴别塔钩子),所以我倾向于使用这样的结构:

*   本机模块——节点本机的内容
*   库模块——lodash、knex 等等
*   本地图书馆——如`../db`
*   本地文件——如`./helpers`或类似文件

保持我的模块有组织，让我更容易看到我正在导入什么，我实际上在使用什么。当我开始写代码时，我也倾向于这样写依赖关系。

我倾向于完全不关心字母排序(除了在析构的导入中)，我真的看不出这有什么意义。

原生模块
我倾向于将原生模块放在最上面，并按照主题保持清晰的组织，就像这样:

```
 import path from 'path';
    import fs from 'fs';

    import util from 'util'; 
```

Enter fullscreen mode Exit fullscreen mode

如果我在浏览器中，我显然会跳过这一步。

我尽可能只从库中导入我需要的东西，但是我还是会按照一些主题来分组。

```
 import knex from 'knex';
    import { clone } from 'lodash'; 
```

Enter fullscreen mode Exit fullscreen mode

我还注意到，如果我正在进行默认导入(例如 knex 导入)，我倾向于将它放在库模块的顶部，而将我的析构导入放在较低的位置。没有必要，但我喜欢它的视觉效果。

**本地/内部库**
本地库，我指的是本地共享的模块，比如与书架建立连接的`db.js`文件。或者，在我工作的情况下，我们有几个库来处理在我们的产品中使用的数字和计算。

```
 import db from '../../../db';
    import calculators from '../../../lib/calculators'; 
```

Enter fullscreen mode Exit fullscreen mode

**本地文件**
最后，我导入本地文件，这些文件通常与我正在处理的文件在同一个文件夹中，或者最多在一个目录下。例如，我为 Redux 编写了一个 reducer，并将它放在与其他 reducer 分开的文件夹中。在这个文件夹中，我还保存了一个帮助文件，通常命名为`[reducer name]Helpers.js` :

```
 import { assignValue, calculateTotal } from './calculationReducerHelpers'; 
```

Enter fullscreen mode Exit fullscreen mode

## 常数

在我导入了所有的依赖项之后，我通常会做一些前期工作，这些工作将会在模块的剩余部分中用到。例如，我从我的`Bookshelf`实例中提取`knex`。或者我可以设置值常量。

```
 const knex = db.knex;

    const pathToDir = '../../data-folder/'; 
```

Enter fullscreen mode Exit fullscreen mode

使用非常数通常表明我依赖于某种类型的单例。我尽量避免这些，但有时这要么是必要的，因为没有其他简单的方法，要么没什么关系(比如一次性命令行脚本)。

## 出口

在我基本上设置了所有模块级的依赖关系之后:无论它们是常量值还是导入的库，我都试图将我的导出分组在文件的顶部。基本上，这是我放置函数的地方，这些函数充当模块的粘合剂，实现模块的最终目的。

在 Redux 的情况下，我可能会导出一个 reducer，然后将工作分开并调用相关的逻辑。在 ExpressJS 的情况下，我可能在这里导出我的所有路线，而实际的路线逻辑如下。

```
 import { COUNT_SOMETHING } from './calculationActions';
    import helpers from './calculationHelpers';

    export function calculationReducer(state, action) {
      switch (action.type) {
        case COUNT_SOMETHING:
          return calculateSomething(state, action);
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我想提一下，这不是我导出函数的唯一部分。

我觉得模块系统的工作方式使得在暴露尽可能窄的 API 和导出函数以在测试中使用它们之间划清界限有点困难。

例如，在上面的例子中，我从来不想在模块之外使用`calculateSomething`。我不完全确定 OOP 语言如何处理私有函数的测试，但这是一个类似的问题。

## 核心逻辑

这可能看起来很奇怪，但核心逻辑对我来说是永恒的。我完全理解人们何时翻转出口和核心逻辑，但这对我来说很好，原因有很多。

当我打开一个文件时，顶层函数告诉我在抽象的步骤中会发生什么。我喜欢这样。我喜欢看一眼就知道文件会做什么。我做了很多 CSV 操作并插入到 DB 中，顶级函数总是一个容易理解的过程，其流程如下:`fetchCSV → aggregateData → insertData → terminate script`。

核心逻辑总是包含从上到下的导出中发生的事情。所以在内联的例子中，我们会有这样的东西:

```
 export async function importCSV(csvPath) {
      const csv = await readCSV(csvPath);
      const data = aggregateData(csv);

      return await insertData(data);
    }

    function aggregateData(csv) {
      return csv
        .map(row => {
         return {
           ...row,
           uuid: uuid(),
           created_at: new Date(),
           updated_at: new Date(),
         };
        })
      ;
    }

    function insertData(data) {
      return knex
        .batchInsert('data_table', data)
      ;
    } 
```

Enter fullscreen mode Exit fullscreen mode

注意`readCSV`不在那里。这听起来很普通，我会把它放到一个助手文件中，然后导入到上面。除此之外，你可以再次看到我的出口与非困境。我不希望`aggregateData`在模块之外可用，但是我仍然想测试它。

除此之外，我倾向于将“更丰富”的功能放在顶部，将较小的功能放在下面。如果我有一个模块特定的实用函数，一个我在不止一个地方使用但只在模块内使用的函数，我会把它们一直放在底部。基本上我按:复杂+使用排序。

所以优先顺序是:

*   核心逻辑函数—顶级导出按使用顺序使用的函数
*   更简单/更小的函数—核心逻辑函数使用的函数
*   效用函数—在模块的多个地方使用的小函数(但不导出)

**核心逻辑函数**
核心逻辑函数就像我导出函数的“子胶”。根据模块的复杂程度，这些可能存在，也可能不存在。功能的分解不是必需的，但是如果一个模块变得足够大，核心逻辑功能就像主功能中的步骤。

如果你正在写 React 或 Angular 之类的东西，这些组件将是我上面提到的导出函数。但是您的核心逻辑功能将是各种监听器或数据处理器的实现。有了 Express，这些将是你的具体路线。在 Redux reducer 中，这些 Redux reducer 是链中足够远的单个 reducer，没有 switch/case 语句。

如果你在 Angular 中，在类中而不是在整个文件的范围内组织这些函数是完全公平的。

```
 export FormComponent extends Component {
      function constructor() { }
      onHandleInput($event) {
        //  logic
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

**更简单/更小的功能**
这些功能通常是核心逻辑和纯实用程序的中间步骤。你可能只使用过一次，或者它们可能比实用函数稍微复杂一点。我可能会删除这一类别，并说“按照复杂性或工作量递减的顺序编写您的函数”。

这里没什么好提的。也许你的`onHandleInput`事件监听器需要一些逻辑来破坏`$event`数据，所以如果它是纯的，你可以把它从类中取出来，如果不是，你把它保存在类中，就像这样:

```
 export FormComponent extends Component {
      onHandleInput($event) {
        try {
          validateFormInput($event);
        } catch (e) {

        }
      }

      validateFormInput($event) {
        if (this.mode === 'strict-form') {
          throw new Error();
        }
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

**效用函数**
最后，效用函数。我倾向于把我的工具放在离我使用它们最近的地方。或者在同一文件中，或者在同一文件夹中(必要时)，在同一模块中，等等。每当使用从文件中一直扩展到项目的根或它自己的 NPM 模块时，我就将函数移出一个级别。

在我看来，效用函数应该永远是一个纯方法，也就是说，它不应该访问其作用域之外的变量，应该只依赖于传递给它的数据，并且没有任何副作用。除了在使用实用函数调用 API 或访问数据库时。因为这些被认为是副作用，我会说它们是唯一的例外。

```
 function splitDataByType(data) {
      return data
        .reduce((typeCollection, item) => {
          if (!typeCollection[item.type]) {
            typeCollection[item.type] = [];
          }

          typeCollection[item.type].push(item);

          return typeCollection;
        }, {});
    }

    function insertData(data, knex) {
      return knex
        .batchInsert('data', data);
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 还有别的吗？

当然可以！我认为每个人都有自己独特的编写代码的方式。在我每天写大量代码的这些年里，上述结构对我来说非常有用。最终，许多细微差别开始出现，我发现自己写代码更快，更享受，调试和测试更容易。

在我结束这篇文章之前，我想分享几个我已经非常习惯的编码花絮，这些花絮与文档结构关系不大，与编写实际代码的小偏好关系更大。

**提前回报**
当我发现提前回报的时候，我立刻恍然大悟。如果可以提前返回，为什么要在一个`else`语句中包装大块的代码呢？

我的经验是，如果早期返回条件小于剩余代码，我将编写早期返回，如果不是，我将颠倒代码，这样较小的代码块总是早期返回。

```
 function categorize(collection, categories) {
      return collection.reduce((items, item) => {
        if (!categories.includes(item.category) {
          return items;
        }

        if (!items[item.category]) {
          items[item.category] = [];
        }

        items[item.category].push(item);

        return items;
      }, {});
    } 
```

Enter fullscreen mode Exit fullscreen mode

早期回报在 switches 中也非常有效，我是 Redux 中的超级粉丝。

分号块
虽然我不再经常使用它(没有更漂亮的支持)，但我总是在单独一行上用分号结束函数链，在链的缩进左边一个缩进。这创建了一个整洁的代码块，代码不只是被挂起。

当然，这意味着我也更喜欢使用分号而不是 not。

```
 return fetchPost(id)
      .then(post => processPost(post))
      .then(post => updatePost(post, userInput))
      .then(post => savePostUpdate(post))
    ; // <- terminating semicolon 
```

Enter fullscreen mode Exit fullscreen mode

或者更好的写法，它可能是这样的:

```
 return fetchPost(id)
      .then(processPost)
      .then(updatePost(userInput))
      .then(savePostUpdate)
    ; // <- terminating semicolon 
```

Enter fullscreen mode Exit fullscreen mode