# 我今天写了一个 JavaScript 单元测试...模拟导入时间功能

> 原文：<https://dev.to/dev3l/i-wrote-a-javascript-unit-test-today-mock-import-time-function-1jdo>

我在一个 create-react-app 应用程序中使用 Redux，包括 jest。

# 任务

向现有的 reducers 函数添加一个名为`wadget`的新 REST 资源。*使用 TDD。*T3】

```
// reducers.js
import {combineReducers} from 'redux'

import widget from './widget'

export default combineReducers({
    widget
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

今天，我想模拟这个在导入时执行的函数。我还在学习 JavaScript 生态系统。我可以用 Python 来做这个...

让我们暂停一下...因为这听起来很荒谬。语言允许导入程序执行非封装代码。当指令指针链接到文件时，这些文件中的副作用会改变运行系统的状态。

**让我们在导入 DAO 基础文件时连接到一个数据库！**

 *# 测试

今天我为此纠结了一会儿。我在[栈溢出](https://stackoverflow.com/questions/50192478/how-do-i-spy-an-exported-function-invocation-in-javascript-using-sinon?noredirect=1#comment87405519_50192478)上问了一个问题。

```
// reducers.test.js
import redux from 'redux'
import widget from './widget'

describe('Use jest', () => {
    afterEach(() => {
        jest.resetModules()
    });

    test('first test', () => {
        jest.doMock('redux');

        require('./reducers');
        let {combineReducers} = require('redux');

        expect(combineReducers).toBeCalledWith({"widget": widget})
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 编码步骤

此时，因为我已经隔离了 combinedReducers 的调用，所以我可以更新我的测试以期待另一个资源类型，wadget。

##### (红色)测试失败

```
import widget from './widget'
import wadget from './wadget'
// ...
expect(combineReducers).toBeCalledWith({
  "widget": widget,
  "wadget": wadget
}) 
```

Enter fullscreen mode Exit fullscreen mode

##### (绿色)成功

```
// reducers.js
import {combineReducers} from 'redux'

import widget from './widget'
import widget from './wadget'

export default combineReducers({
    widget,
    wadget
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

[![agile2018endorsement](img/977d6e0fe4a78faa72534114386769ff.png)T2】](http://sched.co/EU9g)*