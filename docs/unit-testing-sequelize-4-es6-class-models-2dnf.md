# 单元测试 Sequelize 4 ES6 类模型

> 原文：<https://dev.to/hugo__df/unit-testing-sequelize-4-es6-class-models-2dnf>

在[“为 Sequelize 4 模型使用 ES6 类”](https://dev.to/hugo__df/using-es6-classes-for-sequelize-4-models-17ga)中，我们探讨了如何使用 ES6 类定义 Sequelize 模型。这种方法的一个好处是它允许简单的单元测试，它允许你实例化模型而没有所有的 Sequelize(和/或数据库)的包袱。

这些例子将使用 Jest 模块自动模仿，但应该可以移植到其他模块模仿库(如 Proxyquire)的一些修改。

我们将测试以下模型:

```
const { Model } = require('sequelize');

class MyModel extends Model {
  static init() {
    return super.init(
      // Config, see "Using ES6 classes for Sequelize 4 models"
    );
  }

  isAvailable (date) {
    if (!Array.isArray(this.meetings)) {
      throw new Error('meetings should be eager-loaded');
    }
    return !this.meetings.find(
      ({ startDate, endDate }) => (startDate < date && endDate > date)
    );
  }
}

module.exports = MyModel; 
```

Enter fullscreen mode Exit fullscreen mode

在模块级别，我们希望:

*   模仿序列
*   导入模型

在测试中:

*   实例化我们定义的模型(没有崩溃)
*   在该实例上设置一些属性
*   运行一些方法
*   在输出上断言

```
jest.mock('sequelize');
const Model = require('./model');

test('It should not throw when passed a model containing an empty list of meetings', () => {
  const model = new Model();
  model.meetings = [];
  expect(model.isAvailable.bind(null, new Date(Date.now())).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 与`Object.assign`替代

如果我们设置了多个实例属性，使用`Object.assign`会更容易管理:

```
jest.mock('sequelize');

const Model = require('./model');

test('It should not throw when passed a model containing an empty list of meetings', () => {
  const model = Object.assign(
    new Model(),
    {
      meetings: []
    }
  );
  expect(model.isAvailable.bind(null, new Date(Date.now())).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用带有 Sequelize 的 ES6 类使得测试更加容易和自然，[注册时事通讯](https://buttondown.email/hugo)来了解即将到来的 Sequelize ES6 备忘单。

[升高](https://unsplash.com/@elevatebeer?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)