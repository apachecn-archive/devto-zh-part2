# GAE+NuxtのSSRでFirestoreを使う

> 原文：<https://dev.to/dala00/gaenuxtssrfirestore-2ho3>

[前几天在 App Engine 的标准环境下使用 Nuxt 尝试了免费 SSR](https://crieit.net/posts/App-Engine-Nuxt-SSR) ，这次尝试了在 Firebase 的 Cloud Firestore 中保存数据。

顺便说一下，在 GAE 的标准环境下，Node8 会工作，所以在本地也需要全部用 Node8 工作。

## Firebase 的初始设定

按如下方式将设置保存在 plugins/firebase.js 中。

```
import * as firebase from "firebase";
require("firebase/firestore");

if (!firebase.apps.length) {
  const config = {
    apiKey: "your firebase api key",
    authDomain: "your firebase auth domain",
    databaseURL: "your firebase database url",
    projectId: "your google cloud platform project id",
    storageBucket: "your google cloud platform storage bucket",
    messagingSenderId: "your firebase messaging sender id"
  };
  firebase.initializeApp(config);
}

firebase.firestore(); // もしかしたら要らないかも 
```

Enter fullscreen mode Exit fullscreen mode

然后，在 nuxt.config.js 上读取上述内容。

```
 plugins: ["~/plugins/firebase.js"], 
```

Enter fullscreen mode Exit fullscreen mode

现在，Firebase 的库在 Nuxt 上可用。

## 向 Firestore 追加&获取数据

将原本就有的 index.vue 修改如下，添加数据一览，按下按钮，尝试制作了可以登录数据的最小样本。 (没有关系的地方我都刮了)

### 模板

模板只是显示数据，没有什么需要特别注明的地方。

```
<template>
  <section class="container">
    <div>
      <div v-for="text in texts" :key="text.id">
        {{ text.body }}
      </div>
      <input type="text" v-model="textInput">
      <button type="button" @click="add">add</button>
    </div>
  </section>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

### 脚本

#### Initial

```
import db from "../lib/db"; 
```

Enter fullscreen mode Exit fullscreen mode

为了方便使用数据库，我们导入了数据库，使其可以立即使用。 这个并不是一定要做。 内容只是初始化数据库后取来的。

```
import * as firebase from "firebase";
export default firebase.firestore(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 从 Firestore 中获取数据

读取页面时，首先从 Firestore 中获取数据并进行列表。

```
async function getTexts() {
  return new Promise(callback => {
    let texts = [];
    db
      .collection("texts")
      .get()
      .then(query => {
        query.forEach(doc => {
          texts.push(doc.data());
        });
        callback(texts);
      });
  });
}

export default {
  data() {
    return {
      texts: [],
      textInput: ""
    };
  },
  async asyncData({ params }) {
    return { texts: await getTexts() };
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我想在数据的初始化中经常使用 mounted，但是这在服务器端渲染中不起作用，所以需要使用`asyncData`和`fetch`来代替。 `asyncData`在下面的 URL 上记载了使用方法，但似乎可以使用 async/await，或者自由选择 Promise 和 callback 形式等。

[异步数据- Nuxt.js](https://ja.nuxtjs.org/guide/async-data/)

因为后面的里面不能使用`this`，所以没办法只能随便在类之外定义 function 并调用它。

啊，还有 asyncData 在组件上不能使用，所以这次在页面上运行。 在实际开发中，如果想在组件中使用值，也可以通过属性传递，或者使用 store。

#### 将数据添加到 Firestore

之后追加点击添加按钮时的处理。

```
 methods: {
    add() {
      db
        .collection("texts")
        .add({
          body: this.textInput
        })
        .then(docRef => {
          this.textInput = "";
          return getTexts();
        })
        .then(texts => (this.texts = texts));
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

追加后暂时只是在执行刚才的一览获取处理。

这样数据就保存在 Firestore 中，也显示在画面上。 也可以在 Firebase Console 的数据一览中确认。

## 确认是否进行了服务器侧渲染

如果在 Google App Engine 上运行的话，就会进行服务器端渲染，所以我想只要重新加载页面进行确认，就可以确认在源代码上注册的字符串存在。 (如果错误地用`mounted`处理，将不会被 SSR )

FIrebase 的话，部署也不用在意服务器端的处理什么的，所以很轻松！

还有，Firestore 还有[实时更新](https://firebase.google.com/docs/firestore/query-data/listen?hl=ja)的功能，如果获取一览等经常更新的话，在那里试试也说不定。