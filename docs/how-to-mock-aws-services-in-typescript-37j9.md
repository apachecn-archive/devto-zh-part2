# 如何在 TypeScript 中模拟 AWS 服务

> 原文：<https://dev.to/kylegalbraith/how-to-mock-aws-services-in-typescript-37j9>

我最近一直在做一个新项目[来自动将博客文章转换成音频](https://www.parler.io/)，它有几个不同的无服务器微服务。

每个微服务都使用[无服务器框架](https://serverless.com/)进行初始化，通常由一个或多个 Lambda 函数以及一个 SNS 主题、SQS 队列、DynamoDB 表或 API 网关端点组成。

我决定使用 TypeScript 而不是我默认的传统 Javascript 来实现每个无服务器微服务。我之所以选择这条路，是因为我希望为更具弹性的代码库提供静态类型检查。

Typescript 是一种非常酷的语言，近年来越来越受欢迎。除了静态类型检查之外，它还提供了许多不同的好处:

*   可选类型。
*   类型推断。
*   访问新的 ES 功能。
*   选项编译成不同的 JS 版本。
*   通过`.d.ts`清除库 API 定义。
*   类和模块支持。
*   许多很好的工具来支持环境。

在构建这些服务的过程中，我遇到了一个问题，这个问题让我停滞了几个小时。我正在为我的一个服务中的一个新适配器编写单元测试，该服务利用 DynamoDB 进行存储。

为了有效地测试适配器，我需要在`aws-sdk`中模拟来自 DynamoDB 客户端的响应。事实证明，这比我最初想象的要更具挑战性。

因此，我认为有必要就我发现的嘲笑 aws-sdk 的两种方法写一篇简短的博文。

### 用 TypeMoq 在打字稿中嘲讽

有很多绝妙的工具可以模仿 TypeScript。我为我的作品选择的是 [TypeMoq](https://github.com/florinn/typemoq) 。它非常类似于中的 [Moq](https://github.com/moq/moq) 库。NET 框架。

开始相当简单。假设我们有一段如下所示的代码。

```
export class MessageProcessor {
    conversionAdapter: ConversionAdapter

    constructor(conversionAdapter: ConversionAdapter) {
        this.conversionAdapter = conversionAdapter;
    }

    processJobs(records: IConversionModel[]): Promise<boolean>  {
        return new Promise((resolve, reject) => {
            this.conversionAdapter.convert(record).then((result) => {
                resolve(true);
            }).catch((err) => {
                reject(err);
            });
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码在名为`ConversionAdapter`的类上调用`convert`方法。从代码中，我们可以看到该方法返回了一个`Promise`。我们用那个承诺的结果来解决我们外在的承诺，或者如果有错误就拒绝它。

我们可以通过使用 TypeMoq 在`ConversionAdapter`上模拟`convert`方法的结果来为`processJobs`方法设置一个测试。

```
describe("MessageProcessor", function () {
    before(function () => {
        this.model = new ConversionModel();
        this.model.url = "url";
        this.model.content = "content";
        this.model.title = "title";
        this.model.userId = "1234-userid";
    });

    it("processJobs success", function (done) => {
        var mockAdapterFactory = TypeMoq.Mock.ofType<IConversionAdapter>(ConversionAdapter);
        mockAdapterFactory.setup(m => m.convert(this.model)).returns(() => Promise.resolve(1));

        var processor = new MessageProcessor(mockAdapterFactory.object);
        var pass = [ this.model ];
        processor.processJobs(pass).then((result) => {
            expect(result).to.eql(true);
            done();
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里的关键是我们对`mockAdapterFactory`的设置。我们使用`TypeMoq.Mock.ofType<IConversionAdapter>(ConversionAdapter)`来创建适配器的模拟。然后，我们设置了返回`Promise.resolve(1)`的`convert`方法。

从那里开始，我们只需要将实际的模拟传递到我们的类中。

现在我们可以等待我们的承诺解决，并验证我们得到的结果是真实的。

好了，现在我们有了在 Typescript 中模仿的基础。让我们继续讨论如何模仿像 SNS、SQS 或 DynamoDB 这样的 AWS 服务。

### TypeScript 中模拟 AWS 服务的两种方式

明确地说，在 TypeScript 中模仿`aws-sdk`的方式不止这两种。这只是我开始工作的两种方式，我想分享我的经验，希望能在未来节省一些时间。

先给自己一些背景吧。这里我们有一个类，`StorageAdapter`，它在内部使用 DynamoDB。

```
export class StorageAdapter implements IStorageAdapter {
    dynamoClient: DynamoDB;
    tableName: string;

    constructor(dynamoClient: DynamoDB, tableName: string) {
        this.tableName = tableName;
        this.dynamoClient = dynamoClient;
    }

    readItem(id: string): Promise<DynamoDB.GetItemOutput> {
        return new Promise((resolve, reject) => {
            let itemInput: DynamoDB.Types.GetItemInput = {
                TableName: this.tableName,
                Key: this.setKey(id)
            };

            this.dynamoClient.getItem(itemInput).promise().then((data: PromiseResult<DynamoDB.GetItemOutput, AWSError>) => {
                if (data.$response && data.$response.error != null) {
                    reject(data.$response.error);
                } else {
                    resolve(data.Item);
                }
            });
        });
    }

    private setKey(id: string): Key {
        let keyStruct: Key = {
            "ConversionId": {
                S: id
            }
        }

        return keyStruct;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里的`readItem`中，我们从`aws-sdk`调用 DynamoDB 客户端上的`getItem`。此外，我们告诉 SDK，我们希望通过在调用结束时追加`.promise()`来返回一个承诺。

然后，我们接受那个呼叫的结果，并根据是否有错误来拒绝或解决我们的外部承诺。

现在，让我们深入研究在 DynamoDB 客户端上模拟`getItem`的两种方法，以便正确测试我们的代码。

#### 方法一:使用 TypeMoq

我们的第一个选择是使用 TypeMoq。

```
describe("StorageAdapter", function () => {
    before(function () => {
    });

    it("readItem correctly", function (done) => {
        var expectedPr: PromiseResult<DynamoDB.GetItemOutput, AWSError> = {
            Item: {
                "ConversionId": {
                    S: "fooid"
                },
                "Email": {
                    S: "kyle@test.com"
                },
                "Status": {
                    S: "Converting"
                },
                "Characters": {
                    N: `${123}`
                },
                "Url": {
                    S: "https://cloudfront.com"
                }
            },
            $response: null
        };

        var mockAdapterFactory = TypeMoq.Mock.ofType<DynamoDB>(DynamoDB);
        var mockRequestFactory = TypeMoq.Mock.ofType<Request<DynamoDB.GetItemOutput, AWSError>>(Request);      

        mockRequestFactory.setup(m => m.promise()).returns(() => Promise.resolve(expectedPr));
        mockAdapterFactory.setup(m => m.getItem(TypeMoq.It.isAny())).returns(() => mockRequestFactory.object);

        var storage = new StorageAdapter(mockAdapterFactory.object, "testTable");
        storage.readItem("fooid").then((result) => {
            console.log(result);
        }).catch((err) => {
            console.error(err);
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们首先必须设置我们的 DynamoDB 模拟，这就是我们在`mockAdapterFactory`中看到的。然后我们必须创建一个由`getItem`返回的被模仿的`Request`对象，我们可以用我们的`mockRequestFactory`变量看到它。

现在，我们可以配置模拟来返回测试代码所需的内容。

首先，我们在`mockRequestFactory`上配置`promise()`来返回一个解析的承诺，并在`expectedPR`中声明我们期望的`DynamoDB.GetItemOutput`。

接下来，我们在`mockAdapterFactory`上配置`getItem()`来返回我们刚刚完成设置的模拟请求。

从那里，我们只需要通过将`mockAdapterFactory.object`传递给我们的`StorageAdapter`来传递 DynamoDB 客户端的实际模拟。

如果你继续跟踪，我们需要使用 TypeMoq 的四行代码来模拟我们在 DynamoDB 上的`getItem`调用。这对于编写测试来说非常冗长，但是它确实提供了一个高层次的可见性，可以看到这个`getItem`调用成功完成所利用的类型。

让我们探索如何使用不同的模拟库来实现相同的目标。

#### 方法二:使用 aws-sdk-mock

还有另外一个嘲讽库， [aws-sdk-mock](https://github.com/dwyl/aws-sdk-mock) ，专门用来嘲讽`aws-sdk`。

我们实际上可以将 TypeMoq 的四行代码减少到只有一行代码。

```
describe("StorageAdapter", () => {
    before(() => {
    });

    it("readItem correctly", () => {
        var expectedPr: PromiseResult<DynamoDB.GetItemOutput, AWSError> = {
            Item: {
                "ConversionId": {
                    S: "fooid"
                },
                "Email": {
                    S: "kyle@test.com"
                },
                "Status": {
                    S: "Converting"
                },
                "Characters": {
                    N: `${123}`
                },
                "Url": {
                    S: "https://cloudfront.com"
                }
            },
            $response: null
        };

        AWSMock.mock('DynamoDB', 'getItem', Promise.resolve(expectedPr));
        var storage = new StorageAdapter(new DynamoDB(), "testTable");
        storage.readItem("fooid").then((result) => {
            console.log(result);
        }).catch((err) => {
            var x = 3;
        });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以不模仿 DynamoDB 客户端、请求以及与`getItem`相关联的承诺，而是模仿`getItem`并返回一个已解决的承诺。为了创建一个有价值的模拟并准确地测试我们的`StorageAdapter`类，需要做的工作要少得多。

这样做的好处是非常简洁明了。然而，目前`aws-sdk-mock`**和**在开发过程中没有任何影响我们智能感知的类型。

### 重述打字稿中的嘲讽 AWS

已经有相当多的文章介绍了如何模拟 AWS 服务，但是它们通常不包含 TypeScript。这不是问题，因为为普通 JavaScript 编写的任何东西都可以在我们的 TS 世界中使用，只是没有任何类型。这就是我们使用`aws-sdk-mock`所看到的。

测试是任何生产就绪服务的关键，即使在与 AWS 服务接口的低级适配器中也是如此。然而，为了准确地测试我们的代码，模仿 AWS 服务可能会很棘手。这通常是因为类型更复杂，例如，`.getItem().promise()`实际上返回了一个通用类型`PromiseResult<DynamoDB.GetItemOutput, AWSError>`。

我们在这里展示的是两种嘲笑`aws-sdk`的方法。我们使用 TypeMoq 的第一种方法演示了一种 TypeScript 风格，使用类型并逐步构建我们的模拟。我们构建了整个类型链，从请求一直到解决的承诺。

使用`aws-sdk-mock`的第二种方法展示了我们如何用一行代码模拟单个服务调用。

在方法一中，我们有类型和冗长，但是牺牲了简洁性，必须建立我们的模拟。方法二，我们有简洁性，但失去了类型，然后必须知道具体的方法调用和客户端声明。这里没有正确的答案，两者都很好，这取决于你的偏好。

另一方面，`aws-sdk-mock`致力于嘲笑`aws-sdk`。而 TypeMoq 可以在我们的 TypeScript 测试中普遍使用。事实上，我最终在我的服务中同时使用了这两者。TypeMoq 模仿我的类和接口，SDK 模仿 AWS 服务。

像开发代码中的大多数事情一样，没有唯一正确的答案。我希望我在模仿 TypeScript 中的`aws-sdk`的经历给你一个例子，告诉你如何在你自己的代码中做同样的事情。

和往常一样，如果你有关于 AWS 或这篇文章的问题或评论，请在下面留下你的评论。

### 您是否渴望了解更多关于亚马逊网络服务的信息？

如果你想开始你的 AWS 之旅，但却不知道从哪里开始，可以考虑查看我的课程。我们专注于在 AWS 上托管、保护和部署静态网站。让我们在使用时能够了解超过 6 种不同的 AWS 服务。在你掌握了基础知识之后，我们可以进入**的两个额外章节**来讨论更高级的主题，比如基础设施代码和持续部署。