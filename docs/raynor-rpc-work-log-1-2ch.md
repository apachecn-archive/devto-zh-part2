# 雷诺 RPC 工作日志#1

> 原文：<https://dev.to/horia141/raynor-rpc-work-log-1-2ch>

我打算用这篇博文尝试一些不同的东西，让它成为我正在尝试构建的一个小项目的工作日志。

你可能还记得[Raynor](https://dev.to/horia141/raynor-54ap-temp-slug-1053558)——我不久前的小型打字稿编组库。它允许人们在注释的帮助下，围绕序列化/反序列化和数据验证自动化大量样板文件。我在一些小项目中使用过它，我对它的工作方式非常满意。对于未来的主要版本来说，有很多东西需要润色和改进，但没有什么是非常糟糕的。

这让我想到我也应该把它用在其他事情上。作为 RPC 库的一部分是很自然的事情。这些类型的项目携手并进是相当自然的。你可以用 gRPC 或 Thrift 来举例。

所以这篇博文是一个我想称之为“雷诺 RPC”的东西的粗略设计文档。这比我通常的票价要贵一点。

重要的事情，首先。**那么，[我的动力是什么](https://5writers.com/2012/09/01/whats-my-motivation/)？**这主要是一次学习经历，尽管我确实希望在一个项目中测试它。不过，我确实认为只使用 JavaScript 的方法有其优点，因为它可以根据语言的具体情况和运行平台进行微调。据我所知，还没有这样的东西。当然，gRPC 或 Thrift 可以为 Node 生成 JavaScript，但它通常是 Java 或 C++之后的二等公民。它也限制了事情，但不像你想的那么多。人们可以从技术上使用 JavaScript 从嵌入式软件到云中的大型服务器。我还喜欢在 Raynor 中，你可以将小部分逻辑作为函数附加到你正在定义的实体上，这将延续到 Raynor RPC。根据定义，其他系统的 IDL 不允许这样的事情。

我想要的主要东西是:

*   保持雷诺的注释方法。
*   使用 Raynor 编组来确保客户端和服务器端的参数正常。
*   承认 RPC 的语义——所以使用`async/await`和`Promise` s，但是提供一些常规调用的细节。允许客户端处理服务器上引发的异常。
*   集成和利用 TypeScript 强大的类型系统。可能会有很多低层次的魔法发生，但是客户不应该接触到它。
*   有一个定义服务的地方。客户端和服务器基础结构应该由库自动派生。
*   应该是*运输不可知*。但是我将从 HTTP 开始，作为假定的*传输*，JSON 用于数据交换，并在 [Express](https://expressjs.com/) 之上构建服务器端支持。但是这些不应该是假设，并且添加 TCP、Koa 或二进制数据不应该显著改变事情。
*   应该允许从客户端发送请求元数据并在服务器端访问。
*   应该考虑到服务器端中间件。纯粹的 HTTP 服务器并不是唯一将身份验证放入中间件有意义的服务器。随着我开发这个东西，事情会变得更加充实。

现在，我希望*让*让代码看起来像下面这个小例子。假设我们有一个图书馆服务，它允许用户查询可用的书籍并更改特定书籍的标题。我们可以有`library-sdk`包，它定义了实体和服务。它既可以被库服务的客户机用来生成客户机对象和处理实体，也可以被服务器本身用来生成服务器对象。

```
// In index.ts
import { ArrayOf, MarshalWith, MarshalFrom } from 'raynor'
import { nop, Method, Output, Param, Throws } from 'raynor-rpc'
import * as r from 'raynor'

// A pretty standard Raynor annotated class. It is our service's _entity_.
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

// Now, we get to the service definition. At this point, we're only interested
// in its _interface_, and won't/can't specify anything about the
// implementation. This should ideally be an interface, but you can't annotate
// or otherwise work with it because it's a compile-time construct, which won't
// make it to the JavaScript side. Ditto for abstract methods. So we have to use
// a regular class, with a bit of library-provided glue to make things work.
export class LibraryService {
    // The @Method annotation marks a method as being a remote procedure call.
    // The nop() method is provided as a placeholder so TypeScript won't
    // complain. You can technically provide any body, but it _will_ be
    // ignored in the generated client and server classes. This _must_ be
    // an async function returning a `Promise`. At some point it will be
    // possible to check at runtime for this in the annotation, but not yet.
    @Method() @Idempotent
    @Output(ArrayOf(MarshalFrom(Book)))
    async getBooks(): Promise<Book[]> {
        return nop();
    }

    // The possible errors which can be thrown also need to be marked. Java
    // says hello! I'm not married to this idea though, and later designs
    // might drop it, with some small _reduction_ in the information carried
    // along with errors.
    @Method()
    @Output(MarshalFrom(Book))
    @Throws(TestBookError)
    async updateBooks(
        @Param(r.IdMarshaller) bookId: number,
        @Param(r.StringMarshaller) newTitle: string): Promise<Book> {
        return nop(bookId, newTitle);
    }

    // It's perfectly fine to have non @Rpc-ed methods here. They will be
    // available in the client and server classes, and no _magic_ will
    // happen to them.
    isTestId(bookId): boolean {
        return bookId < 10000;
    }
} 
```

在`library-server`应用程序中，我们实际上定义了`LibraryService`的实现，并使其作为 HTTP 服务器在网络上可用。它可能看起来像这样:

```
import * as express from 'express'
import { ExpressHttpServer, Server} from 'raynor-rpc'

import { Book, TestBookError, LibraryService } from 'library-sdk'

// This is just a regular class which extends `LibraryService` and overrides
// it's existing methods. All @Rpc methods need to be overriden! Other than
// that, the class can behave like a regular one. A manual dependency injection
// occurs in this one, for example to pass in a repository object which deals
// with storage interaction.
class LibraryServiceImpl extends LibraryService {
    constructor(private readonly libraryRepository: LibraryRepository) {
    }

    // Nothing special needs to happen here - no annotation or anything. Though
    // you need to respect the signature of `getBooks`. Luckily TypeScript
    // enforces this - there's no function overloading with inheritance.
    async getBooks(): Promise<Book[]> {
        const books = await libraryRepository.getAllBooks();
        return books.filter(b => !this.isTestId(b));
    }

    async updateBooks(bookId: number, newTitle: string): Promise<Book> {
       if (this.isTestId(bookId)) {
           throw new TestBookError();
       }

       const book = await libraryRepository.getBookById(bookId);
       book.title = newTitle;
       await libraryRepository.updateBook(bookId, book);
    }
}

// Now, we setup the server. Notice that we're working with the implementation
// object now.
const repository = new LibraryRepository();
const serviceImpl = new LibraryServiceImpl(repository);

// Not really 100% with what happens next. It'll get fleshed out later though.
// Implementation wise I'd expect to have an `GET /getBooks` endpoint returning
// some JSON, and a `POST /updateBooks` endpoint accepting some JSON and
// returning some JSON as well. But that's an implementation detail.
const server = new Server(serviceImpl);
const httpServer = new ExpressHttpServer(server);

// The rest is a regular express setup. You can define other routes and routers
// as you like.

const app = express.Express();
app.use("/api/library", httpServer.router);

app.listen(80, '0.0.0.0', () => {
    console.log('We\'re in business');
}); 
```

最后，在将前端实现为 SPA 的`library-frontend`包中，我们希望使用库服务。事情应该是这样的:

```
import { Client, HttpClient } from 'raynor-rpc'

import { Book, LibraryService, TestBookError } from 'library-sdk'

async function markBadBooks(): Promise<Book[]> {
    // First we setup the client. Notice that we're working with the _interface_
    // object.
    const client = new Client(LibraryService);
    const httpClient = new HttpClient(client, 'http://library-server/api/library');

    try {
        const books = await client.getBooks();
        const badBooks = books.filter(b => b.title.contains('Bad'));
        for (const badBook of badBooks) {
            // Done serially here for simplicity
            await client.updateBook(badBook.id, `${badBook.title} - WATCH OUT`);
        }
    } catch (e) {
        if (e instanceof TestBookError) {
            console.log('Tried to work with a test book! Not good');
            return;
        }
        console.log(e);
    }
} 
```

好了，这应该是客户端和服务器组件的 v1 API 的大致轮廓。以前遇到过 gRPC/Thrift 的人应该都很熟悉。希望它对其他人来说足够直观。需要注意的一件重要事情是，在客户端，没有任何东西需要用*代码*来使用它。一切都由库来完成，因为所有能做的事情通常都是非常重复和机械的。这里我指的是参数编组、HTTP 连接设置、等待响应、解析响应并检查其有效性、处理各种错误等。在服务器端也有同样的问题，但是你也必须编写一些代码。但只是实际的*重要的*部分。一旦像`updateBooks`这样的方法执行，你可以确定`title`存在并且是一个字符串，或者`bookId`是一个非负整数。

就代码而言，在 github.com/raynor-rpc 的[有一些*草图*——但出于某种原因它被标上了版本`'0.0.0'`。基本上，只需要检查基于注释的 API 是否可以工作，仅此而已。我将在此基础上发表新的文章，希望我能以此为基础建立一个不错的系列。](https://github.com/horia141/raynor-rpc)

* * *

为了让我记住一些事情，这里有一些其他的好东西:

*   幂等方法的一个注解。这可能会影响生成的服务器端 APIs 即，在使用 HTTP 基础设施时，有一个`GET`,而不是一个`POST`或`PUT`。
*   更清楚地标记 void 输出的注释。
*   对于具有空输出的方法，支持“一劳永逸”或单向方法，如 Thrift。
*   支持可选参数。
*   API 版本和兼容性的一些概念。
*   支持用于前端的[后端的服务代理。](https://samnewman.io/patterns/architectural/bff/)

由于我们正处于梦想阶段，我们还可以进一步提供:

*   呼叫机制——超时、重试、流水线、背压等。
*   API 文档和调试交互 UI，像 Swagger。
*   内置对 API 调用的监控、跟踪和日志记录的支持。
*   支持流响应，特别是通过新版本 JavaScript 中的新[异步迭代](http://2ality.com/2018/04/async-iter-nodejs.html)。
*   支持流请求数据，如 gRPC 中的双向流。