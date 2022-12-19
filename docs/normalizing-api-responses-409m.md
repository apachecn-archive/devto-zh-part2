# 标准化 API 响应

> 原文：<https://dev.to/mattdevio/normalizing-api-responses-409m>

[Express.js](https://expressjs.com) 一直是我在 node 中构建 API 的 goto 框架。我经常遇到的一个问题是，如果有足够的自由度，每个 api 响应都会呈现自己的形状。当每个远程调用都必须考虑如何使用每个响应时，这就产生了一种奇怪的代码味道。

> 注意:所有的例子都使用了 [express v4](https://www.npmjs.com/package/express) 和[主体解析器](https://www.npmjs.com/package/body-parser)。

```
const todos = [{ ... }, { ... }]; // an array of todos

router.get('/todos', function(req, res, next){
    res.status(200);
    res.json({
        msg: 'So many things to do',
        todos: todos,
    });
});

router.post('/todos', function(req, res, next){
    const todo = {
        title: req.body.title,
        description: req.body.description,
        completed: false,
        id: uuid(), // generate a random id,
    };
    try {
        todos.push(todo); // imagine this might fail, todo is a remote db :)
    } catch (e) {
        return next(e);
    }
    res.status(201);
    res.json({
        message: 'Todo created',
        data: todo,
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

从上面可以看到，每个端点都有自己的响应结构，结构非常相似。两者都发送回一条消息和一个数据集，但是使用不同的密钥。当您开始在组合中加入错误时，这个问题会变得更加明显。

## 规范化 API 响应

我们可以通过创建一个返回对象的函数来解决这个问题。为了简单起见，这个对象将有 4 个键值对

*   数据-主数据，默认为对象，可以是任何类型
*   状态-请求是否成功，1 表示成功，0 表示失败
*   错误-处理过程中生成的一组错误
*   消息-用户友好的消息发生了什么

```
function apiResponse(data = {}, status = 1, errors = [], message = '') {
    return {
        data,
        status,
        errors,
        message,
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个好的开始，但是你的开发伙伴必须考虑参数的顺序。让我们通过接受一个对象作为参数并从其中析构我们需要的键来解决这个问题。

```
function apiResponse({ data = {}, status = 1, errors = [], message = '' }) {
    return {
        data,
        status,
        errors,
        message,
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这种解决方案有效，但它并不能保护我们不犯错误。初始化后，对象结构的完整性处于危险之中。让我们把 apiResponse 变成一个类，这样我们就可以获得更多的控制权。

```
class ApiResponse {
    constructor({ data = {}, status = 1, errors = [], message = '' }) {
        this._data = data;
        this._status = status;
        this._errors = errors;
        this._message = message;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在引擎盖下，`res.json()`会调用有效载荷上的`JSON.stringify()`对其进行编码。`stringify`的一个很酷的副作用是，如果一个对象有一个 [toJSON](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#toJSON()_behavior) 属性，它的值是一个函数，这个函数将被调用来定义对象如何被序列化。这意味着我们可以选择在 JSON 字符串中显示哪些键。

```
class ApiResponse {
    constructor({ data = {}, status = 1, errors = [], message = '' }) {
        this._data = data;
        this._status = status;
        this._errors = errors;
        this._message = message;
    }
    toJSON() {
        return {
            data: this._data,
            status: this._status,
            errors: this._errors,
            message: this._message,
        };
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，javascript 类没有私钥。我们拥有的最接近的东西是`Symbols`。让我们用这些来使我们的密钥“私有”。

```
const apiResponse = (payload = {}) => {

    const DataSymbol = Symbol('data');
    const StatusSymbol = Symbol('status');
    const ErrorsSymbol = Symbol('errors');
    const MessageSymbol = Symbol('message');

    class ApiResponse {
        constructor({ data = {}, status = 1, errors = [], message = '' }) {
            this[DataSymbol] = data;
            this[StatusSymbol] = status;
            this[ErrorsSymbol] = errors;
            this[MessageSymbol] = message;
        }
        toJSON() {
            return {
                data: this[DataSymbol],
                status: this[StatusSymbol],
                errors: this[ErrorsSymbol],
                message: this[MessageSymbol],
            }
        }
    }

    return new ApiResponse(payload);

} 
```

Enter fullscreen mode Exit fullscreen mode

Javascript 也没有类型，但是我们有`getters`和`setters`。我们可以用它们来检查作业的类型。这是我们代码的最终演变。

```
const apiResponse = (payload = {}) => {

    const DataSymbol = Symbol('data');
    const StatusSymbol = Symbol('status');
    const ErrorsSymbol = Symbol('errors');
    const MessageSymbol = Symbol('message');

    class ApiResponse {
        constructor({ data = {}, status = 1, errors = [], message = '' }) {
            this.data = data;
            this.status = status;
            this.errors = errors;
            this.message = message;
        }

        get data() {
          return this[DataSymbol];
        }

        set data(data) {
          if (typeof data === 'undefined')
              throw new Error('Data must be defined');
          this[DataSymbol] = data;
        }

        get status() {
          return this[StatusSymbol];
        }

        set status(status) {
          if (isNaN(status) || (status !== 0 && status !== 1))
            throw new Error('Status must be a number, 1 is OK, 0 is BAD');
          this[StatusSymbol] = status;
        }

        get errors() {
          return this[ErrorsSymbol];
        }

        set errors(errors) {
          if (!Array.isArray(errors))
            throw new Error('Errors must be an array');
          this[ErrorsSymbol] = errors;
        }

        get message() {
          return this[MessageSymbol];
        }

        set message(message) {
          if (typeof message !== 'string')
            throw new Error('Message must be a string');
          this[MessageSymbol] = message;
        }

        toJSON() {
            return {
                data: this.data,
                status: this.status,
                errors: this.errors.map(e => e.stack ? e.stack : e),
                message: this.message,
            }
        }
    }

    return new ApiResponse(payload);

} 
```

Enter fullscreen mode Exit fullscreen mode

getters 和 setters 还让我们能够在初始化后安全地改变响应对象。现在有趣的部分来了，使用我们新的`apiResponse`函数🎉！

```
const todos = [{ ... }, { ... }]; // an array of todos

router.get('/todos', function(req, res, next){
    res.status(200);
    res.json(apiResponse({
        data: todos,
        message: 'You have a lot todo!',
    }));
}); 
```

Enter fullscreen mode Exit fullscreen mode

**期望从 GET /todos 得到的响应**

```
{  "data":  [{  ...  },  {  ...  }],  "message":  "You have a lot todo!",  "errors":  [],  "status":  1,  } 
```

Enter fullscreen mode Exit fullscreen mode

目前就这些。这是我的第一篇文章，希望听到你的反馈。希望这对某个人有帮助。编码快乐！