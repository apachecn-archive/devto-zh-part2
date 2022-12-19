# 滥用 Jest 快照测试:一些不错的用例📸

> 原文：<https://dev.to/hugo__df/abusing-jest-snapshot-tests-some-nice-use-cases--ncn>

除了常见的 React/Vue UI 组件之外，还有一些很好的快照测试用例。

换句话说，尽管使用快照进行 React 和 Vue 测试已经有了很好的记录，但这并不是它们唯一有用的地方。

根据经验，您可以用快照测试替换大量断言特定数据的单元测试。

对于快照测试，我们有以下优势:

*   比赛数据存储在一个单独的文件中，所以很难忘记事情，例如，在回顾中被浏览

*   与内联数据匹配相比，更改要简单得多，只需运行`npx jest -u`即可更新所有快照。

我还想到了以下缺点:

*   与内联数据匹配相比，这是一种更轻松的更改，这意味着人们需要注意快照文件中的更改

*   尽管有社区的努力，唯一支持开箱即用的主要测试库是 Jest(它将您锁定在那个生态系统中)

这使得它特别适合几个领域:

*   [配置🎛](#config)
*   [数据库模型🏬](#database-models)
    *   [设置🏗](#setup)
    *   [查询🔍](#queries)
*   [帕格或车把模板](#pug-or-handlebars-templates)
*   [⚠️快照测试截图](#gotchas-of-snapshot-testing)
    *   有些东西(比如函数)不能很好地序列化🔢
    *   [如果你能完全匹配，那就去做](#if-you-can-do-a-full-match-do-it)
    *   [尝试用另一种类型的测试✌️覆盖相同的代码/特性](#try-to-cover-the-same-codefeature-with-another-type-of-test)

完整的代码可在[github.com/HugoDF/snapshot-everything](https://github.com/HugoDF/snapshot-everything)获得。

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo)[订阅](https://buttondown.email/hugo)，在你的收件箱里获得最新的帖子(比任何人都要早)。

## 配置🎛

`monitor-queues.test.js` :

```
jest.mock('bull-arena');
const { monitorQueues } = require('./monitor-queues');
describe('monitorQueues', () => {
  test('It should return an Arena instance with parsed data from REDIS_URL', () => {
    const redisPort = 5555;
    const REDIS_URL = `redis://h:passsssword@hosting:${redisPort}/database-name`;
    const QUEUE_MONITORING_PATH = '/arena';
    const ArenaConstructor = require('bull-arena');
    ArenaConstructor.mockReset();
    monitorQueues({ REDIS_URL, QUEUE_MONITORING_PATH });
    expect(ArenaConstructor).toHaveBeenCalledTimes(1);
    expect(ArenaConstructor.mock.calls[0]).toMatchSnapshot();
  });
  test('It should return an Arena instance with defaulted redis data when REDIS_URL is empty', () => {
    const REDIS_URL = '';
    const QUEUE_MONITORING_PATH = '/arena';
    const ArenaConstructor = require('bull-arena');
    ArenaConstructor.mockReset();
    monitorQueues({ REDIS_URL, QUEUE_MONITORING_PATH });
    expect(ArenaConstructor).toHaveBeenCalledTimes(1);
    expect(ArenaConstructor.mock.calls[0]).toMatchSnapshot();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

`monitor-queues.js` :

```
const Arena = require('bull-arena');
const { JOB_TYPES } = require('./queue/queues');
const url = require('url');
function getRedisConfig (redisUrl) {
  const redisConfig = url.parse(redisUrl);
  return {
    host: redisConfig.hostname || 'localhost',
    port: Number(redisConfig.port || 6379),
    database: (redisConfig.pathname || '/0').substr(1) || '0',
    password: redisConfig.auth ? redisConfig.auth.split(':')[1] : undefined
  };
}
const monitorQueues = ({ REDIS_URL, QUEUE_MONITORING_PATH }) =>
  Arena(
    {
      queues: [
        {
          name: JOB_TYPES.MY_TYPE,
          hostId: 'Worker',
          redis: getRedisConfig(REDIS_URL)
        }
      ]
    },
    {
      basePath: QUEUE_MONITORING_PATH,
      disableListen: true
    }
  );
module.exports = {
  monitorQueues
}; 
```

Enter fullscreen mode Exit fullscreen mode

给出以下快照:

```
exports[`monitorQueues It should return an Arena instance with defaulted redis data when REDIS_URL is empty 1`] = `
Array [
  Object {
    "queues": Array [
      Object {
        "hostId": "Worker",
        "name": "MY_TYPE",
        "redis": Object {
          "database": "0",
          "host": "localhost",
          "password": undefined,
          "port": 6379,
        },
      },
    ],
  },
  Object {
    "basePath": "/arena",
    "disableListen": true,
  },
]
`;

exports[`monitorQueues It should return an Arena instance with parsed data from REDIS_URL 1`] = `
Array [
  Object {
    "queues": Array [
      Object {
        "hostId": "Worker",
        "name": "MY_TYPE",
        "redis": Object {
          "database": "database-name",
          "host": "hosting",
          "password": "passsssword",
          "port": 5555,
        },
      },
    ],
  },
  Object {
    "basePath": "/arena",
    "disableListen": true,
  },
]
`; 
```

Enter fullscreen mode Exit fullscreen mode

## 数据库型号🏬

### 设置🏗

```
test('It should initialise correctly', () => {
  class MockModel { }
  MockModel.init = jest.fn();
  jest.setMock('sequelize', {
    Model: MockModel
  });
  jest.resetModuleRegistry();
  const MyModel = require('./my-model');
  const mockSequelize = {};
  const mockDataTypes = {
    UUID: 'UUID',
    ENUM: jest.fn((...arr) => `ENUM-${arr.join(',')}`),
    TEXT: 'TEXT',
    STRING: 'STRING'
  };
  MyModel.init(mockSequelize, mockDataTypes);
  expect(MockModel.init).toHaveBeenCalledTimes(1);
  expect(MockModel.init.mock.calls[0]).toMatchSnapshot();
}); 
```

Enter fullscreen mode Exit fullscreen mode

`my-model.js` :

```
const { Model } = require('sequelize');

class MyModel extends Model {
  static init (sequelize, DataTypes) {
    return super.init(
      {
        disputeId: DataTypes.UUID,
        type: DataTypes.ENUM(...['my', 'enum', 'options']),
        message: DataTypes.TEXT,
        updateCreatorId: DataTypes.STRING,
        reply: DataTypes.TEXT
      },
      {
        sequelize,
        hooks: {
          afterCreate: this.afterCreate
        }
      }
    );
  }

  static afterCreate() {
    // do nothing
  }
}

module.exports = MyModel; 
```

Enter fullscreen mode Exit fullscreen mode

给了我们下面的快照:

```
exports[`It should initialise correctly 1`] = `
Array [
  Object {
    "disputeId": "UUID",
    "message": "TEXT",
    "reply": "TEXT",
    "type": "ENUM-my,enum,options",
    "updateCreatorId": "STRING",
  },
  Object {
    "hooks": Object {
      "afterCreate": [Function],
    },
    "sequelize": Object {},
  },
]
`; 
```

Enter fullscreen mode Exit fullscreen mode

### 查询🔍

`my-model.test.js` :

```
jest.mock('sequelize');
const MyModel = require('./my-model');

test('It should call model.findOne with correct order clause', () => {
  const findOneStub = jest.fn();
  const realFindOne = MyModel.findOne;
  MyModel.findOne = findOneStub;
  const mockDb = {
    Association: 'Association',
    OtherAssociation: 'OtherAssociation',
    SecondNestedAssociation: 'SecondNestedAssociation'
  };
  MyModel.getSomethingWithNestedStuff('1234', mockDb);
  expect(findOneStub).toHaveBeenCalled();
  expect(findOneStub.mock.calls[0][0].order).toMatchSnapshot();
  MyModel.findOne = realFindOne;
}); 
```

Enter fullscreen mode Exit fullscreen mode

`my-model.js` :

```
const { Model } = require('sequelize');

class MyModel extends Model {
    static getSomethingWithNestedStuff(match, db) {
    return this.findOne({
      where: { someField: match },
      attributes: [
        'id',
        'createdAt',
        'reason'
      ],
      order: [[db.Association, db.OtherAssociation, 'createdAt', 'ASC']],
      include: [
        {
          model: db.Association,
          attributes: ['id'],
          include: [
            {
              model: db.OtherAssociation,
              attributes: [
                'id',
                'type',
                'createdAt'
              ],
              include: [
                {
                  model: db.SecondNestedAssociation,
                  attributes: ['fullUrl', 'previewUrl']
                }
              ]
            }
          ]
        }
      ]
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

给出以下快照:

```
exports[`It should call model.findOne with correct order clause 1`] = `
Array [
  Array [
    "Association",
    "OtherAssociation",
    "createdAt",
    "ASC",
  ],
]
`; 
```

Enter fullscreen mode Exit fullscreen mode

## 帕格或车把模板

这与 Vue/React 快照测试基本相同，但不管怎样，让我们从头到尾看一遍，我们在 Pug 和 Handlebars 中有两个等效的模板:

`template.pug` :

```
section
  h1= myTitle
  p= myText 
```

Enter fullscreen mode Exit fullscreen mode

`template.handlebars` :

```
<section>
  <h1>{{ myTitle }}</h1>
  <p>{{ myText }}</p>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

`template.test.js` :

```
const pug = require('pug');

const renderPug = data => pug.renderFile('./template.pug', data);

test('It should render pug correctly', () => {
  expect(renderPug({
    myTitle: 'Pug',
    myText: 'Pug is great'
  })).toMatchSnapshot();
});

const fs = require('fs');
const Handlebars = require('handlebars');
const renderHandlebars = Handlebars.compile(fs.readFileSync('./template.handlebars', 'utf-8'));

test('It should render handlebars correctly', () => {
  expect(renderHandlebars({
    myTitle: 'Handlebars',
    myText: 'Handlebars is great'
  })).toMatchSnapshot();
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里的大部分工作实际上是用 pug 和 handlebars 的原始编译器将模板编译成字符串。

快照最终非常简单:

```
exports[`It should render pug correctly 1`] = `"<section><h1>Pug</h1><p>Pug is great</p></section>"`;

exports[`It should render handlebars correctly 1`] = `
"<section>
  <h1>Handlebars</h1>
  <p>Handlebars is great</p>
</section>
"
`; 
```

Enter fullscreen mode Exit fullscreen mode

## ⚠️快照测试截图

### 有些东西(比如函数)不能很好地序列化🔢

参见`__snapshots__ /my-model.test.js.snap` :

```
"hooks":  Object  {  "afterCreate":  [Function],  }, 
```

Enter fullscreen mode Exit fullscreen mode

我们真的应该添加如下一行来测试这个函数*实际上是*正确的函数，(`my-model.test.js` ):

```
expect(MockModel.init.mock.calls[0][1].hooks.afterCreate).toBe(MockModel.afterCreate); 
```

Enter fullscreen mode Exit fullscreen mode

### 如果能做到完全匹配，就去做

很多时候，对象匹配的硬断言是一个很好的选择，不要因为可以就拍快照。

你应该为那些几乎不是代码核心目的的东西拍快照，比如渲染模板中的字符串，渲染模板中的 DOM 结构，配置。

与快照的权衡如下:

> 与内联的`toBe`或`toEqual`相比，快照提供的断言更弱，但是在测试中输入的代码和存储的信息方面也更少(因此降低了复杂性)。

### 尝试用另一种类型的测试✌️覆盖相同的代码/特性

无论是`/arena`*实际加载公牛竞技场队列监控的手动冒烟测试，还是对整个应用程序的集成测试，您都应该检查事情是否正常🙂。*

 *完整的代码可在[github.com/HugoDF/snapshot-everything](https://github.com/HugoDF/snapshot-everything)获得。

本·索尔*