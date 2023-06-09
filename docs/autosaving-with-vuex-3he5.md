# 使用 Vuex 自动保存

> 原文：<https://dev.to/isaacdlyman/autosaving-with-vuex-3he5>

# 一些背景

早在 21 世纪初，如果你一直在处理一份微软 Word 文档，没有按 Ctrl+S 就关闭了电脑，那你的处境就不妙了。你所有的工作都没了，没有办法再找回来。在 Word 的未来版本中，他们引入了一个自动恢复工具，如果你幸运的话，这个工具可以帮你恢复大部分内容。这很好，但真正改变游戏规则的是谷歌文档，它有一个始终保持警惕的*保存*指示器，可以注意到你对文档的修改，并每隔几秒钟自动保存到云端。

自动保存现在是表赌注。如果你的应用程序允许用户输入内容，他们就不再寻找一个明确的“保存”按钮。他们希望上传到云端的速度和他们打字的速度一样快。幸运的是，在现代 webapps 中，实现它并不太难。

今天我将向你展示如何使用 Vue.js、Vuex 和一个 Vuex 插件编写一个 autosaver。这种方法的好处在于，它不需要你的应用程序在每次有变化时都显式地发送 API 请求；相反，Vuex 插件观察应用程序的状态，并在需要时做出响应，将应用程序的内部工作与其与 API 的通信分离开来。我已经在我的爱好项目 [Edward](https://edwardtheapp.com) 中实现了这一点，它运行起来就像做梦一样。

# 配料

以下是我们将使用的工具:

*   Vue.js 是一个用于编写 web 组件的简单而强大的框架。它在反应的范围和范式上是相似的，但是充满了快捷方式，使得使用起来更有趣。
*   Vuex 是 Vue 的一个状态管理工具。和 Redux 差不多。它是一个不可变的状态机，这意味着它管理着一个巨大的对象，其中充满了你的应用程序需要的数据，每次数据发生变化，它都会产生一个全新的对象。
*   Lodash 是一个充满美好事物的 JavaScript 工具链(很像下划线或 jQuery 的一部分)。今天我们只需要它的一个功能。如果我们想的话，没有它我们也能过下去。
*   一个**网络浏览器**。

回购的例子是这里的。你猜怎么着？不需要`npm install`，没有构建步骤(你可以直接在你的浏览器中打开`index.html`，整个事情不到 100 行代码(不包括注释)。另外，这都是在平原 ES5！请尽量控制你的激动情绪。

# 怎么做

首先，你需要一个基本的`index.html`文件。它将包含一个供 Vue 附加的 div、`<script>`标签用于我们需要的库、一个`<script>`标签用于我们的 JavaScript 文件，还有一个`<style>`标签使事情看起来更好一些。

```
<body>
  <div id="app"></div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script src="https://unpkg.com/vuex@3.0.1/dist/vuex.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.5/lodash.min.js"></script>
<script src="./index.js"></script>
<style>
  textarea {
    height: 100px;
    width: 300px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到我们正在引入 Vue、Vuex、Lodash 和我们自己的名为`index.js`的 JavaScript 文件。这就是这个文件。

创建`index.js`文件。我们将从引导我们的 Vue 应用程序开始:

```
var app = new Vue({
  el: '#app',
  template: '<div></div>',
}) 
```

Enter fullscreen mode Exit fullscreen mode

您应该将这段代码保存在您的`index.js`文件的底部，因为它将引用我们从这里开始构建的所有内容。

Vue 将找到与`el`选择器匹配的元素并控制它。目前它所做的就是在里面放入另一个`<div></div>`。一会儿我们会让它做一些更有趣的事情。

现在让我们创建一个允许用户输入文本的组件:

```
Vue.component('text-entry', {
  template: '<textarea v-model="content" @keyup="registerChange"></textarea>',
  data: function () {
    return {
      content: '' // This is the initial value of the textarea
    }
  },
  methods: {
    registerChange: function () {
      // We'll do something whenever the textarea changes
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这将显示一个文本区域，并且每当用户在其中键入时更新`content`的值。让我们通过更新 Vue 构造函数将它添加到我们的应用程序中:

```
var app = new Vue({
  el: '#app',
  template: '<div> <text-entry></text-entry> </div>',
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们应该在应用程序中看到一个文本区。接下来，我们创建一个 Vuex 商店:

```
var store = new Vuex.Store({
  state: {
    content: ''
  },
  mutations: {
    'UPDATE_CONTENT': function (state, newContent) {
      state.content = newContent
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常基本的 Vuex 商店。它有一个数据和一个突变。当我们提交变异时，它会将我们传入的内容保存为新的值`content`。

我们在三个地方需要这个商店:

*   一旦在我们的应用程序声明，所以 Vue 知道使用它。
*   一次在我们的`text-entry`组件的`data`声明中，所以它将基于 Vuex 状态设置 textarea 的值。
*   一次在我们的`text-entry`组件的`registerChange`方法中，每次用户改变文本区域中的文本时提交一个突变。

一旦我们完成了所有这些，我们的`index.js`文件应该看起来像这样:

```
var store = new Vuex.Store({
  state: {
    content: ''
  },
  mutations: {
    'UPDATE_CONTENT': function (state, newContent) {
      state.content = newContent
    }
  }
})

Vue.component('text-entry', {
  template: '<textarea v-model="content" @keyup="registerChange"></textarea>',
  data: function () {
    return {
      content: this.$store.state.content
    }
  },
  methods: {
    registerChange: function () {
      this.$store.commit('UPDATE_CONTENT', this.content)
    }
  }
})

var app = new Vue({
  el: '#app',
  template: '<div> <text-entry></text-entry> </div>',
  store: store
}) 
```

Enter fullscreen mode Exit fullscreen mode

为了演示我们的自动保存特性，我们需要一个地方来存储页面刷新后将持续存在的数据。我不会为此费心创建一个 web 服务器。让我们改用 LocalStorage】

```
var storageKey = 'content'
var api = {
  load: function () {
    var json = window.localStorage.getItem(storageKey) || JSON.stringify('')
    return JSON.parse(json)
  },
  save: _.debounce(function (content, callback) {
    window.localStorage.setItem(storageKey, JSON.stringify(content))
    callback()
  }, 1000, { maxWait: 3000 })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的伪 API 有两个方法，`save`和`load`。`load`尝试从 LocalStorage 中获取应用程序状态，如果不存在，则返回一个空字符串。`save`设置 LocalStorage 键的值，然后调用回调。我们在这里使用 Lodash 的便捷的`debounce`方法来确保`save`每秒钟不会被调用超过一次。这很重要，因为如果我们不去抖方法，它将在每次用户键入一个键时被调用。对于 LocalStorage 来说，这还不算太糟糕，但是如果您对实际的 web 服务器进行 XHR 请求，一个每分钟 70 个单词的用户可能每秒会提交几个请求，这会降低他们和您的速度。我还使用了`maxWait`参数，它确保如果用户连续输入，内容会每三秒自动保存一次。

好了，现在我们可以创建一个 Vuex 插件来自动保存文本区域的内容。Vuex 插件是一个接受 Vuex 存储作为参数的函数。然后，它可以订阅存储，以便在每次状态更改时得到通知。

```
var autosaverPlugin = function (store) {
  store.commit('UPDATE_CONTENT', api.load())

  store.subscribe(function (mutation, state) {
    if (mutation.type === 'UPDATE_CONTENT') {
      api.save(mutation.payload, function () {
        // This callback doesn't need to do anything yet
      })
      return
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

当插件首次加载时，我们立即从 LocalStorage 加载应用程序状态，并将其提交为一个变异。然后我们订阅 Vuex 商店。每次发生变异，我们都会收到通知。我们可以检查突变类型，以确保它是一个内容更新，然后将有效负载发送到我们的伪 API，以将其保存在 LocalStorage 中。

现在让我们将这个插件添加到我们的 Vuex 声明:

```
var store = new Vuex.Store({
  state: {
    content: ''
  },
  mutations: {
    'UPDATE_CONTENT': function (state, newContent) {
      state.content = newContent
    }
  },
  plugins: [autosaverPlugin]
}) 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利！如果您在文本区域中键入内容，等待一秒钟，然后刷新页面，您将看到您的更改会持续存在。而你的 Vue 组件甚至不用担心；Vuex 插件正在做所有繁重的工作。

# 最后一次触摸

这很好，但是我们可以用一种方式向用户表明他们的工作已经保存了。这让用户放心，并帮助你看到应用程序正在工作。让我们添加一些文本，说明“保存...”或者说“得救了”。

首先，让我们给 Vuex 商店添加一些状态:

```
var store = new Vuex.Store({
  state: {
    content: '',
    saveStatus: 'Saved'
  },
  mutations: {
    'SET_SAVE_STATUS': function (state, newSaveStatus) {
      state.saveStatus = newSaveStatus
    },
    'UPDATE_CONTENT': function (state, newContent) {
      state.content = newContent
    }
  },
  plugins: [autosaverPlugin]
}) 
```

Enter fullscreen mode Exit fullscreen mode

`saveStatus`将包含一个字符串，向用户表明他们的工作是否已经保存。而`SET_SAVE_STATUS`会更新它。

现在让我们创建一个组件来显示它:

```
Vue.component('saving-indicator', {
  template: '<div>{{ saveStatus }}</div>',
  computed: {
    saveStatus: function () {
      return this.$store.state.saveStatus
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

并且让我们通过修改 app 模板将其显示在 textarea 上方:

```
var app = new Vue({
  el: '#app',
  template: '<div> <saving-indicator></saving-indicator> <text-entry></text-entry> </div>',
  store: store
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们更新我们的 autosaver 插件，将突变提交给`saveStatus` :

```
var autosaverPlugin = function (store) {
  store.commit('UPDATE_CONTENT', api.load())

  store.subscribe(function (mutation, state) {
    if (mutation.type === 'UPDATE_CONTENT') {
      store.commit('SET_SAVE_STATUS', 'Saving...')
      api.save(mutation.payload, function () {
        store.commit('SET_SAVE_STATUS', 'Saved')
      })
      return
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们的`index.js`文件看起来像示例 repo 中的文件。看这里:[https://github . com/isaaclyman/vuex-auto saver/blob/master/index . js](https://github.com/isaaclyman/vuex-autosaver/blob/master/index.js)

试试吧！当您在文本区域中键入时，信息会显示“正在保存...”一旦你完成，它会说“保存”。就像在谷歌文档里一样。这是一些超级巨星的网络开发。

有问题吗？更正？给我留言吧。

# 作业

以下是你可以添加到项目中的一些东西，以帮助你尝试一下:

*   如果`api.save`方法抛出一个错误，保存指示器会显示“错误”。
*   假 API 可以使用超时来模拟缓慢的 XHR 请求。
*   假 API 也可以返回一个承诺，而不是接受回调。
*   可以有两个文本区域，每个文本区域都可以在尽可能少的代码重复的情况下自动保存。提示:尝试自动保存对象而不是字符串。
*   如果`api.save`没有收到回调，它当前抛出一个错误。它可以预防这种情况。
*   一切都可能看起来更专业。这几乎是谷歌文档的 Craigslist 版本。