# React 的 Vue——用我的 React 知识用 Vue 构建应用程序

> 原文：<https://dev.to/iwilsonq/the-vue-from-react---building-apps-with-vue-using-my-knowledge-of-react-2nn2>

[![view](img/6ccf701581f395e94c0ccd100fcda951.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jf-h1vZ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1507058868659-0a799c039186%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D94c4e53bf90ee9e6783e7e950c29166b%26auto%3Dformat%26fit%3Dcrop%26w%3D750%26q%3D80)

几周前，我有了一个疯狂的想法，想尝试一个新的前端框架，它不是 React。鉴于最近 Vue 背后的大肆宣传，我认为这将是一个很好的机会来弄脏它。

我通常通过浏览半打教程或视频课程来开始学习一项新技术。然后，在我消化了足够多的好例子之后，我将开始通过改变变量的名称来制作我自己的项目，然后在它们上面打上我的烙印。

然而，这次我想进行一个实验。

我将学习通过查阅 Vue 官方文档来构建一个用户界面。出于设计的目的，我倾向于对单调的网页感到气馁，我将使用 [Vuetifyjs](https://vuetifyjs.com) 。希望通过这样做，我能够对学习过程有一个新的认识。

## 【对事情做出反应】我需要在 Vue 中弄清楚

因为我精通 React，所以我希望知道一些关于用组件构建 web 应用程序的事情。也就是说，我知道在 React 中我们可以将数据作为道具传递下去，在 Vue 中我们也可以这样做吗？

*   我如何将信息从子组件传递给它们的父组件？

*   我到底应该如何处理状态？假设我不想接触像 Vuex 这样类似 Redux 的库。

*   路由是如何工作的？vue-router 很像 react-router 吗？

*   如果我想在我的组件被挂载时获取一些数据，没有一个`componentDidMount`生命周期方法，我该如何完成呢？

*   我正在使用一个名为 Vuetify 的风格框架，它附带了许多布局所需的组件。我如何定义和使用我自己的风格？

这些只是我在构建这个应用程序时希望回答的几个问题。

## App:每日即兴重复

这个网络应用程序将是一个日志，让人们发布他们播放即兴片段或整首歌的视频，并注明原创艺术家，以及一些标签或原创视频的链接。嗯，它不会很先进；为了简洁起见，我将跳过重要的音频/视频部分，更多地关注 Vue。

这听起来相当具体，但灵感来自于我目前想更频繁地练习弹吉他的愿望。我一直想组建一支摇滚乐队；我高中的时候有过一段短暂的感情，但后来我们就分道扬镳了。实际上，他们住在街尾。我们只是懒得带着设备到处跑。

但是一旦他们看到这个应用程序，他们就会意识到他们错过了什么。如果你想看这款应用的源代码，请点击 github 上的[查看。我们走吧。](https://github.com/iwilsonq/daily-riff)

## 入门

我从下载 Vue CLI 开始，并用它来初始化一个新项目。这将使我们经历一些与初始化 npm 项目没有太大区别的提示。

```
vue init webpack daily-riff 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了我们的启动样板文件(我检查了 vue-router ),我们就可以用 yarn dev 或 npm run dev 启动项目，并在 [http://localhost:8080](http://localhost:8080) 上看到它运行。

样板文件包括一堆可能有帮助的链接，但是我们要去掉它们，放入我们自己的代码。让我们添加 Vuetify，这样我们就可以用它来构建我们的布局。

```
yarn add vuetify  # or npm install --save vuetify 
```

Enter fullscreen mode Exit fullscreen mode

然后在`src/main.js`内部更新代码，产生如下内容。

```
// src/main.js
import Vue from 'vue'
import Vuetify from 'vuetify'
import App from './App'
import router from './router'
import 'vuetify/dist/vuetify.min.css' // Ensure you are using css-loader

Vue.use(Vuetify)
Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，这与构建 React 应用程序非常相似，只是我们没有使用 react-dom 呈现到我们的 html 模板中，而是定义了这个 Vue 类的一个实例，它允许您设置一个元素来呈现到哪些组件以及使用哪些组件。这也是我们设置路由器的地方。

如果你熟悉 Redux 或 Express 中的中间件，Vuetify 通过调用`Vue.use(Vuetify)`工作起来有点像中间件。通过在我们的应用程序的根目录中设置它，我们将能够在我们自己的组件中使用它的内置模板。

## 构建 App 布局

在我们去掉所有的样板代码之前，让我们添加我们自己的顶栏。通常当我开始一个新项目时，我会花很大力气做一个像样的顶栏(或者 navbar 或者工具栏，取决于你来自哪里)。然后我对这个项目失去了兴趣，因为其他事情都变得更难了，但是嘿，它在进步。首先在组件目录中创建一个`Toolbar.vue`文件。注意文件结尾，它不是。js 也不是. jsx.

```
<template>
  <v-toolbar
    color="red"
    dense
    fixed
    clipped-left
    app
  >
    <v-toolbar-title class="mr-5 align-center">
      <span class="title white-text">Daily Riff</span>
    </v-toolbar-title>
  </v-toolbar>  </template> 
<style>
.white-text {
  color: #fff;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

白色文本在那里，以便品牌在红色工具栏上更好地显示。其中一个文件的格式看起来与您的标准 JavaScript 文件略有不同。我们有可以放置标记的模板标签，样式规则的样式标签，正如我们将在下一个文件中看到的，我们可以使用脚本标签来定义 JavaScript。现在让我们设置 App 组件，将`src/App.vue`更改为以下内容。

```
// src/App.vue
<template>
  <div id="app">
    <v-app>
      <toolbar />
      <v-content>
        <v-container fluid>
          <router-view/>
        </v-container>
      </v-content>
    </v-app>
  </div> </template> 
<script>
import Toolbar from './components/Toolbar'
export default {
  name: 'App',
  components: {
    Toolbar
  }
}
</script> 
<style>
#app {
  font-family: 'Roboto', sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

还记得我们在`src/main.js`中使用 App 作为我们的根组件吗？这个文件的模板是在根目录中使用的标记。也就是说，当我们导入 Vue 组件文件时，我们得到的是它们的模板以及导出的数据。这里，App.vue 使用工具栏组件，因此我们必须导入工具栏，然后在要导出的对象的 components 字段中定义它。这让 App.vue 知道当它在模板中看到`<Toolbar />`或`<toolbar />`时如何反应。

Vue 中另一个美学上的不同是，当我们导入和定义一个组件时，组件的标签是不区分大小写的。Vue 中的约定倾向于“烤肉串式”标签标记，而不是“骆驼式”。

## 建设首页

请注意`src/router/index.js`，我们将更新命名，以便更好地反映我们即将创建的主页组件。

```
// src/router/index.js
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/components/Home'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Home',
      component: Home
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

看到这个，我真的很惊喜。我以为 vue-router 会有一个更复杂的浏览器路由方案，也许更类似于 react-router。在这个文件中，我们导入了一个路由器类，它的实例被导出供我们在根节点`src/main.js`中使用。我们所要做的就是定义一个路径、组件名和我们想要呈现的实际组件。

实际的 home 组件相当简单，它主要负责呈现一些数据源存储的记录列表。在这种情况下，我们将使用一个我专门为这个场合设计的。稍后会详细介绍。

让我们从添加呈现记录列表的代码开始，然后描述一下正在发生的事情。另外，一定要运行

```
npm install --save axios 
```

Enter fullscreen mode Exit fullscreen mode

这是 Home.vue 组件。

```
// src/components/Home.vue
<template>
  <v-container>
      <v-layout row wrap>
        <v-flex v-for="(record, i) in records" :key="i" xs4>
          <record :record="record" />
        </v-flex>
      </v-layout>
  </v-container> </template> 
<script>
import axios from 'axios'
import Record from '@/components/Record'

export default {
    name: 'Home',
    data: () => ({
      records: [],
      isLoading: false
    }),
    mounted() {
      this.getRecords()
    },
    methods: {
      getRecords() {
    this.isLoading = true
    axios
          .get('https://secret-headland-43248.herokuapp.com/records')
            .then(({ data }) => {
          this.records = data
          this.isLoading = false
        })
        .catch(err => {
          this.isLoading = false
          console.error(err)
        })
      }
    },
    components: {
      Record
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 首页模板:渲染列表和传递道具

这里的标记非常简单，它主要描述了如何使用 Vuetify 创建一个“3x”的布局。要记住的模式类似于

```
v-container -> v-layout -> v-flex (iterate over these!) 
```

Enter fullscreen mode Exit fullscreen mode

如果您来自 bootstrap，这种类的层次结构会更有意义，您的页面需要一个容器类、一行和该行的列。Vuetify 的工作方式类似于 flexbox 和 bootstrap 的组合(因为我们可以添加 xs4 或 offset-xs4 这样的道具)。这是需要考虑的其他事情，你总是可以推出不同风格的解决方案。

有趣的部分是我们赋予 v-flex 标签的 v-for 属性。我觉得把 JavaScript 放在标记属性里感觉有点奇怪；我仍然更喜欢 React 风格的呈现列表。在模板属性中，我们可以直接访问脚本中导出的一些值，比如数据或方法函数中返回的字段。

特别是，我们使用从数据导出的记录数组来呈现我们的记录列表。此时，由带有 v-for 属性的迭代标记包围的任何字段都可以访问迭代中的当前项或索引，在本例中是 record 对象。

请注意，我们还将记录组件称为“记录”。幸运的是，Vue 模板能够区分数据属性和其标记中的其他 Vue 组件。

这一行中的另一个“问题”是我们将道具传递给子组件的方式。为了传递一个道具，我们可以写一个类似

的属性

```
<record v-bind:record="record">
// equivalent to 
<record :record="record"> 
```

Enter fullscreen mode Exit fullscreen mode

`:<attr>`是一种速记符号，我们可以用它来使我们的组件不那么冗长。还要注意，引用中的属性或属性不一定是字符串。更容易将引号中的字符视为 JavaScript 来执行。JSX 的 React 等价物看起来更像这样:

```
<Record record={this.state.record} /> 
```

Enter fullscreen mode Exit fullscreen mode

## Home 脚本:定义生命周期挂钩、方法和数据

回顾一下模板标记，我们可以立即注意到 React 的生命周期方法的一些相似之处。当视图组件呈现在 DOM 上时，它的生命周期可以用创建、安装、更新和销毁来描述。这些术语绝不是巧合，它们是我们可以在`Home.vue`文件中导出的一些函数。

在这个特定的实现中，我只关心组件是何时安装的，因为这是我希望发出 API 请求来获取记录数据的地方。

看看从这个文件导出的数据和方法字段，这就是我们分别为这个组件定义“状态”和“类属性”的方式。与 React 不同，我们可以通过简单地为属性赋值来更新 Vue 组件的状态，例如

```
this.records = data  // instead of this.setState({ records: data }) 
```

Enter fullscreen mode Exit fullscreen mode

在我们已经实现的`getRecords`方法中可以找到一个更明确的例子，该方法调用了我专门为这种情况准备的 API。在我们的导出方法字段中定义的方法可以在我们的 Vue 组件的生命周期钩子中的任何地方被访问。

我们还没有讨论的唯一字段是 name 字段。它有点杂，但是通过定义它，我们可以在我们的模板中递归地呈现它。如果您使用的是 Vue 的 devtools，name 字段也有助于调试。

## 记录组件

好吧，我们终于有了主页渲染和解释。

现在我们已经有了管理状态的组件，如果您愿意，可以用一个智能组件来创建它所呈现的“哑”子组件。

```
// src/components/Record.vue
<template>
  <v-card width="350px">
    <v-card-media :src="record.imageurl" height="200px" class="card-media">
      <v-container fill-height fluid>
        <v-layout fill-height>
          <v-flex xs12 >
            <span class="headline">{{record.title}}</span><br/>
            <span>{{record.artist}}</span>
          </v-flex>
        </v-layout>
      </v-container>
    </v-card-media>
    <v-card-title primary-title>
      <div>
        <div>{{record.description}}</div>
      </div>
    </v-card-title>
    <v-card-actions>
      <v-btn flat color="orange" :href="record.taburl">Tab</v-btn>
    </v-card-actions>
  </v-card>  </template> 
<script>
export default {
    props: {
        record: {
        title: String,
        artist: String,
        description: String,
        taburl: String,
        imageurl: String
    }
    }
}
</script> 
<style>
.card-media {
    text-align: left;
    color: #fff;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这个文件中的标记多了一点，但是逻辑少了一点。我在标记中自由使用了 Vuetify 卡。模板中另一个有趣的地方是我们如何访问记录属性。当在属性引用中使用它时，你会看到我们可以像访问任何其他 JavaScript 对象一样访问属性。

类似地，我们可以在实际的标签中使用双花括号符号做同样的事情，例如

```
<div>{{record.description}}</div> 
```

Enter fullscreen mode Exit fullscreen mode

在 Vue 中，我们被迫在 React 中定义我们所谓的道具类型。为了让一个组件对它接收到的道具进行操作，它必须声明它所期望的道具。在我们的例子中，我甚至定义了记录属性中每个字段所期望的类型。我也可以在没有这些类型的情况下定义它，只需在一个数组中指定 record:

```
export default { props: ['record'] } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，只要记录组件收到一个名为“record”的属性，就不会有错误。

在这个文件中，我们还可以看到，我们可以在。vue 文件。关于 Vue 样式的一个巧妙之处是，你甚至可以给 styles 标签一个“scoped”属性，这样这些样式就只影响文件的组件。

在这一点上，如果你一直跟着做，你可能能够用`yarn dev`或`npm run dev`启动服务器，并检查我们当前拥有的应用程序。

到目前为止还不错？希望这管用！

现在来说说添加内容。

## 上传内容

现在，老实说，我打算有一个更令人兴奋的 web 表单，一个允许你或我录制声音片段或视频并直接上传到 Soundcloud 或 Youtube 的表单。嗯，我认为这仍然是可能的，但这超出了我们的小 Vue 教程的范围。毕竟，我不能让你整天呆在这里。

尽管如此，让我们继续努力，将这段代码添加到一个名为`Upload.vue`

的新文件中

```
// src/components/Upload.vue
<template>
  <v-layout>
    <v-flex sm8 offset-sm2>
      <h3 class="headline pb-4">Upload a Killer Riff!</h3>
      <v-form v-model="valid" ref="form" lazy-validation>
        <v-text-field
          label="Song Title"
          v-model="title"
          :rules="titleRules"
          placeholder="Add song title"
          required
        ></v-text-field>
        <v-text-field
          label="Artist"
          v-model="artist"
          :rules="artistRules"
          placeholder="Add artist"
          required
        ></v-text-field>
        <v-text-field 
          label="Description"
          v-model="description"
          :rules="descriptionRules"
          placeholder="Add description"
          multi-line
        ></v-text-field>
        <v-text-field
          label="Image url"
          v-model="imageurl"
          :rules="imageurlRules"
          placeholder="Add url of image"
        ></v-text-field>
        <v-text-field
          label="Tab url"
          v-model="taburl"
          :rules="taburlRules"
          placeholder="Add url of tab"
        ></v-text-field>
        <v-btn
          @click="submit"
          :disabled="!valid"
        >
          submit
        </v-btn>
        <v-btn @click="clear">clear</v-btn>
      </v-form>
    </v-flex>
  </v-layout> </template> 
```

Enter fullscreen mode Exit fullscreen mode

我知道有很多文字。这实际上只是一个表单的一大堆字段，但是从中可以得到一些有趣的东西。其中之一是 v-model 属性。这个属性是组件状态和用户输入之间双向数据绑定的语法糖。

在 React 中，我们通常会给输入组件一个`onChange`属性，并用它来更新状态。这里简单一点。

如果我们想要更严格的验证逻辑，比如说电子邮件地址验证，我们可以为该特定字段定义一组规则，并将它们传递给输入。稍后会有更多的介绍。

还有@click prop，它是`v-on:click`的简写，允许我们定义处理用户输入事件的方法。我们有两个具有这些点击属性的按钮；向一个按钮传递 submit 方法，向另一个按钮传递 clear 方法。

下面是剩余的代码:

```
// src/components/Upload.vue

// <template> ... </template>

<script>
import axios from 'axios'

export default {
    data: () => ({
        valid: true,
        title: '',
    titleRules: [
        v => !!v || 'Title is required',
        v => (v && v.length <= 140) || 'Title must be less than 140 characters'
    ],
    artist: '',
    artistRules: [
        v => !!v || 'Artist is required',
        v => (v && v.length <= 140) || 'Artist must be less than 140 characters'
    ],
    description: '',
    descriptionRules: [
        v => !!v || 'Description is required',
        v => (v && v.length <= 300) || 'Title must be less than 300 characters'
    ],
    taburl: '',
    taburlRules: [v => !!v || 'taburl is required'],
    imageurl: '',
    imageurlRules: [v => !!v || 'imageurl is required']
    }),

    methods: {
    submit() {
        if (this.$refs.form.validate()) {
        axios
            .post('https://secret-headland-43248.herokuapp.com/records',
                {
                title: this.title,
                artist: this.artist,
                description: this.description,
                taburl: this.taburl,
                    imageurl: this.imageurl
            },
                { 
                          headers: { 
                            'content-type': 'application/json' 
                          } 
                        })
            .then(res => {
                if (res.status === 200) {
                console.log('good!')
            }
            })
            .catch(err => {
                console.log('bad!')
            console.error(err)
            })
            }
    },
        clear() {
            this.$refs.form.reset()
    }
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，数据字段相当简单；存在绑定到输入字段的字段以及每个字段的规则。这些函数被定义为验证函数的数组，接受输入值并返回一个描述输入是否有效的布尔值。这里还描述了整体形式的有效性。

在方法下有两个，一个提交表单，向我们的后端发出 axios POST 请求，另一个清除表单中的值。

Vue 组件有许多可用的实例属性，如本表单中的`this.$refs`。我认为这些实例属性主要用于进行事件处理和生命周期，但我们似乎也可以访问它们。

现在让我们通过在路由器中设置一个新条目来连接它:

```
// src/router/index.js
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/components/Home'
import Upload from '@/components/Upload'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Home',
      component: Home
    },
    {
      path: '/upload',
      name: 'Upload',
      component: Upload
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

最后，向 Home.vue 文件添加一个 FAB，这样我们就可以从主页访问表单。

```
// src/components/Home.vue
<template>
  <v-container>
    <v-layout row wrap>
      <v-flex v-for="(record, i) in records" :key="i" xs4>
        <record :record="record" />
      </v-flex>
    </v-layout>
    <router-link to="/upload">
      <v-btn fixed dark fab bottom right color="pink">
    <v-icon>add</v-icon>
      </v-btn>
    </router-link>
  </v-container> </template> 
// other Home.vue code 
```

Enter fullscreen mode Exit fullscreen mode

您会看到，我只是在这里添加了包装在路由器链接中的 v-btn，这里没有复杂的路由。只是几个按钮。如果一切顺利，你应该可以启动它！

[https://thepractical dev . S3 . Amazon AWS . com/I/8 b 8 sckeaz 8 oxr 7 M9 dqq 7 . png](https://thepracticaldev.s3.amazonaws.com/i/8b8sckeaz8oxr7m9dqq7.png)

差不多结束了。同样，这个应用程序源于我想在吉他上更持续地练习粉碎的愿望。谢天谢地，我可以说我实际上在这点上变得更加一致了——尽管事实上花了一个多星期才推出这篇文章！

理想情况下，该表单将包含音频或视频记录功能。这不会太难，但是对于这篇博文的范围来说，我认为把它留到以后的续作中是明智的。

如果你正在寻找一个好的 Vue 入门指南，看看 Víctor Adrían 的这个[帖子](https://dev.to/lobo_tuerto/quickstart-guide-for-a-new-vuejs-project--5ed2)。

下次见。

* * *

好奇更多帖子或者妙语连珠？在[媒体](https://medium.com/@iwilsonq)、 [Github](https://github.com/iwilsonq) 和 [Twitter](https://twitter.com/iwilsonq) 上关注我吧！