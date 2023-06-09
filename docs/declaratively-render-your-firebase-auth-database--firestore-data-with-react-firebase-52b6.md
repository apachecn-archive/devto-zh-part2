# 使用@react-firebase 以声明方式呈现您的 Firebase 身份验证、数据库和 Firestore 数据。

> 原文：<https://dev.to/rakannimer/declaratively-render-your-firebase-auth-database--firestore-data-with-react-firebase-52b6>

## TLDR；

发布@react-firebase 是为了让在 react 应用中集成 firebase 快速&变得容易🎉。

如果你使用 React & Firebase，去[检查一下](https://react-firebase-js.com)！

## 加长版

I ❤️ Firebase & React 但是让他们和其他贡献者一起开发相对较大的代码库往往会导致:

1.  🍝代码库中不必要的复杂性
2.  🐛订阅管理和资源间依赖的细微错误。
3.  🎃当你的用户界面不需要的时候，僵尸监听器会一直在你身边。

[`@react-firebase`](https://react-firebase-js.com) 提供小型、类型化、经过良好测试的可组合组件，允许在 React 或 React 本机应用程序中轻松集成 Firebase Auth、实时数据库和/或 Firestore。

## 概述

`@react-firebase/auth`、`@react-firebase/database`、`@react-firebase/firestore`出口`Provider`和`Consumer`组件。

### 提供者

`Provider`组件接受`firebase`和 firebase 配置选项道具，应该在你的应用程序中渲染一次，作为你的`Consumer`组件的祖先。

#### 如何获得我的 Firebase 配置？

##### Web 或 React Native 与 Expo

如果你用的是 [firebase](https://www.npmjs.com/package/firebase)

将`PROJECT_NAME`更改为您的项目名称，并在此获取您的 firebase 配置:

`https://console.firebase.google.com/project/PROJECT_NAME/settings/general/`

它应该是这样的:

```
// Firebase Config
const config = {
  apiKey: "API_KEY",
  projectId: "PROJECT_ID",
  databaseURL: "DATABASE_URL",
  authDomain: "AUTH_DOMAIN",
  // OPTIONAL
  storageBucket: "STORAGE_BUCKET",
  messagingSenderId: "MESSAGING_SENDER_ID"
}; 
```

##### 反应原生火基

如果你使用的是 [react-native-firebase](https://rnfirebase.com) ，那么你只需要传入你的 firebase 版本作为道具(firebase 应用已经在原生端初始化了。)

### 提供商示例

```
import * as firebase from "firebase/app";

import "firebase/auth";
import { FirebaseAuthProvider } from "@react-firebase/auth";

// And or
import { FirebaseDatabaseProvider } from "@react-firebase/database";
import "firebase/database";

// And or
import "firebase/firestore";
import { FirestoreProvider } from "@react-firebase/firestore"; 
```

```
<FirebaseAuthProvider firebase={firebase} {...config}>
  <MyApp1 />
  <MyApp2 />
</FirebaseAuthProvider> 
```

### 消费者

每个模块都导出将消费和反应的消费组件(😅)来激发基本变化。

所有消费者组件都期望它们的`children`属性是一个反应节点或者一个返回反应节点的同步函数。(下面是特定于库的示例👇)

#### Firebase 授权消费者

##### `FirebaseAuthConsumer`

在应用程序组件树中的任何位置，添加一个 FirebaseAuthConsumer，以便在用户的身份验证状态改变时监听和更新 ui。

```
<FirebaseAuthConsumer>
  {({ isSignedIn, user, providerId }) => {
    return isSigned ? (
      <pre style={{ height: 300, overflow: "auto" }}>
        {JSON.stringify({ isSignedIn, user, providerId }, null, 2)}
      </pre>
    ) : <div>Not signed in.</div>;
  }}
</FirebaseAuthConsumer> 
```

##### [认证 API 引用](https://react-firebase-js.com/docs/react-firebase-auth/api)

#### Firebase 数据库消费者

##### 读取数据

###### `FirebaseDatabaseNode`

在应用程序组件树中的任何地方，添加一个`FirebaseDatabaseNode`来监听和更新 ui，当数据在提供的路径上改变时。

```
<FirebaseDatabaseNode path={"user_bookmarks/BOOKMARK_ID"}>
  {({ value, isLoading }) => {
    return (
      <pre>
        {JSON.stringify({ value, isLoading })}
      </pre>
    );
  }}
</FirebaseDatabaseNode> 
```

您还可以使用所有支持的 Firebase 实时数据库操作符来查询您的 Firebase 数据:

```
<FirebaseDatabaseNode path={"user_bookmarks/BOOKMARK_ID"} limitToFirst={10} orderByKey>
  {({ value, isLoading }) => {
    return (
      <pre>
        {JSON.stringify({ value, isLoading })}
      </pre>
    );
  }}
</FirebaseDatabaseNode> 
```

所有支持的道具。

##### 写数据

###### [T3`FirebaseDatabaseMutation`T4】T1】](#-raw-firebasedatabasemutation-endraw-amp-raw-firebasedatabasetransaction-endraw-)

变异 API 的灵感来自于 [apollo-graphql 变异](https://www.apollographql.com/docs/react/essentials/mutations.html)。

变异组件分别向它们的子组件注入一个`runMutation`或`runTransaction`道具。

`FirebaseDatabaseMutation`，除了`path`道具还需要一个变异`type`道具，可以是
之一

```
"set" | "update" | "push"; 
```

###### FirebaseDatabaseMutation 例子。

```
<FirebaseDatabaseMutation path={collectionPath} type="push">
  {({ runMutation }) => (
    <button
      onClick={async () => {
        const { key } = await runMutation(testDocValue);
        if (key === null || typeof key === "undefined") return;
        this.setState(state => ({
          keys: [...state.keys, key]
        }));
      }}
    >
      add-node-with-generated-key
    </button>
  )}
</FirebaseDatabaseMutation> 
```

###### FirebaseDatabaseTransaction 示例。

```
<FirebaseDatabaseTransaction path={path}>
  {({ runTransaction }) => {
    return (
      <div>
        <button
          onClick={() => {
            runTransaction({
              reducer: val => {
                if (val === null) {
                  return 1;
                } else {
                  return val + 1;
                }
              }
            }).then(() => {
              console.log("Ran transaction");
            });
          }}
        >
          Click me to run a transaction
        </button>
      </div>
    );
  }}
</FirebaseDatabaseTransaction> 
```

*   [FirebaseDatabaseMutation API](https://react-firebase-js.com/docs/react-firebase-realtime-database/api#firebasedatabasemutation-props)
*   [FirebaseDatabaseTranasaction API](https://react-firebase-js.com/docs/react-firebase-realtime-database/api#firebasedatabasetransaction-props)

#### Firestore 数据库消费者

##### 读取数据同`FirestoreDocument` & `FirestoreCollection`

在应用程序组件树的任何地方，添加一个`FirestoreDocument`或`FirestoreCollection`来监听数据，并在数据改变时更新 ui。

###### `FirestoreDocument`举例。

```
const s = v => JSON.stringify(v, null, 2);
const App = () => (
  <FirestoreDocument path={`/user_bookmarks/id`}>
    {({ value, path, isLoading }) => {
      return (
        <div>
          {value && <pre>{s(value)}</pre>}
          {path && <div>{path}</div>}
          <div>{isLoading}</div>
        </div>
      );
    }}
  </FirestoreDocument>
); 
```

###### [`FirestoreDocument` API](https://react-firebase-js.com/docs/react-firestore-database/api#firestoredocument)

`FirestoreCollection`例子

```
<FirestoreCollection path="/user_bookmarks/" limit={5}>
  {d => {
    return <pre>{s(d)}</pre>;
  }}
</FirestoreCollection> 
```

###### [`FirestoreCollection` API](https://react-firebase-js.com/docs/react-firestore-database/api#firestorecollection)

##### 用`FirestoreMutation` & `FirestoreTransaction` & `FirestoreBatchedWrite`写数据

变异 API 的灵感来自于 [apollo-graphql 变异](https://www.apollographql.com/docs/react/essentials/mutations.html)。

将`FirestoreMutation` & `FirestoreTransaction`的变异成分分别注入一个`runMutation`或者`runTransaction`的道具，给它们的孩子。

`FirestoreMutation`除了`path`道具还需要一个突变`type`道具，可以是
之一

```
"set" | "update" | "add"; 
```

`BatchedWrite`公开了不同的 API，因为它的行为不同于写和事务，因为它可以在`committing`更新之前运行许多更新。

它将两个道具`addMutationToBatch`和`commit`注入到它的子元素中。

*   `addMutationToBatch`

```
type addMutationToBatch = (
  {
    path,
    value,
    type
  }: {
    path: string;
    value: any;
    type: "add" | "update" | "set" | "delete";
  }
) => void; 
```

*   `commit`

```
type commit = () => Promise<void>; 
```

##### `FirestoreMutation`举例

```
<FirestoreProvider firebase={firebase} {...config}>
  <FirestoreMutation type="set" path={path}>
    {({ runMutation }) => {
      return (
        <div>
          <h2> Mutate state </h2>
          <button
            onClick={async () => {
              const mutationResult = await runMutation({
                nowOnCli: Date.now(),
                nowOnServer: firebase.firestore.FieldValue.serverTimestamp()
              });
              console.log("Ran mutation ", mutationResult);
            }}
          >
            Mutate Set
          </button>
        </div>
      );
    }}
  </FirestoreMutation>
</FirestoreProvider> 
```

##### `FirestoreTranasaction`举例

```
<FirestoreProvider firebase={firebase} {...config}>
  <FirestoreTransaction>
    {({ runTransaction }) => {
      return (
        <div>
          <button
            data-testid="test-set"
            onClick={async () => {
              await runTransaction(async ({ get, update }) => {
                const res = await get({
                  path
                });
                const data = res.data();
                if (typeof data === "undefined") return;
                await update(path, {
                  ...data,
                  a: isNaN(parseInt(data.a, 10)) ? 1 : data.a + 1
                });
              });
            }}
          >
            runTransaction
          </button>
        </div>
      );
    }}
  </FirestoreTransaction>
</FirestoreProvider> 
```

##### `FirestoreBatchedWrite`举例

```
<FirestoreBatchedWrite>
  {({ addMutationToBatch, commit }) => {
    return (
      <div>
        <h2>Batched write</h2>
        <button
          onClick={() => {
            console.log("adding to batch");
            addMutationToBatch({
              path,
              value: { [`a-value-${Date.now()}`]: Date.now() },
              type: "set"
            });
          }}
        >
          Add to batch
        </button>
        <button
          onClick={() => {
            console.log("committing to batch");
            commit().then(() => {
              console.log("Committed");
            });
          }}
        >
          Commit batch
        </button>
      </div>
    );
  }}
</FirestoreBatchedWrite> 
```

## 资源

*   [源代码](https://github.com/rakannimer/react-firebase)

*   [可编辑沙箱](https://react-firebase-js.com/docs/try-it-out)

*   [详细教程(WIP)](https://react-firebase-js.com/docs/guides/build-a-react-app-with-firebase-auth-and-realtime-database/setup-the-development-environment)

*   [书签应用示例](https://github.com/rakannimer/react-firebase/tree/master/modules/tutorial-bookmarking-app)

*   [用 Expo 和 Firebase 反应本地示例](https://github.com/rakannimer/react-firebase/tree/master/react-native-examples/react-native-expo-firebase-example)

*   [React Native Firebase 启动器示例](https://github.com/rakannimer/react-firebase/tree/master/react-native-examples/react-native-firebase-starter)

## 相关工作

*   [重置基础](https://github.com/tylermcginnis/re-base)
*   [redux-firebase](https://github.com/prescottprue/redux-firebase)
*   [react-firestore](https://github.com/green-arrow/react-firestore)
*   你的图书馆？在这里给我发个 DM 或者评论补充一下。

感谢你坚持到❤️邮报的最后！

这里有几个专门为您准备的附加资源:

*   [生成连接的 JSON 数据](https://react-firebase-js.com/docs/generate-json-data)
*   [在 Firebase 实时数据库中生成数据](https://react-firebase-js.com/docs/generate-firebase-data)
*   [在 Firebase Firestore 数据库中生成数据](https://react-firebase-js.com/docs/generate-firestore-data)