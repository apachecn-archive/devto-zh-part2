# Vuejs 和 GitHub API 入门

> 原文：<https://dev.to/aveeksaha/getting-started-with-vuejs-and-the-github-api-1pk1>

如何用 Vue.js 制作 web app 的初学者教程。

# 简介

本教程是给初学 Vue.js 的，vue . js 是一个类似 Angular 和 React 的渐进式 JavaScript 框架。你可以阅读更多关于 vue [`here`](https://vuejs.org/) 。因为 Vue 是一个 JavaScript 框架，所以你需要 HTML、JS 和 CSS 的基础知识。

我将教你如何制作一个 web 应用程序，你可以通过查询 [`GitHub API`](https://developer.github.com/v3/) 来查看 GitHub 发布的下载次数。

## 什么是发布？

发布是 GitHub 打包并向用户提供软件的方式。存储库所有者可以上传可下载的文件，用户可以将这些文件安装或提取到他们的系统中，作为分发软件的一种方式。这里可以看到一个例子 [`Music Player releases`](https://github.com/Aveek-Saha/MusicPlayer/releases) 。

不过这个系统有一个问题，它不会显示你的软件或软件包被下载的次数。发布软件的人很可能想知道它有多受欢迎，或者有多少人在使用他的软件。因此，我们将构建一个应用程序来帮助我们填补这一空白。

# 设置

有许多方法可以将 Vue.js 包含在应用程序中，但是对于本教程，我们将使用 Vue CLI。

安装 CLI。

```
npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

要创建新项目，运行:

```
vue create vue-tut 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一个选择`default`预设或`manually select features`的提示。默认选项对我们来说是合适的，所以只需按 enter 键并等待 CLI 初始化项目。光盘放入目录并运行`npm run serve`。如果项目创建正确，当您转到`http://localhost:8080`时，应该会看到一个默认的 web 页面。

项目文件夹将有这样的结构

```
 vue-tut
  |----node_modules
  |
  |----public
  |     |--index.html
  |     |--favicon.ico
  |
  |----src
  |     |----assets // logo.png
  |     |----components // contains a HelloWorld.vue component
  |     |
  |     |--App.vue // main component
  |     |--main.js
  |
  |-- // other files...
  |
  |--package.json 
```

Enter fullscreen mode Exit fullscreen mode

一些有用的脚本:

*   在本地主机上运行:`npm run serve`
*   为生产而构建:`npm run build`

我们将主要处理`src`文件夹中的文件。在`index.html`中，我们将添加 Bootstrap 和 JQuery 来给我们的应用程序增加一些视觉吸引力。将以下内容放在 head 标签中。

```
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo"
crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49"
crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy"
crossorigin="anonymous"></script> 
```

Enter fullscreen mode Exit fullscreen mode

我们将通过 GitHub API 获得发布版本中每个文件的下载次数。为了发出这些 Api 请求，我们将使用`Axios`，这是一个基于 promise 的 HTTP 客户端，用于浏览器和 node.js。

# 组件

Vue 组件有 3 个部分

```
<template>
<!-- for HTML and components -->
</template>

<script>
// data and methods of the component
</script>

<style>
/* styles for the component */
</style> 
```

Enter fullscreen mode Exit fullscreen mode

让我们从将`HellWorld.vue`重命名为更恰当的名称开始，比如`Data.vue`，因为我们将在那里处理和显示 API 请求数据。重命名后，务必用`Data`重命名`Data.vue`和`App.vue`中所有出现的`HellWorld`。

在`Data.vue`中，删除`style`标签和`template`标签之间的所有内容。您的文件应该如下所示。

```
<template>
</template>

<script>
export default {
  name: 'Data',
}
</script>

<style scoped>

</style> 
```

Enter fullscreen mode Exit fullscreen mode

我们将为`App.vue`添加一个导航栏、一个容器 div 和一些样式，文件应该是这样的。

```
<template>
 <div id="app">
 <!-- added a navbar -->
   <nav class="navbar navbar-dark bg-dark">
     <span class="navbar-brand mb-0 h2">GitHub Download Stats</span>
     <a href="https://aveek-saha.github.io/" class="navbar-brand mb-0 h1 text-right">
       <i class="fab fa-github fa-inverse"></i>
     </a>
   </nav>

   <!-- wrapped the data component in a container -->
   <div class="container">
   <!-- inject the Data component -->
     <Data/>
   </div>

 </div>
</template>

<script>
   // import the data component
import Data from './components/Data.vue'

export default {
    name: 'app',
    components: {
        Data
    }
}
</script>

<style>
   /* global styles */
body{
    background-color: #FEFEFE
}

.asset{
    word-break: break-all;
}

#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这基本上是我们将对`App.vue`做的所有修改。所有的魔法都发生在`Data.vue`中，这里是我们主要的逻辑所在。

## 数据.视图

这是我们发出 API 请求的地方，为了使用`axios`,它必须被导入到组件中。除此之外，我们还将在这个组件的`script`部分创建一些变量。

```
 // import axios
import axios from 'axios';

export default {
 name: 'Data',
 // all the data belonging to this component goes
 // to data() and is returned as an object
 data() {
   return {
       // declare some variables that we'll use later

       // owner of the repository
       username: "",
       // name of the repo
       repository: "",
       // list of releases
       releases: [],
       // total downloads of all releases combined
       grandTotal: 0,
       // if the query returned a status 404 or not
       success: true,
       // does the repository have any releases?
       empty: false,
       // base url
       url: "https://api.github.com"
   }
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要制作一个表单，以便人们可以输入`username`和`repository`，我们将把它添加到`template`标签中。

```
 <div class="row justify-content-center">
   <div class="col-lg-6">

       <div class="card bg-light shadow-sm mb-3 rounded" style="margin-top: 2%">

           <div class="card-body">
               <h5 class="card-title">Enter Details</h5>

               <form>
                   <div class="form-group">
                       <input type="text" class="form-control form-control-sm" v-model="username" id="username" placeholder="Your username" >
                   </div>

                   <div class="form-group">
                       <input type="text" class="form-control form-control-sm" v-model="repository"
                       list="repos" id="repository" placeholder="Repository name" v-on:keyup.enter="sendReq">
                   </div>
               </form>

               <button class="btn btn-outline-dark" v-on:click="sendReq">Submit</button>
           </div>
       </div>

   </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码显示了 Vue 中的两个重要概念:

*   **数据绑定-** 在下面这一行我们使用`v-model="username"`，这意味着我们将输入的值绑定到变量`username`。当输入字段的值改变时，`data()`中的变量`username`也会改变，反之亦然。

```
<input type="text" class="form-control form-control-sm"
v-model="username" id="username" placeholder="Your username" > 
```

Enter fullscreen mode Exit fullscreen mode

*   **事件处理-** 这里我们使用`v-on:click="sendReq"`，这意味着当我们点击这个按钮时，它将调用`sendReq`，这是我们现在要实现的一个方法。

```
<button class="btn btn-outline-dark" v-on:click="sendReq">Submit</button> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了输入表单，当我们单击 Submit 时，它什么也不做。我们需要编写向 GitHub api 发送请求的`sendReq`方法。在`script`
中做如下修改

```
 import axios from 'axios';

export default {
 name: 'Data',
 data() {
   return {
     username: "",
     repository: "",
     releases: [],
     grandTotal: 0,
     success: true,
     empty: false,
     url: "https://api.github.com"
   }
 },
 // add methods
 methods: {

   sendReq: function(){
     var that = this;
     this.empty = false;

     // get data using a promise with axios
     // the request url has the format base + /repos/:user/:repo/releases
     axios.get(this.url + '/repos/' + this.username + "/" + this.repository + "/releases" )
       .then(function (response) {

         var data = response.data

         // the total of all the release downloads
         that.grandTotal = 0

         for (let i = 0; i < data.length; i++) {

           // total of a particular release version
           var total = 0
           data[i].total = 0

           for (let j = 0; j < data[i].assets.length; j++) {
             // add up the download counts of the individual
             // binary/packaged files for a particular release version
             total += parseInt(data[i].assets[j].download_count)
           }
           // add a new field to the data object
           data[i].total = total
           that.grandTotal += total
         }

         // that.releases is an array of releases
         that.releases = data

         // if we got this far that means the request was a success
         that.success = true
         if(response.data.length === 0){
             // check if there are any releases for the repo
           that.empty = true
         }       
       })
       .catch(function (error) {
           // if there's an error then the request was not a success
         that.success = false
         that.empty = false

       });

   }
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在如果我们输入一个有效的`username`和`repository`并点击提交，响应将被存储在`releases`中。现在我们需要一种方法来显示我们得到的信息。由于`releases`是一个数组，我们将使用`v-for`为每个版本迭代显示一张卡片。

为此，我们将创建一个名为`Card`的新组件，我们将通过**属性**传递来自`Data`组件的数据。这意味着我们从父组件传递给属性的任何值都可以被子组件读取，并成为该组件的一个属性。

将以下几行添加到`Data.vue`

在`Template`中

```
 <div class="row justify-content-center">
   <div class="col-lg-6">

       <h5 v-if="grandTotal && success" style="margin-bottom: 3%;">
           Total Downloads: {{grandTotal.toLocaleString()}}
       </h5>

       <!-- show a card for every element in releases -->
       <!-- release and index are props in the <Card> component -->
       <Card v-for="(release , index) in releases" :key="index" v-bind:release="release" v-bind:index="index" ></Card>

       <!-- if request not successful then say that the repo could not be found -->
       <h5 v-if="!success" style="margin: 2%;">
           No repository found
       </h5>

       <!-- if the repo exists but it has no releases, show that it has none -->
       <h5 v-if="empty" style="margin: 2%;">
           No Releases
       </h5>

   </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在`script`中增加

```
 // card component that we'll be creating
import Card from './Card.vue'

export default {
  name: 'Data',
  components: {
    Card
  },
  // ... rest of the exports
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们可以看到 Vue 的 3 个更重要的特性:

*   **列表呈现-**`v-for`指令用于呈现基于数组的项目列表。使用`v-for`指令的另一种方式是`v-for="item in items"`，这更简单。在这种情况下，我们根据`releases`数组的值来渲染卡片。

```
<div class="card shadow-sm mb-3 rounded"
v-for="(release , index) in releases" :key="index"> 
```

Enter fullscreen mode Exit fullscreen mode

*   **条件渲染-** 我们使用`v-if`根据条件显示或隐藏内容。例如，在本例中，我们希望仅当 GET 请求不成功时才显示此消息。`v-if`可与`v-else`和`v-else-if`配合使用。

```
<h5 v-if="!success" style="margin: 2%;">
No repository found
</h5> 
```

Enter fullscreen mode Exit fullscreen mode

*   **类和样式绑定-**`v-bind:class`或`v-bind:style`可用于根据条件操作元素的类或样式。如下图所示，如果`release.prerelease`为真，那么 div 将被赋予一个类`alert-warning`，如果`index == 0`是数组中的第一个元素，那么 div 将被赋予一个类`alert-success`。

```
<div class="card-header"
v-bind:class="{ 'alert-warning': release.prerelease , 'alert-success': index == 0 }" > 
```

Enter fullscreen mode Exit fullscreen mode

## 卡.视图

现在我们将创建卡组件。创建一个名为`Card.vue`的文件，并将以下内容放入该文件。

```
 <template>
    <div class="card shadow-sm mb-3 rounded " style="margin-top: 2%; text-align:left" v-if="success" v-for="(release , index) in releases" :key="index">

           <div class="card-header" v-bind:class="{ 'alert-warning': release.prerelease , 'alert-success': index == 0 }" >
               <h5 class="card-title" style="margin-bottom:0px">

               <!-- Use the prop values passed by the parent -->
                   <a v-bind:href="release.html_url" class="alert-link" style="color:inherit"> {{release.tag_name}}</a> &nbsp;> &nbsp;
                   <span class="text-muted h6">
                   {{release.total.toLocaleString()}} downloads
                   </span>
               </h5>
           </div>

           <div class="card-body" >

           <!-- create a table with the name of the file and the number of downloads -->
               <table class="table table-borderless table-sm">
                   <thead>
                       <tr>
                           <th scope="col">Name</th>
                           <th scope="col">Downloads</th>
                       </tr>
                   </thead>
                   <tbody>
                       <tr v-for="( asset , index) in release.assets" :key="index">
                           <th scope="row" class="asset">{{asset.name}}</th>
                           <td>{{asset.download_count.toLocaleString()}} </td>
                       </tr>
                   </tbody>
               </table>

           </div>
       </div>
</template>

<script>

export default {
  name: 'Card',
  // Props that become properties when release and index are passed from the parent
  props: ['release', 'index'],
}
</script>

<style scoped>

.alert-warning{
  color: inherit
}
.alert-success{
  color: inherit
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

# 包装完毕

现在，如果我们用`npm run serve`为应用程序提供服务，您应该会看到类似这样的内容:

[![Screenshot1](img/b42947aa52aa4a25c2b380bd7a12382e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5_UZxgcD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://home.aveek.io/blog/githubstats/screenshot2.png)

当您单击 submit，或在键入包含版本的有效存储库后输入时，您应该会看到总下载量和每个单独版本的下载量:

[![Screenshot2](img/2340f73e2422fcc0a31645db4d90c432.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NIJzK4di--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://home.aveek.io/blog/githubstats/screenshot3.png)

就是这样！您已经使用 Vue 从头开始创建了一个真实世界的应用程序，并使用了一些重要的功能，如-

*   **数据绑定**
*   **事件处理**
*   **列表渲染**
*   **条件渲染**
*   **类和样式绑定**

# 代号&演示

如果你想要这个项目的代码，叉这个 GitHub repo - [`GitHub Stats`](https://github.com/Aveek-Saha/GithubStats)

如果你想看现场演示，试试这里- [`Live Demo`](https://githubstats0.firebaseapp.com/)