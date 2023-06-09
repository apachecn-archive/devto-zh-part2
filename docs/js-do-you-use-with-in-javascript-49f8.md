# JavaScript 里用‘with’吗？

> 原文：<https://dev.to/jochemstoel/js-do-you-use-with-in-javascript-49f8>

```
let a = document.createElement('a')
with(a) {
  setAttribute ('href', 'http://google.com/')
}

console.log(a.getAttribute('href')) // http://google.com/

let object = {
  id: 14904, 
  value: 'Main Street Avenue'
}

with(object) {
  console.log(id, value) // 14904, Main Street Avenue
}

let fs = require ('fs')
with(fs) {
  readFile('log.txt')
} 
```

Enter fullscreen mode Exit fullscreen mode

我很少碰到有人把*和*一起使用。一些消息来源认为它已经过时了，并建议不要使用它，但是根据我的经验，每个 JavaScript 解释器都理解它并像预期的那样工作。

> 不建议使用 with 语句，因为它可能会导致令人困惑的错误和兼容性问题。有关详细信息，请参见下面“说明”部分中的“歧义消除”一节。with 语句扩展了语句的作用域链。

来源: [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/with)

> 你的经历呢，你和我在一起吗？
> 
> > T3
> 
> T5】