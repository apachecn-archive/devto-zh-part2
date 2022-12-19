# 使用 Vue.js + Parcel + TypeScript 创建搜索应用程序:第 3 部分，共 3 部分

> 原文：<https://dev.to/scottlepp/creating-a-search-app-with-vuejs--parcel--typescript-part-3-of-3-1j5f>

在[第二部分](https://dev.to/scottlepp/creating-a-search-app-with-vuejs--parcel--typescript-part-2-2ega)中，我们添加了 bootstrap-vue，并为搜索应用设置了一个基本布局。现在我们将创建组件并获取/显示我们的搜索结果。

在第 2 部分中，我们将所有的 html 放在应用程序组件中。这提供了一个快速的原型来查看我们的布局，但一个真正的工作应用程序将有单独的组件。独立组件的一些优点是封装了每个组件的复杂性，并且在某些情况下提供了组件的重用。

这是来自我们的应用程序组件的原始模板:

```
<template>
  <div id="app">
    <b-navbar toggleable="md" type="light" variant="light">
      <b-navbar-toggle target="nav_collapse"></b-navbar-toggle>
      <b-navbar-brand href="#">Zerch</b-navbar-brand>
    </b-navbar>
    <div class="container-fluid">
      <div class="row mx-auto">
        <!-- Search input section -->
        <section class="col-sm-12 pt-3 px-0">
          <b-form inline class="d-flex justify-content-center">
            <!-- Bug in bootstrap-vue - need div around input or button disappears -->
            <div class="col-md-6 col-8 pl-0">
              <b-input class="w-100 mr-sm-2" type="text" placeholder="Enter Search Term"/>
            </div>
            <b-button class="my-2 my-sm-0" type="submit">Search</b-button>
          </b-form>
        </section>
        <!-- Results section -->
        <section class="results">
          <div class="card-columns">
            <div class="card">
              <img class="card-img-top" src="https://dummyimage.com/mediumrectangle/222222/eeeeee" alt="Card image cap">
              <div class="card-body">
                <h5 class="card-title">Card title that wraps to a new line</h5>
                <p class="card-text">This is a longer card with supporting text below as a natural lead-in to additional content. This content is a little bit longer.</p>
              </div>
            </div>
          </div>
        </section>
      </div>
     </div>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我们的新应用程序组件现在看起来像这样:

```
<template>
  <div id="app">
    <b-navbar toggleable="md" type="light" variant="light">
      <!-- <b-navbar-toggle target="nav_collapse"></b-navbar-toggle> -->
      <b-navbar-brand href="#">Zerch</b-navbar-brand>
    </b-navbar>
    <div class="container-fluid">
      <div class="row mx-auto">
        <!-- Search input section -->
        <section class="col-sm-12 pt-3 px-0">
          <vs-input @search="onSearch"></vs-input>
        </section>
        <!-- Results section -->
        <section class="results">
          <vs-results :data="results"></vs-results>
        </section>
      </div>
     </div>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

注意我们现在有了 vs-input 和 vs-results 标签。让我们创建这些新组件。

我们将创建一个名为 vs-input.vue 的文件，并添加以下代码:

```
<template>
  <b-form inline class="d-flex justify-content-center">
    <!-- Bug in bootstrap-vue - need div around input or button disappears -->
    <div class="col-md-6 col-8 pl-0">
      <b-input v-model="term" class="w-100 mr-sm-2" type="text" placeholder="Enter Search Term"/>
    </div>
    <b-button class="my-2 my-sm-0" @click="search()">Search</b-button>
  </b-form>
</template>

<script lang="ts">
  import { Component, Vue, Provide } from 'vue-property-decorator'
  @Component
  export default class VsInput extends Vue {
    @Provide()
    term = '';
    search() {
      this.$emit('search', this.term);
    }
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

那么这个组件在做什么呢？为我们的搜索捕获输入，并向应用程序组件提供一个事件来指示用户想要搜索。

*   b-input 元素包含了 *v-model* 指令。这将把输入绑定到“term”变量
*   b-button 元素有一个 *@click* 指令，用于在单击按钮时触发“搜索”功能。
*   在我们的脚本标签中，我们有我们的类型脚本代码来声明*项*变量和*搜索*函数。搜索功能只是发出一个带有该词的事件，因此应用程序知道何时执行搜索。

现在让我们创建一个结果组件来显示我们的结果。添加一个名为 vs-results.vue 的新文件，代码如下:

```
<template>
  <div class="card-columns" >
    <div class="card" v-for="item in results" :key="item.id">
      <img v-if="item.thumb" class="card-img-top" :src="item.thumb" :alt="item.title" @error="error(item)">
      <div class="card-body">
        <h5 class="card-title">{{item.name}}</h5>
        <p class="card-text" v-html="truncate(item.description || item.abstract, 50)"></p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop, Provide } from 'vue-property-decorator'

@Component
export default class VsResults extends Vue {

  @Prop()
  data;

  get results() {
    return this.data;
  }

  truncate(text, limit) {
    text = text === undefined ? '' : text;    
    const content = text.split('  ').slice(0, limit);
    return content.join('  ');
  }

  error(item) {
    delete item.thumb;
    this.$forceUpdate();
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

让我们首先关注上面的 html:

*   v-for 将迭代我们的*结果*数组，该数组是从 app 组件传入的，我们将在后面看到。
*   img 标记使用 v-if 指令有条件地显示缩略图，该缩略图绑定到搜索结果项的 item.thumb 属性。
*   卡片标题绑定到 item.title
*   卡体绑定到 item.description 或 item.abstract。注意，这里我们使用 v-html 指令，因为该内容可能是 html，我们希望将其呈现为 html 而不仅仅是文本。我们还调用了一个 *truncate* 方法来限制文本。

现在让我们仔细看看 typescript 代码:

*   我们有一个属性叫做*数据*。应用程序组件将把它传入。
*   我们有一个计算函数叫做*结果*。这就是我们在模板 v-for 中引用的迭代结果。
*   truncate 函数将我们的描述限制在 50 个单词以内。
*   错误函数将处理下载失败的结果图像。这是我们的 img 元素上带有 [@error](https://dev.to/error) 指令的引用。

现在需要更改 app.vue 组件来处理来自 vs-input 组件的事件，执行搜索，然后将结果传递给 vs-results 组件:

```
<template>
  <div id="app">
    <b-navbar toggleable="md" type="light" variant="light">
      <!-- <b-navbar-toggle target="nav_collapse"></b-navbar-toggle> -->
      <b-navbar-brand href="#">Zerch</b-navbar-brand>
    </b-navbar>
    <div class="container-fluid">
      <div class="row mx-auto">
        <!-- Search input section -->
        <section class="col-sm-12 pt-3 px-0">
          <vs-input @search="onSearch"></vs-input>
        </section>
        <!-- Results section -->
        <section class="results">
          <vs-results :data="results"></vs-results>
        </section>
      </div>
     </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop, Provide } from 'vue-property-decorator'
import VsResults from './search-results/vs-results.vue';
import VsInput from './search-input/vs-input.vue';
import voyagerApi from './search-results/search-api';

@Component({
  components: {
    VsResults,
    VsInput
  }
})
export default class App extends Vue {

  @Provide() 
  results = [];

  async onSearch(term) {
    this.results = await voyagerApi.search(term);
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

*   注意，在上面的模板中，vs-input 使用@search 指令来绑定 onSearch 函数。这将在 vs-input 发出事件时触发上面的 onSearch 函数。
*   onSearch 函数将调用一个 api 来获取结果。我们接下来会看这个。
*   还要注意模板中的 vs-results :data 属性。这是 app 组件将结果变量传递给 vs-results 组件的地方。

快好了。注意上面我们导入了 voyagerApi。我们需要创造这样的环境。使用下面的代码添加一个名为 search-api.ts 的文件，该文件将从 solr 索引中获取搜索结果。

```
export default {
  search: async function(term: string): Promise<Array<any>> {
    // solr endpoint
    const host = 'http://voyagerdemo.com/';
    const path = 'daily/solr/v0/select';
    const fields = 'id,name:[name],thumb:[thumbURL],abstract,description'; // fields we want returned
    const api = `${host}${path}?q=${term}&fl=${fields}&wt=json&rows=20`;
    const call = await fetch(api);
    const json = await call.json();
    return json.response.docs;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码定义了一个“api ”,它是一个 solr 索引的 url。*字段*定义了我们想要返回的字段。 *q* param 传递用户输入的“术语”并希望过滤结果。我们使用内置的“fetch”函数对 api 进行 ajax 调用，并“等待”结果，这是一个承诺。

您可能还注意到搜索函数是一个*异步*函数。这允许我们使用 *await* 使代码看起来更加同步，而不是使用 Promise.then()语法。

就是这样！现在，如果您检查浏览器，您应该能够输入一个搜索词，然后单击“搜索”并看到类似这样的内容:

[![Run](../Images/d78361fd6471ec2ba16315d5b6454dd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oM4bR6gU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tau6ftboeu7gk58j6n45.PNG)

如果出现问题，您可以从这里获得完整的工作版本:[https://github . com/scottlepp/search-vue-parcel-typescript/tree/final](https://github.com/scottlepp/search-vue-parcel-typescript/tree/final)