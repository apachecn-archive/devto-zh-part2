# 我今天写了一个 JavaScript 单元测试...窥探导入时间依赖

> 原文：<https://dev.to/dev3l/i-wrote-a-javascript-unit-test-today-spy-on-an-import-time-dependency-2f8b>

我正在 react 应用程序中初始化 firebase 身份验证提供程序。

# 给定

```
// base.js
L01  import firebase from 'firebase';
L02  const config = {
L03      apiKey: process.env.REACT_APP_FIREBASE_KEY,
L04      authDomain: process.env.REACT_APP_FIREBASE_DOMAIN,
L05      databaseURL: process.env.REACT_APP_FIREBASE_DATABASE,
L06      projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
L07      storageBucket: process.env.REACT_APP_FIREBASE_STORAGE_BUCKET,
L08      messagingSenderId: process.env.REACT_APP_FIREBASE_SENDER_ID
L09  };
L10  if (!firebase.apps.length) {
L11      firebase.initializeApp(config);
L12  }
L13  const auth = firebase.auth();
L14  export {auth} 
```

Enter fullscreen mode Exit fullscreen mode

# 任务

添加单元测试以完全覆盖并断言`base.js`每行的预期行为。

* * *

作为今天的一个`kata`，我想要命中导入的 JavaScript 文件中的每一行代码。模拟系统范围的导入，比如初始化数据库或 api，是掌握系统单元的基础。

正如我在之前的 [JavaScript 单元测试文章](https://dev.to/dev3l/i-wrote-a-javascript-unit-test-today-mock-import-time-function-1jdo)中提到的，*语言允许导入程序执行非封装代码。当指令指针链接到文件时，这些文件中的副作用会改变运行系统的状态，例如连接到数据库或 api。*尽可能多的单元应该能够无依赖地存在于系统中。

# 测试

```
// base.test.js

// harness
describe("Base", () => {
    afterEach(() => {
        jest.resetModules()
    });
    // tests go here
}); 
```

Enter fullscreen mode Exit fullscreen mode

## **测试#1** :当 Firebase 有 App 时，初始化 App *未被*调用

*   断言当 firebase 已经有任何现有应用程序时，它不会调用 L11 `firebase.initializeApp(config);`。
*   模拟 firebase.apps 的值以返回 L10，`firebase.apps = [1]`上的真值。
*   使用一个间谍来断言 L13 只被调用过一次。
*   使用 spy，断言其函数的返回值是从`base.js`默认导出的值。

```
test("firebase initializeApp not called if already initialized", () => {
    const firebase = require('firebase');
    jest.mock('firebase');

    firebase.initializeApp = (config) => {
        throw "Should not be hit in test"
    };
    firebase.apps = [1];

    let mockAuth = jest.fn();
    let authReturnValue = 'auth'
    mockAuth.mockReturnValueOnce(authReturnValue)
    firebase.auth = mockAuth;

    let auth = require("./base");

    expect(mockAuth.mock.calls.length).toBe(1);
    expect(auth).toEqual({"auth": authReturnValue})
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这个测试中，我们执行 L13 之外的每一行代码。

## **测试#2** :初始化 App *是用 Firebase 配置变量*调用的

*   断言使用预期的环境变量调用 initializeApp。

```
test("firebase initializeApp called with firebase environment variables", () => {
    const firebase = require('firebase');
    jest.mock('firebase');

    // hold on to existing env
    const env = process.env;
    // set mock env variables
    process.env = {
        REACT_APP_FIREBASE_KEY: 'key',
        REACT_APP_FIREBASE_DOMAIN: 'domain',
        REACT_APP_FIREBASE_DATABASE: 'database',
        REACT_APP_FIREBASE_PROJECT_ID: 'project',
        REACT_APP_FIREBASE_STORAGE_BUCKET: 'bucket',
        REACT_APP_FIREBASE_SENDER_ID: 'sender',
        REACT_APP_EXTRA_KEY: 'extra'
    };

    const expectedConfig = {
        apiKey: 'key',
        authDomain: 'domain',
        databaseURL: 'database',
        projectId: 'project',
        storageBucket: 'bucket',
        messagingSenderId: 'sender'
    };

    // spy for initializeApp
    let mockInitializeApp = jest.fn();
    firebase.initializeApp = mockInitializeApp;
    firebase.apps = [];

    require("./base");
    expect(mockInitializeApp.mock.calls[0][0]).toEqual(expectedConfig);

    // restore env
    process.env = env;
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

笑话继续给我带来惊喜。我发现它的[模拟功能文档](https://facebook.github.io/jest/docs/en/mock-functions.html)对用户非常友好！谈到单元测试，嘲讽总是一个棘手的话题。如果你不明白这是怎么回事，一定要提问！

[完整来源](https://github.com/DEV3L/brain-bit-ledger/tree/master/src/modules/firebase)

[![unit test coverage](../Images/e9dc68603e4c0ef2345f0470436f19e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BnASN_SB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6rj61fu37g954cnur08t.png)

* * *

[![agile2018endorsement](../Images/977d6e0fe4a78faa72534114386769ff.png)T2】](http://sched.co/EU9g)