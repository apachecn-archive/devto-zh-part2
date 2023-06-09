# 在 Jest 测试中模仿 JavaScript 的当前日期

> 原文：<https://dev.to/hugo__df/mocking-javascripts-current-date-in-jest-tests-412j>

有些情况下会在应用程序代码中使用`Date.now`。这些代码需要被测试，记住如何模仿`Date.now`总是很困难。下面是这个神奇的片段:

```
const literallyJustDateNow = () => Date.now();

test('It should call and return Date.now()', () => {
  const realDateNow = Date.now.bind(global.Date);
  const dateNowStub = jest.fn(() => 1530518207007);
  global.Date.now = dateNowStub;

  expect(literallyJustDateNow()).toBe(1530518207007);
  expect(dateNowStub).toHaveBeenCalled();

  global.Date.now = realDateNow;
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

这实际上不是一个 Jest 特有的技巧，我们只是访问节点`global`对象并用一个存根替换`Date.now`。
我们也是良好的单元测试公民，并把原来的`global.Date.now`实现放回去😇。

布莱斯·巴克在 Unsplash 上拍摄的封面照片。