# Redux Reducer 中的递归循环用于检查本地存储的完整性

> 原文：<https://dev.to/nodefiend/recursive-loop-in-redux-reducer-to-check-integrity-of-local-storage-30h>

## 问题:

> 当我们在本地存储对象中添加嵌套设置时，用户会收到指向 MapStateToProps 的错误，他们本地存储中的键丢失了我们的新更新。

## 错误:

[![WHy?!?!?](img/f41063da5dfdb0cd9401dde70e1fc7b8.png "BugginOut")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3CVxv-RI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zirznpyl3dvc9dqxp22i.png)

### 我们的本地存储对象:

我们使用本地存储来保存像标签状态这样的东西，在这个例子中，我们保存了您在最后一个文档中创建的最后一个封面/页眉。

*reducers/local-storage-settings . js*

```
 const DEFAULT_SETTINGS = {
  docs: {
    settings: {
      coverPageId: null,
      headerId: null
    }
  },
  projectInfoOpen: true,
  lastSelectedInfoTab: null,
  lastSelectedAboutTab: null,
  lastSelectedContactTab: null,
  lastSelectedRegisterTab: null,

}; 
```

当用户登录到我们的应用程序时，我们检查他们的本地存储，并将我们的 redux 存储设置为他们上次访问时我们保存到他们本地存储中的对象。

问题是当我们向这个对象添加新的字段时，我们希望有嵌套的字段，这样看起来就很好了。

但是如果我们有嵌套字段，我们希望能够检查以确保那些键存在

### 什么时候？！

因为这是处理状态的，我们不希望这个函数一直运行，我能想到的最好的地方是把它放在项目中我们的基本容器的`componentWillMount`里面。(我们对每个项目都有一个本地存储对象)

```
 componentWillMount() { 
    // // NOTE: ensures the sanity of our local storage with recursive method
    this.props.ensureAllFieldsPresent({
      projectUuid: this.props.params.projectUuid
    });
  } 
```

因此，当调用这个动作时，我们将运行这个函数，只传递`projectUuid`来标识我们想要检查本地存储中的哪个项目。

### 减速器中发生了什么？

*reducers/local-storage-settings . js*

```
 function ensureAllFieldsPresent(state, { projectUuid }) {
  let newState = JSON.parse(JSON.stringify(state));
  let fixedProjectState = recursiveCheck(newState[projectUuid]);
  newState[projectUuid] = fixedProjectState;
  localStorage.setItem('projectSettings', JSON.stringify(newState));
  return newState;
} 
```

因此，对于每个项目，我们将运行递归检查，将本地存储设置设置为新的“固定”状态，然后最后将 newState 返回到我们的 redux 存储。

```
function recursiveCheck(state, defaultSettings = DEFAULT_SETTINGS) {
  let newState = JSON.parse(JSON.stringify(state));

  for (let k in defaultSettings) {
    if (!newState[k]) {
      // NOTE: 'in the event that a value is default set to TRUE'
      if (newState[k] !== false) {
        newState[k] = defaultSettings[k];
      }
    } else if (typeof defaultSettings[k] == 'object') {
      recursiveCheck(newState[k], defaultSettings[k]);
    }
  }
  return newState;
} 
```

### 到底怎么回事？？！

```
 function recursiveCheck(state, defaultSettings = DEFAULT_SETTINGS) {...} 
```

因为我们将再次调用这个函数(从函数内部)，所以我们设置了一个 defaultSettings 默认值，这样当这个方法最初被调用时，我们使用的第一个状态对象是`DEFAULT_SETTINGS`对象，它具有我们将用来纠正用户损坏的本地存储对象的更新值。

```
 let newState = JSON.parse(JSON.stringify(state)); 
```

因为 redux 的状态对象是`readonly`你不能改变对象并返回它，你必须复制，修改，然后返回一个新的状态。

我们使用`JSON.parse(JSON.stringify(state)`来制作对象的深层副本并获取所有这些字段。(如果你知道更快更好的方法，在下面评论！)

```
 for (let k in defaultSettings) {
    if (!newState[k]) {
      // NOTE: 'in the event that a value is default set to TRUE'
      if (newState[k] !== false) {
        newState[k] = defaultSettings[k];
      }
    }

} 
```

`for`循环:这将遍历我们的`DEFAULT_SETTINGS`对象并检查(将我们的默认对象与用户状态进行比较)是否对任何字段进行了更新，但是请注意，它将只检查对象的顶层，我们希望更深入地检查嵌套在其中的其余字段。

```
if (!newState[k]){...} 
```

因此，如果用户的本地存储设置在其本地状态中没有值，我们希望为他们创建一个值(这包括`null`和`undefined`)

**这部分花了我一天时间才弄明白**

```
 if (newState[k] !== false) {
        newState[k] = defaultSettings[k];
} 
```

因此，如果用户状态在他们的存储中有一个布尔值`false`,并且他们在安装了主要组件的视图中，并且这个方法正在运行，这是绝对可能的，那么将会发生的是，这个方法将运行并且将他们的存储重置为`DEFAULT_SETTINGS`,即使他们刚刚单击将 whatever 选项设置为 false。

在这种情况下，当用户点击关闭某个东西时，它不记得他们关闭了它，当我们离开并返回时，它又打开了。

所以除了检查`!newState[k]`之外，我还写了另一个条件 inside(我知道我可以在一行中完成)来检查以确保`newState[k]`最初不为假。如果它最初不为假，我们将替换这个对象。

### 递归性

那么当我们在这个循环中遇到一个对象时会发生什么呢？

我们想更深入地研究这个对象，并运行相同的键检查。

```
 else if (typeof defaultSettings[k] == 'object') {
      recursiveCheck(newState[k], defaultSettings[k]);
    } 
```

所以现在当循环到达一个对象的值时，这意味着我们可以更深入，检查这个对象内部的键。

还记得一开始，我们传递的初始状态(作为第二个参数)是`DEFAULT_SETTINGS`

```
function recursiveCheck(state, defaultSettings = DEFAULT_SETTINGS) {...} 
```

所以这一次，我们将把状态的`default[k]`部分作为第二个参数传入该方法，并把`newState[k]`作为第一个参数，这将确保用户状态对象的正确部分和`DEFAULT_SETTINGS`的正确部分相互核对。

### 好听

现在，在这结束时，我们已经充分检查了这个用户的本地存储设置的每个角落，现在希望不会遇到任何错误，当我们添加新的设置到应用程序。

# 非常感谢阅读！