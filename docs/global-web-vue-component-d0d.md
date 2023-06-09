# 全球 Web Vue 组件

> 原文：<https://dev.to/moxardi/global-web-vue-component-d0d>

## Vue 中的组件

[![alt text](img/ff8734dfcb43f2aef16633828b2a92bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--at-_9Sh3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/coursetro/posts/134-full.png)

web 应用程序中的 Web 组件是封装了标记、HTML 模板、CSS 和 Javascript 的 T2 块。

***Vue。JS web 组件*** 只是我们可以在不同的 vue 实例中使用的可重用代码或模板。

***Vue。JS web 组件*** 是可以反复重用的代码块。通过创建组件，您可以消除多次编写代码的需要，这有助于您的应用程序变得更加模块化和易于使用。

**成分语法** :

`Vue.component(tagName, options);`

*标记名*代表组件的名称，并接受一个字符串值。
*选项*代表一个函数或者一个选项对象，所以这个对象包含了一堆选项，这些选项可能通过模板属性来定义标记脚本，另外还有数据、方法、道具等选项或者属性。

定义组件:

要创建 vue.js 组件，您必须首先注册它。
下面是注册 Vue.js 组件的样子:

```
// Register vue component before creating the Vue instance
// template property holds the HTML elements to executed once component called
  Vue.component('message', { 
    template: '<p> Hey there, I am a re-usable component </p>'
   });

// Create the root vue instance after creating the vue components
let app = new Vue({
el: '#app-vue'
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用组件:

要使用 vue.js 组件，您必须通过使用名为组件名的自定义元素来调用该组件。

**调用组件语法** :

`<tagName></tagName>`

所以还是用起来吧！

```
 <div id="vue-app">
      <message></message>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

代码的输出:

```
Hey there, I am a re-usable component 
```

Enter fullscreen mode Exit fullscreen mode

现在，web vue 组件的另一个例子:

首先，注册组件:

```
// Register a global component named movie-card html tag
Vue.component('movie-card',{
// the props (defines the attributes of the movie-card html tag)
  props:['image','title'],

// the template (defines the inner html of the component)
template: `
<div>
    <img width="100" v-bind:src="image" v-bind:alt="title"/>
    <h2> {{ title }} </h2>
  </div>
`
});

// Root vue instance 
new Vue({
      el: '#vue-app'
 }); 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:道具选项的属性，我们可以通过使用 v-bind 指令或者通过 mustashue style `{{}}`在模板选项内部访问它。

换句话说:

*   如果我们使用 props 作为 Html 元素的属性值，那么我们必须在该属性上使用 v-bind 指令。
*   如果我们使用 props 作为任何 Html 元素的值，那么我们必须在 Html 元素中使用 Mustashue 样式`{{}}`。

二、调用组件:

```
<div id="vue-app">

 <movie-card
 title="Mo Salah"       
 image="http://www.arabnews.com/sites/default/files/styles/n_670_395/public/2018/06/04/1210816-177415191.jpg?itok=rgY5P4oi">
 </movie-card>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，您可以像使用 HTML 元素一样使用注册的组件。

最后，web vue 组件的第三个示例:

我们将使用**物化 CSS** 框架和 **VueJS** 框架来制作玩家卡片。

链接:[物化 CSS](https://materializecss.com)

```
<div class="container" id="vue-app">
      <div class="row">

   <player-card name="Mo Salah"
                bio="Professional Footballer for Liverpool FC and Egypt."             
                twitter="https://twitter.com/MoSalah" 
                image="https://d3j2s6hdd6a7rg.cloudfront.net/v2/uploads/media/
                       default/0001/68/thumb_67083_default_news_size_5.jpeg">
    </player-card>

   <player-card name="Cristiano Ronaldo" 
                bio="Professional Footballer for Juventus FC and Portuguesa."                                       
                twitter="https://twitter.com/cristiano" 
                image="https://d3j2s6hdd6a7rg.cloudfront.net/v2/uploads/media/
                       default/0001/68/thumb_67083_default_news_size_5.jpeg">
    </player-card>

   <player-card name="Neymar da Silva Santos" 
                bio="Professional Footballer for Paris Saint-Germain FC and Brasileira." 
                twitter="https://twitter.com/neymarjr" 
                image="https://images.cdn.fourfourtwo.com/sites/fourfourtwo.com/
                files/styles/image_landscape/public/neymarcropped
                _1dddbvc0xb8gt18uqwcscs9y4r.jpgitok=PVUv2vle&c
                =87b6d99828d88c1b8ffe17a08d24fc7d">
    </player-card>     

    </div>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们注册一个名为 *player-card* 的组件，然后分配名为 *name* 、 *image* 、 *bio* 、 *twitter* 的 4 个道具值来传递来自父节点的数据。

另外，在模板选项上签上姓名首字母，这是一个 HTML 字符串，显示在 HTML 文档中组件所在的位置。

请记住，将 4 props 值从父项绑定到模板选项。

```
 // Register component before instantiating the Vue instance

 Vue.component('player-card', {
      props: ['name', 'image', 'bio', 'twitter'],
      template: `
    <div class="col s12 m7 l4">
      <div class="card">
        <div class="card-image">
          <img v-bind:src="image" height="180px">
        </div>
        <div class="card-content">
            <span class="card-title red-text">  
              <div class="chip">
              {{name}}
              </div>
          </span>
          <p> {{bio}} </p>
        </div>
        <div class="card-action purple darken-4 ">
          <a :href="twitter" target="_blank" class="blue-text darken-4">Twitter Account</a>
        </div>
      </div>
    </div>
      `
    });

// Vue root instance
    let app = new Vue({
      el: '#vue-app',
    }); 
```

Enter fullscreen mode Exit fullscreen mode

下面是代码的结果:

[![3 player cards](img/4430112e8f23c054580854eb02f00e78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IlUfVrub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/hDHsdA/cards.png)

*重述*:Web Vue 组件给了你一次声明 HTML 元素并在多个页面上重用它们的能力。耶！这些是可重用的代码块。
所以，Vue 组件让你的代码更加动态。

此外，vue 组件可以在不同的 vue 实例中使用，因此当您想要在两个或三个 vue 实例中使用代码块时，您可以通过使用 vue 组件来完成，而不是在每个 vue 实例的 tamplate 选项中编写代码块，我们在一个 vue 组件中编写一次，然后在 Vue 实例范围中调用。

*参考*:[Vue-组件](https://vuejs.org/v2/api/#Vue-component)