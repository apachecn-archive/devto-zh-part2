# 雷诺 RPC 工作日志#2

> 原文：<https://dev.to/horia141/raynor-rpc-work-log-2-5aod>

[上次](https://dev.to/horia141/raynor-rpc-work-log-1-4if0-temp-slug-4364995)我勾画了雷诺 RPC。写了一些代码放在 GitHub 上，但没什么大的。这一次我要擦亮那个东西。主要的焦点是获得正确的注释。

这些注释的主要思想是，它们应该是描述服务和制作 RPC 的机制的更好的方式。考虑上一集的示例服务:

```
import { ArrayOf, MarshalWith, MarshalFrom } from 'raynor'
import { nop, Method, Output, Param, Throws } from 'raynor-rpc'
import * as r from 'raynor'

export class Book {
    @MarshalWith(r.IdMarshaller)
    id: number;
    @MarshalWith(r.StringMarshaller)
    title: string;
}

export class TestBookError extends Error {
    constructor() {
        super('Tried to do something with a test book!');
    }
}

export class LibraryService {
    @Method() @Idempotent
    @Output(ArrayOf(MarshalFrom(Book)))
    async getBooks(): Promise<Book[]> {
        return nop();
    }

    @Method()
    @Output(MarshalFrom(Book))
    @Throws(TestBookError)
    async updateBooks(
        @Param(r.IdMarshaller) bookId: number,
        @Param(r.StringMarshaller) newTitle: string): Promise<Book> {
        return nop(bookId, newTitle);
    }
} 
```

它存在于源代码级别。如果你愿意的话。但是我们真的希望它在运行时存在，这样它就可以作为所有其他我们想做的事情的基础——生成的客户端和服务器以及围绕它的所有 RPC 机器。所以我们会*实际上*希望它是一个描述服务的数据结构。大概是这样:

```
const libraryServiceDescriptor = {
    "name": "LibraryService",
    "methods": {
        "getBooks": {
            "output": {
                "hasOutput": true
                "marshaller": ArrayOf(MarshalFrom(Book))
            }
            "input": [],
            "idempotent": true
        },
        "updateBook": {
            "output": {
                "hasOutput": true,
                "marshaller": MarshalFrom(Book)
            },
            "input": [{
                "index": 0,
                "marshaller": r.IdMarshaller
            }, {
                "index": 1,
                "marshaller": r.StringMarshaller
            }],
            "idempotent": false
        }
    }
} 
```

但我觉得说这是读书写字都难看的，也不是轻描淡写。它实际上非常接近 WSDL 和其他 XML 噩梦。幸运的是，TypeScript 的 decorators 允许足够的自省来完成从源代码级构造到描述的转换。所以我们得到了两个世界的最好的东西——代码中漂亮的语法和易于使用的数据结构。

| **注意**:还有其他方法可以得到这个。你可以有一个单独的程序来解析源文件并从中提取信息。至少在 JavaScript 生态系统中，这不是一个奇怪的选择，因为大多数团队会使用`tsc`、`babel`、`webpack`等。同上，gRPC 或 Thrift 函数是这样的。|

所以最初的游戏是构建注释并让它们提取适当的数据。为了完整起见，这里是当前设置:`Service`、`Method`、`Output`、`NoOutput`、`Throws`和`Param`。

描述符本身有以下类型:

```
interface ServiceDescriptor {
    name: string;
    methods: Map<string, MethodDescriptor<any>>;
}

interface MethodDescriptor<T> {
    name: string;
    output?: OutputDescriptor<T>;
    errors?: ErrorDescriptor;
    params: Array<ParamDescriptor<any>>;
}

interface OutputDescriptor<T> {
    hasOutput: boolean;
    marshaller: Marshaller<T>|null;
}

interface ErrorDescriptor {
    errorConstructors: Set<Constructor<Error>>;
}

interface ParamDescriptor<T> {
    index: number;
    marshaller: Marshaller<T>;
    required: boolean;
} 
```

实际的电流源可以在这里找到[。但是仍然有一些问题，我还没有设法将参数的类型捕获为通用类型，只是为了输出。](https://github.com/horia141/raynor-rpc/blob/master/src/core.ts)

| **注意**:这里的一个策略是允许 RPC 方法有一个*单个*参数。无论如何，这符合设计服务时的*最佳*实践，因为当你允许多个参数以及添加和删除它们时，会有很多关于版本控制的问题。第二个策略是允许*最多*一个*高的*数量，比如 8 个，并以此类推。|

主要思想是，当我们在模块加载时运行了所有的装饰代码后，带注释的类有一个包含上面的数据结构的`__service`字段。注意，这个位非常不安全。我们没有机制说用`@Service`注释的类将拥有这个字段。所以有更多的防御性编程的例子。

例如，`Param`装饰器的代码看起来像:

```
function Param<T>(marshallerCtor: MarshallerConstructor<T>) {
    return function(target: any, methodName: string, parameterIndex: number) {
        const service = _ensureServiceDescriptor(target);
        const method = _ensureMethodDescription(service, methodName);

        method.params[parameterIndex] = {
            index: parameterIndex,
            marshaller: new marshallerCtor(),
            required: true
        };
    }
} 
```

注意，我们*可以*在这里捕获参数的类型，但是我们不能用当前的类型系统“存储”它。他们看起来都差不多，你可以在 [GitHub](https://github.com/horia141/raynor-rpc/blob/master/src/annotations.ts) 上查看。

**注意**:在 Raynor 中，我们实际上无法确保`MarshallerConstructor`使用的`Param<T>`中的`T`类型是与相同的*类型或者与参数的实际类型兼容。下面将实际通过编译:* 

```
updateBook(@RpcParam(r.StringMarshaller) bookId: **string** , ...) {
   ...
} 
```

暂时就这样了。在第 3 部分中，我们将把重点放在将描述转换成客户机和服务器对象上。预览——我们将同时做一个基于内存和 Express 的实现。在这种情况下，我发现同时针对两种方法非常有用，因为抽象和定义正确接口的可能性会更好。我们不会被特定方法的工作方式所束缚。我们担心一个外部环境或任何其他大功能。