# 通过合同测试节省时间

> 原文：<https://dev.to/napicella/contract-testing-p53>

大家好！今天的主题是契约驱动测试。

我们支配时间的方式很重要。这对我们的生活很重要，对我们工作的公司也很重要。
我认为，最大限度地减少我们在非生产性活动上投入的时间，最大限度地利用我们在构建新想法上投入的时间，符合我们的最佳利益。

> 好吧，这是相当普遍的，甚至有人会说这只是常识。这和合同测试有什么关系？

这里我要说的是，在许多情况下，我们可以编写单元测试，而不是编写端到端的测试或手工测试。我们可以而且应该通过测试不同组件的集成，作为单元测试套件的一部分，来加速反馈循环(建立我们编写的代码实际上如预期那样工作的信心所需的时间)。

## 合同测试 101

当两个实体进行通信时，对供应商 API 的更改会导致其所有消费者失败。

> 当我讨论[健壮性原则](https://dev.to/napicellatwit/robustness-principle-with-jackson-3h5)时，我已经部分触及了这个话题，事实上我认为契约测试是一种在不放弃开发速度的情况下使我们的服务更加健壮的方法。

我们该怎么办？我们编写集成测试/端到端测试/手动测试。毫无疑问，这种测试有助于在生产结束前发现错误，但也有不利之处。

1.  运行它们需要建立基础设施、部署应用程序等。它们比单元测试慢得多，因为这是我们调用其他服务、进行网络调用和使用数据库的地方。因为我们知道它们很慢，并且需要所有的东西都准备好，所以我们不能像通常对单元测试那样频繁地运行它们。
2.  第一点的含义是，它们增加了开发反馈循环。
3.  由于我们与其他开发人员共享相同的管道，失败的集成测试并不一定意味着我们破坏了管道。所以，还需要更多的时间来调查到底发生了什么。

在研究合同测试的具体例子之前，让我们先来看看测试金字塔。

[![Test pyramid](img/730c675d072631b75b451ec7ddbc9e35.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TPNUHnhd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0wnre301xwja9mysa9y7.png)

金字塔是我们应该对每种类型进行多少测试的图形表示。在底部我们有单元测试，这意味着按比例我们应该写更多的单元测试。

*   它们确保我们的代码在孤立运行时能够正确运行
*   它们易于编写，运行速度快

每当我们对代码库进行代码更改或更新我们的依赖项时，我们都会编写大量的代码并执行它们。

基于我所说的集成测试和端到端测试，它们被放在金字塔的顶端并不奇怪。

## 举例

让我们看一个使用契约测试代替端到端测试的具体例子。

### 例 1

`Context`:客户端到服务的通信
`Scenario:`一个 ReactJs 应用程序，它为我们的用户管理待办事项列表。待办事项列表被序列化并发送到服务器，服务器将信息存储到 S3。
`What we want to test:`代码中的任何变化都不会导致系统中的回归，也就是说，我们仍然能够反序列化从服务器接收的待办事项列表，并将其显示在 react 组件中。

待办事项列表可能是这样的:

```
export class TodoList {
    items: Item[]

    constructor(items: Item[] = []) {
        this.items = items;
    }
}

// ......
// And this an item of our TodoList

export class Item {
    constructor(public description: string = '', 
                public isChecked: boolean = false) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

在代码的某个地方，我们发出一个 http 请求来获取 TodoList，反序列化它并更新视图的状态。

#### 接近 1(坏)

我们可以编写一个端到端的测试:

*   打开浏览器(使用量角器、selenium 或类似工具)
*   请求 react 应用程序
*   向待办事项列表添加一些项目
*   保存待办事项列表
*   再次获取待办事项列表
*   断言 ToDo 小部件中的信息显示正确，并且没有发生其他错误。

这正是我们想要避免写的东西；它缓慢而脆弱。通过使用契约测试，我们可以避免端到端的测试。

#### 接近 2(好)

首先，让我们为待办事项列表创建一个契约。
我们将要使用的工具:

*   Jest 用于单元测试(任何其他单元测试工具都可以正常工作)
*   [Typescript-json-schema](https://github.com/YousefED/typescript-json-schema) 转换 json 模式中的待办事项列表
*   [Json 模式验证器](https://github.com/tdegrunt/jsonschema)用于测试我们的待办事项列表是否符合约定

让我们定义一个在第一次运行测试时创建契约的效用函数:

```
getOrCreateContract = (instance, filename) => {
    if (schemaDoesNotExist(filename)) {
        // TJS comes from the Typescript-json-schema lib
        const program = TJS.getProgramFromFiles([resolve(filename)], {}, basePath);
        const schema = TJS.generateSchema(program, instance, settings);
        saveSchema(CONTRACT_FOLDER, filename);

        return schema;
    }

    return getSchema(CONTRACT_FOLDER, filename);
}; 
```

Enter fullscreen mode Exit fullscreen mode

为我们的待办事项列表生成的契约如下所示:

```
{  "$schema":  "http://json-schema.org/draft-06/schema#",  "definitions":  {  "Item":  {  "properties":  {  "description":  {  "default":  "",  "type":  "string"  },  "isChecked":  {  "default":  false,  "type":  "boolean"  }  },  "type":  "object"  }  },  "properties":  {  "items":  {  "items":  {  "$ref":  "#/definitions/Item"  },  "type":  "array"  }  },  "type":  "object"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们编写契约测试:

```
describe('ToDo List', () => {
    test('respect contract', () => {
        let todo = new TodoList([
            new Item('contract tests', true)
        ]);

        let contract = getOrCreateContract(todo, 'TodoList.schema.json');
        let contractValidator = new Validator();
        let respectContract = () => {
            contractValidator.validate(todo, contract);
        };

        expect(respectContract().error().length).toBe(0);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

与上面的端到端测试相比，这个测试给了我们完全相同的信心，但是它更快，并且不需要与真正的依赖项进行通信。

显然，有些情况下我们想更新合同。例如，我们可以添加一个命令行参数来覆盖现有的契约。

### 例 2

`Context:`服务对服务的沟通

我觉得这个帖子有点太长了。拥有服务到服务通信的结果需要引入更多的概念，所以我将在后续文章中写这个例子。

## 结论

集成测试在测试金字塔中占有重要的位置，但有时我们会过度使用它们。合同测试可以节省我们的时间！

如果你对题目感兴趣，请告诉我！这将有助于我了解我是否应该写一篇关于服务对服务沟通的后续文章。

谢谢！尼古拉