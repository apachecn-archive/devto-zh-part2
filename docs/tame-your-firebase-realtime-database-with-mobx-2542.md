# 用 MobX 驯服您的 Firebase 实时数据库

> 原文：<https://dev.to/rakannimer/tame-your-firebase-realtime-database-with-mobx-2542>

虽然 Firebase 的实时数据库让你几乎可以构建任何东西，但在应用程序中操作实时数据可能会导致编写难以调试、理解和维护的代码。

进入 [MobX](https://mobx.js.org) 和 [mobx-firebase-database](https://github.com/rakannimer/mobx-firebase-database) 。

MobX 是一个强大的状态管理库，可以与所有前端框架一起工作。

MobX-firebase-database 允许你用`firebase`、`firebase-admin`或`react-native-firebase`将你的 firebase 实时数据映射到 MobX 观察点(盒子、阵列或地图)

## 使用它

`mobx-firebase-database`导出一个函数 getMobxFire，用 firebase 和你的 firebase 配置调用它来访问你的工具箱。

```
import getMobxFire from "mobx-firebase-database";
import firebase from "firebase/app";
import "firebase/database";

// Don't worry about calling it more than once.
const { toBox, toArray, toMap, getFirebaseRef, destroy } = getMobxFire({
  config,
  firebase
}); 
```

*   将一个火焰基地转化为一个可观察的盒子

*   `toArray`:将一个火基 ref 变成一个可观察的有序的`{key,value}`物体阵列

*   `toMap`:将 firebase ref(带有您能想到的任何查询)转换为可观察映射:`Map<K,V>`其中 K 是子键名称，V 是子值

既然我们已经可以访问这组原语进行构建。我们可以很容易地用它们来构建 ui。

## 用`mobx-firebase-database`搭配香草 JS

```
import { observe } from "mobx";
import getMobxFirebase from "mobx-firebase-database";
import firebase from "firebase/app";
import "firebase/database";
import { config } from "../config";

const PATH = `TEST_SANDBOX/posts/1`;
let counter = 0;
const pretty = obj =>
  `<pre>${JSON.stringify(obj, null, 2)}</pre>`;

const { toBox } = getMobxFirebase({
  config,
  firebase
});
const refToPost = firebase.database().ref(PATH);
const { value } = toBox(refToPost);

setInterval(() => {
  counter += 1;
  // Write data fo firebase here
  refToPost.set({
    title: `post title ${counter}`
  });
}, 500);
observe(value, () => {
  // see it update here
  document.getElementById("root").innerHTML = pretty(
    value.get()
  );
}); 
```

## 使用`mobx-firebase-database`与 ReactJS

```
 const App = observer(() => {
  // Render it here
  return <pre>{JSON.stringify(value.get())}</pre>; });

render(<App />, document.getElementById("root")); 
```

就这么简单:)

想了解更多信息:

[实验用 VanillaJS 代码](https://codesandbox.io/s/jzon1ywl2y)
T3】实验用 React 代码
[阅读文档](https://github.com/rakannimer/mobx-firebase-database#api)

就是这样，伙计们！

有什么问题吗？
在 [@rakannimer](https://twitter.com/rakannimer) 上给我留言或者在这里留言！