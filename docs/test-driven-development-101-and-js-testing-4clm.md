# 测试驱动开发 101 和 JS 测试

> 原文：<https://dev.to/leanminmachine/test-driven-development-101-and-js-testing-4clm>

## **测试驱动开发周期**

TDD 周期:

1.  编写一个测试用例，它在开始时显然会失败。一次不要有超过一个失败的测试用例。首先实现最简单的算法，然后在发现一些模式时将其一般化。
2.  编写使测试用例通过的代码
3.  在代码基础上重构代码。当测试失败时，不要重构。让测试用例先通过。

老实说，我仍在努力掌握测试的诀窍，我仍在努力熟悉 js。

## **开玩笑写测试**

设置非常简单。
只是 npm 安装 jest。
记得在 package.json 里改，

```
 "scripts": {
    "test": "jest"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

命名测试文件[文件名].test.js
首先，我们编写我们的测试。
测试需要主模块。
我们可以给它赋一个变量名。
比如:

```
const arabicToRomanNumbers = require(“./arabicToRomanNumbers”); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以开始编写测试。

第一个测试应该是这样的，

```
test(“I is 1”, () => {
expect(arabicToRomanNumbers(“I”)).toBe(1);
}); 
```

Enter fullscreen mode Exit fullscreen mode

Name 文件名[文件名]。js
主 js 文件:写函数就行了。

```
function arabicNumber(string) {
if (string == “1”) {
return 1;
}

export.modules = arabicNumber; 
```

Enter fullscreen mode Exit fullscreen mode

BTW:导出类 MyClass 不适用于 nodejs。我必须使用 module.exports 关键字:(或者使用 babel npm 包将 ES6 蒸发到 commons 目标中。

你可以先编写所有的函数声明，然后在一个对象中导出:

```
function bar() {
   //bar
}

function foo() {
   //foo
}

module.exports = {
    foo: foo,
    bar: bar
}; 
```

Enter fullscreen mode Exit fullscreen mode

虽然没有神奇的一行程序，但是您需要显式地导出您想要公开的函数。

```
var exported = {
   someFunction: function() { },
   anotherFunction: function() { },
}

module.exports = exported; 
```

Enter fullscreen mode Exit fullscreen mode

## 用摩卡写测试:

不知何故，我不得不把我的测试文件命名为 test.js。

还需要声明 assert。
const assert = require(' assert ')；

否则，结构是非常相似的。编写一个函数，然后在测试过程中使用这个函数。

```
describe('romanise', () => {
    it('should be less than 4000', function () {
        const result = romanise(4000);
        assert.equal(result, 'invalid input');
    });

// write more it and asserts here.

}); 
```

Enter fullscreen mode Exit fullscreen mode