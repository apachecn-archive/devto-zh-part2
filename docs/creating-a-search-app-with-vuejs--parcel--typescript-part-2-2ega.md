# 使用 Vue.js + Parcel + TypeScript 创建搜索应用程序:第 2 部分，共 3 部分

> 原文：<https://dev.to/scottlepp/creating-a-search-app-with-vuejs--parcel--typescript-part-2-2ega>

在[第 1 部分](https://dev.to/scottlepp/creating-a-search-app-with-vue--parcel--typescript-part-1-52f9)中，我们设置了开发应用程序的基本工具(vue、包和类型脚本)。在这里，我们将添加一个工具，bootstrap，来布局我们的应用程序，使它看起来更专业，响应更快。

**第一步**。从你的终端添加 bootstrap-vue 到我们的项目:

```
npm install bootstrap-vue --save 
```

Enter fullscreen mode Exit fullscreen mode

**第二步**。导入引导 vue 组件和 css

*   打开 *main.ts* 并在第 2 行后添加以下内容:

```
import BootstrapVue from 'bootstrap-vue'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

Vue.use(BootstrapVue); 
```

Enter fullscreen mode Exit fullscreen mode

**第三步**。添加简单的导航栏

打开我们的 *app.vue* 文件。在`div id="app"`下粘贴以下内容:

```
 <b-navbar toggleable="md" type="light" variant="light">
      <b-navbar-toggle target="nav_collapse"></b-navbar-toggle>
      <b-navbar-brand href="#">Zerch</b-navbar-brand>
    </b-navbar> 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用了 bootstrap-vue 中的 b-navbar 组件。详见[导航条](https://bootstrap-vue.js.org/docs/components/navbar/)。

**第三步**。添加我们的搜索输入和结果。

这里我们将添加一个带有搜索按钮的输入部分，然后添加一个结果部分。这是临时的，只是为了检查我们的布局。稍后我们将把这些部分转换成组件。在我们的导航栏下面添加以下内容:

```
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
```

Enter fullscreen mode Exit fullscreen mode

现在，在您的浏览器中，您应该会看到:

[![Run](../Images/349dcc314830d3c0a61b551c0f20eb2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---wJRzRbs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6c80x9685wus7m2grfj9.PNG)

如果出现问题，您可以在这里获得完整的工作版本:[https://github . com/scottlepp/search-vue-parcel-typescript/tree/part-2](https://github.com/scottlepp/search-vue-parcel-typescript/tree/part-2)

我们的布局已经确定了！在[第 3 部分](https://dev.to/scottlepp/creating-a-search-app-with-vuejs--parcel--typescript-part-3-of-3-1j5f)中，我们将部分分成组件，并创建一个从 solr 索引中获取结果的工作应用。