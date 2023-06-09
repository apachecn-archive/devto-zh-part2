# 实用类型:IO 验证或如何在静态类型语言中处理基于 JSON 的 API

> 原文：<https://dev.to/stereobooster/pragmatic-types-io-validation-or-how-to-handle-json-based-apis-in-statically-typed-language-1d9m>

具有静态类型的语言需要一个特殊的过程来将数据从外部(非类型化)世界(也称为输入输出或 IO)转换到内部(类型化)世界。否则，它们将失去承诺的类型安全。这个过程称为 IO 验证。附注:系统在运行时进行类型检查的事实意味着它是一个动态类型系统，但这将在另一篇文章中解释。

IO 验证的一个典型例子是解析来自 API 的 JSON 响应。

## 流程和打字稿

注意:代码在类型脚本和流程中看起来是一样的

```
// @flow
type Person = {
  name: string;
};
// $FlowFixMe or @ts-ignore
const getPerson = (id: number): Promise<Person> =>
  fetch(`/persons/${id}`).then(x => x.json()); 
```

Enter fullscreen mode Exit fullscreen mode

我们希望`getPerson`会返回`Person`的`Promise`，我们欺骗了类型系统，让它相信它总是这样，但实际上，它可以是任何东西。如果 API 响应看起来像什么:

```
{
  "data": { "name": "Jane" },
  "meta": []
} 
```

Enter fullscreen mode Exit fullscreen mode

这将导致函数中某处出现运行时错误，该函数需要`Person`类型。所以即使我们的静态类型系统没有发现错误，它们仍然可能存在。让我们通过添加 IO 验证来解决这个问题。

```
// it is guaranteed that this function will return a string
const isString = (x: any): string => {
  if (typeof x !== "string") throw new TypeError("not a string");
  return x;
};

// it is guaranteed that this function will return an object
const isObject = (x: any): { [key: string]: any } => {
  if (typeof x !== "object" || x === null) throw new TypeError("not an object");
  return x;
};

// it is guaranteed that this function will return an Person-type
const isPerson = (x: any): Person => {
  return {
    name: isString(isObject(x).name)
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个函数，它保证返回 Person 或者抛出一个错误，所以我们可以:

```
// without need to use $FlowFixMe
const getPerson = (id: number): Promise<Person> =>
  fetch(`/persons/${id}`)
    .then(x => x.json())
    .then(x => {
      try {
        return isPerson(x);
      } catch (e) {
        return Promise.reject(e);
      }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果我们考虑到承诺中抛出的任何异常都将变成拒绝的承诺，我们可以写:

```
// without need to use $FlowFixMe
const getPerson = (id: number): Promise<Person> =>
  fetch(`/persons/${id}`)
    .then(x => x.json())
    .then(x => isPerson(x)); 
```

Enter fullscreen mode Exit fullscreen mode

这是在动态和静态类型系统之间建立桥梁的基本思想。流程中的一个完整示例是[这里的](https://flow.org/try/#0CYUwxgNghgTiAEAzArgOzAFwJYHtVJAzAAsAKAZwxi1QHMAaeHAB2z3IH4AueAbwF8AlDwAKMHAFss5EAB5eAK3J4upQQF4AfGMnS5UVAE9N-TQG4AUAHor8AAKIIOAO4WMh5ghEgYy-Or4LeHhUKAkQHkpqOkt+SzB2DHhpAGUqGlp4ANIADx4DQ2F4KIyszUDgrER4UndPHGqc+ABCdQCAIhK6dsF4DGJxZxCQIYAVDxAAURhxGFJ21BwkqGL07sFLYLgMZBh8HNj4xOTyAHkAIwVwJOy8+AKi3ngAbQBrEENItdoAXXyjeD8MoVZLVWoTBrwJqtDo4S7XdrwAA+SKhWTaIWQEAgvX6g2GYwm01m80Wy3wcKumB6m3g212+0OFgSqEoJ28vjwWRqdweoh8fmBvCCdMIDJBwVC4R4qW+pGkFypGFyggAdFKQIIRXELHFgszjrRCBzBdksMAeKhkBJzj4ijopDJZCa8OUtCLEIQSKQAAZWTyc1lWAAkvHN-B9WuCwVV-RAqFywJyqqUeDUUejseI8cTWnZArTOUEGyAA)。打字稿中的一个完整例子是[这里](https://www.typescriptlang.org/play/index.html#src=type%20Person%20%3D%20%7B%0D%0A%20%20name%3A%20string%3B%0D%0A%7D%3B%0D%0Aconst%20isString%20%3D%20(x%3A%20any)%3A%20string%20%3D%3E%20%7B%0D%0A%20%20if%20(typeof%20x%20!%3D%3D%20%22string%22)%20throw%20new%20TypeError(%22not%20a%20string%22)%3B%0D%0A%20%20return%20x%3B%0D%0A%7D%3B%0D%0Aconst%20isObject%20%3D%20(x%3A%20any)%3A%20%7B%20%5Bkey%3A%20string%5D%3A%20any%20%7D%20%3D%3E%20%7B%0D%0A%20%20if%20(typeof%20x%20!%3D%3D%20%22object%22%20%7C%7C%20x%20%3D%3D%3D%20null)%20throw%20new%20TypeError(%22not%20an%20object%22)%3B%0D%0A%20%20return%20x%3B%0D%0A%7D%3B%0D%0Aconst%20isPerson%20%3D%20(x%3A%20any)%3A%20Person%20%3D%3E%20%7B%0D%0A%20%20return%20%7B%0D%0A%20%20%20%20name%3A%20isString(isObject(x).name)%0D%0A%20%20%7D%3B%0D%0A%7D%3B%20%20%0D%0Aconst%20getPerson%20%3D%20(id%3A%20number)%3A%20Promise%3CPerson%3E%20%3D%3E%0D%0A%20%20fetch(%60%2Fpersons%2F%24%7Bid%7D%60)%0D%0A%20%20%20%20.then(x%20%3D%3E%20x.json())%0D%0A%20%20%20%20.then(x%20%3D%3E%20isPerson(x))%3B)

## 库

每次手工编写这些类型的验证并不方便，相反，我们可以使用一些库来为我们做这些。

### [讽刺](https://github.com/jamiebuilds/sarcastic/)讨流

最少，可能阅读源代码和理解。**缺点** : [错过`union`型](https://github.com/jamiebuilds/sarcastic/pull/3)。

```
import is, { type AssertionType } from "sarcastic"
const PersonInterface = is.shape({
  name: is.string
});
type Person = AssertionType<typeof PersonInterface>
const assertPerson = (val: mixed): Person =>
  is(val, PersonInterface, "Person")
const getPerson = (id: number): Promise<Person> =>
  fetch(`/persons/${id}`)
    .then(x => x.json())
    .then(x => assertPerson(x)); 
```

Enter fullscreen mode Exit fullscreen mode

### [io-ts](https://github.com/gcanti/io-ts) 为打字稿

好，高级，心里有 FP。

```
import * as t from "io-ts"
const PersonInterface = t.type({
  name: t.string
});
type Person = t.TypeOf<typeof Person>
const getPerson = (id: number): Promise<Person> =>
  fetch(`/persons/${id}`)
    .then(x => x.json())
    .then(x => PersonInterface.decode(x).fold(
       l => Promise.reject(l),
       r => Promise.resolve(r)
     )); 
```

Enter fullscreen mode Exit fullscreen mode

## 发电机

不需要手动编写“IO 验证器”，相反[我们可以使用工具从 JSON 响应](https://transform.now.sh/)中生成它。此外，检查[类型-o-rama](https://github.com/stereobooster/type-o-rama) 的所有类型转换。带 IO 验证的发电机用方框表情符号标记。

[这篇文章是](https://dev.to/t/pragmatictypes)系列文章的一部分。在[推特](https://twitter.com/stereobooster)和 [github](https://github.com/stereobooster) 上关注我。