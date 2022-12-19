# Hyperapp + Hyperapp-router:创建一个电影购物车 web 应用。

> 原文：<https://dev.to/thobyv/hyperapp--hyperapp-router-create-a-movie-shopping-cart-web-app-47d>

大家好，这是我关于 dev.to 的第一个教程，我很乐意与你们分享我一直在学习的一个很棒的前端开发框架；Hyperapp JS。

我们将构建一个[电影购物车单页应用](https://thobyv.github.io/Hyperapp-shopping-cart-demo/)来学习如何使用 hyperapp 的一些基本功能，包括虚拟 dom 渲染、路由和应用状态管理。

[![screenshot-hyperapp-router-tutorial](../Images/2e99c41c505bfad70bb14a6779173087.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1uOyeFZA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hux80q9k5reymj11aibz.JPG) 
*截图*

这里是 github repo 的[，以防你想直接进入代码，这里是 github 页面上的](https://github.com/ThobyV/Hyperapp-shopping-cart-demo)[现场演示](https://thobyv.github.io/Hyperapp-shopping-cart-demo/)。继续玩按钮。

## 要求

遵循/完成本教程并不需要太多，但 hyperapp 的基础知识([快速文档](https://github.com/hyperapp/hyperapp)中有一个非常简单的例子)，并且了解 Javascript 的 ES6 语法的基础知识也会有所帮助。

**我们将安装的 npm 软件包**

Hyperapp:一个用于构建现代 web 应用程序的微框架，它将状态管理与支持键控更新&生命周期事件的虚拟 DOM 引擎相结合——所有这些都没有依赖性。

[Hyperapp-router](https://github.com/hyperapp/router):Hyperapp 的官方路由包。

Hyperapp-transitions:我们组件的平滑动画。

布尔玛:基于 flex-box 的 css 样式框架。

# 入门

如果你通读了 [hyperapp 基础](https://github.com/hyperapp/hyperapp#getting-started)，我想你已经知道 hyperapp 的状态、动作和视图概念，但是如果你仍然有点困惑；我抓住你了，伙计。

**状态**
状态基本上只是与 webapp 或任何其他软件的视图/组件相关联的数据。任何时候你在 twitter 上喜欢一个帖子，然后这个爱的形状的图标变成了红色，我们可以说你已经把它的状态从白色变成了红色，其他的例子包括在 css 卡的列表/网格中呈现的来自 API 的数据，跨许多组件使用的用户的用户名或个人资料图片。

**动作**
如前所述，hyperapp 为我们提供了使用动作来更新或更改应用程序中所有组件的状态数据的方法。在 hyperapp **中，动作**是开发者可以创建的功能。Hyperapp 支持异步动作和产生副作用的动作。

当状态发生变化时，视图功能会根据你想要的样子自动更新虚拟 DOM，并呈现我们的组件。

## 设置我们的项目

在本教程中，我们不会讨论如何设置 rollup 或 webpack & babel 配置，使用这个[hyperapp-roll up-babel-hyperapp-router](https://github.com/ThobyV/hyperapp-rollup-babel-hyperapp-router)样板文件会更快更容易。它包含我们开发/发布应用程序所需的依赖关系/汇总模块捆绑配置。

让我们在终端中运行以下命令来克隆 repo，导航到文件夹并安装来自 *package.json* 的所有项目依赖项，并添加 bulma 包进行样式化。

```
 git clone https://github.com/selfup/hyperapp-one
    cd hyperapp-one
    npm install
    npm install bulma bulma-slider bulma-switch bulma bulma-badge 
```

运行以下命令，并在您的浏览器中访问 **localhost:8080** 来查看我们的应用程序。

```
 npm start 
```

我们的样板文件带有 **live reload** ，因此浏览器会自动刷新我们的应用程序，以反映我们在代码中所做的任何保存/更改。

### 文件夹结构

在本教程中，我们不会涵盖我们项目中所有的文件/文件夹解释(我太懒了，现在不会！).

但是解释我们在项目中经常使用的主要文件夹/文件总是好的。

#### `/src folders:`

在主文件夹`/src`中，您会发现我们有文件夹:

**/state** 带一个 *state.js* 文件。

**/actions** 带 *actions.js* 文件。

带有惰性/容器组件文件的**/视图/容器**。

**/具有常规组件文件的组件**。

**/config** 文件夹中存放我们想要的任何帮助函数/文件。它在这个样板文件中是空的。

#### `/src files:`

**index.js** 作为我们模块捆绑器的入口文件。

**routes.js** 文件用于我们的路线和查看功能。

(我们很快就能详细了解每个文件在一瞬间做了什么)。

### 伪造我们的数据。

导航到`src/config`文件夹，创建一个`data.js`文件，里面会有假的顶级电影数据，你可以从[这里](https://github.com/ThobyV/Hyperapp-shopping-cart-demo/blob/master/src/config/data.js)复制数据，粘贴到`data.js`文件中。

在与`config`文件夹相同位置的`styles`文件夹中，创建另一个名为`variables.sass`的 sass 文件来定制 bulma 并声明我们的 bulma 包。

编辑`app.sass`文件，并为我们的 bulma 包添加以下 sass 导入:

```
 @import "variables";
@import "node_modules/bulma/bulma.sass";
@import "node_modules/bulma-badge/src/sass/index.sass";
@import "node_modules/bulma-switch/src/sass/index.sass";
@import "node_modules/bulma-slider/src/sass/index.sass"; 
```

在`variables.sass`中，从[复制并粘贴以下样式/变量到这里](https://github.com/ThobyV/Hyperapp-shopping-cart-demo/blob/master/src/styles/variables.scss)，如果你想的话，你可以自定义它，但是为了得到我们应用程序的黑暗主题，我们需要使用它。我们的`rollup`配置包括一个在我们的项目中编译 sass 的包。

### 关于 JSX

我们将使用`JSX`编写我们的组件。

JSX 是一种语言语法扩展，可以让你编写夹杂着 JavaScript 的 HTML 标签。因为浏览器不理解 JSX，所以我们使用一个类似于`babel`的 transpiler 来将其转换成幕后的`hyperapp.h`函数调用。

## 现在言归正传！

### 建立我们的 app 状态模型。

我们要做的第一件事是声明我们的应用程序将使用的状态模型，导航到路径`src/state.js`并添加代码:

```
 import { location } from "@hyperapp/router"

import  { data } from '../config/data'

export const state =  {
  location: location.state,    
  movies: data,
  movie_list: [],
  cart: [],
  range_value: 160,
  switch_value: false,
  cart_item_count: 0,
  cart_item_total: 0,
} 
```

在这段代码中，我们按照 hyperapp 的要求导入 hyperapp-router `location` api 函数，并将其注册到我们的状态对象中，以便进行路由。

我们还从`config/`文件夹导入我们的模拟数据，然后将其设置为我们的`movies`状态值。在其他情况下，我们可以从任何地方获得这些数据；来自服务器等的 json 响应，但这里我们只是把它伪装成已经得到的响应数据。这就是我们的[假数据](https://github.com/ThobyV/Hyperapp-shopping-cart-demo/blob/master/src/config/data.js)的样子。

之后，我们创建一个空数组，然后连接到我们的`movie_list`属性，它是空的，所以我们可以用我们想要的任何类型的数据填充它，稍后我们将在我们的应用程序的功能中使用它。

`cart` state 属性也是一个空数组，它将包含用户使用我们即将定义的`ADD_TO_CART`动作添加到购物车的任何电影。

`range_value`将保存来自范围滑块元素的整数值。这里的默认值是 160。

`switch_value`将保存一个 html 开关元素的布尔值。

`cart_item_count`将保存`cart`数组中项目计数的整数值。

`cart_item_total`将在`cart`数组中保存物品总价的整数值。

定义我们的状态对象及其属性/值非常好，因为它是我们应用程序的数据模型。不要担心，很快你就会看到一切是如何联系在一起的。

### 使用动作变异我们的状态数据。

我们之前已经简要解释了状态概念并声明了我们的状态模型，
接下来我们需要导航到我们的`actions`文件，在这里我们将编写可以改变我们的状态数据的动作函数，在 hyperapp 中只有动作可以改变状态数据。

让我们继续写我们的第一个动作。在`src/actions/actions.js:`中添加此代码

```
 import { location } from "@hyperapp/router"

export const actions = {

  location: location.actions,

  GET_ALL_MOVIES: () => (state) => ({ 

    movie_list: state.movie_list = state.movies,  

  }),

} 
```

如果你读过 hyperapp 的基础知识，那么你就已经知道这段代码是做什么的了，但最好还是让我们解释一下；

我们像往常一样导入并注册我们的路由器 api，并创建一个函数`GET_ALL_MOVIES()`，它传递给我们的状态存储数据，它通过将假数据从`movies`状态复制到它来改变我们最初的空`movie_list`状态。不要担心，你很快就会明白为什么我们不使用`movies`状态。

**现在让我们在 action 对象中添加一些其他动作**来实现我们应用程序的功能特性。在 Hyperapp 中，你可以有任意多的动作；

#### `Add movie to cart`动作:

```
 ADD_MOVIE_TO_CART: (movie_id) => (state) => ({

    cart_item_count: state.cart_item_count += 1,

    cart:  state.cart.filter(movie => movie.id === movie_id).length>0
    ?
    Object.assign(state.cart, state.cart[state.cart.findIndex(obj =>
    obj.id === movie_id )].quantity ++ )
    : 
    state.cart.concat(state.movies.filter( movie => movie.id == movie_id).map(res =>
    ({ movie_title: res.title,
       price: res.price, 
       movie_poster: res.poster_path, 
       total: res.price,
       quantity: 1,
       id: res.id }) )),

    cart_item_total: state.cart.reduce( (acc, cur) => {
    return acc + cur.price * cur.quantity;

    }, 0),

  }), 
```

动作`ADD_MOVIE_TO_CART()`包含修改它们被赋予的状态属性值的函数。这些功能是:

`cart_item_count`函数通过每次将电影对象添加到状态`cart`数组中时将当前状态值加 1 来增加`cart_item_count`状态属性值。每次调用`ADD_MOVIE_TO_CART`动作时都会调用它。

`cart`函数将电影对象从我们的状态添加到状态`cart`数组中。因为动作可以访问状态，也可以从我们的组件传递有效载荷(数据)，所以我们使用 Javascript 的`.filter()`函数对我们的`movies`状态数据进行处理，从它的数组中返回电影对象，该对象的`id`与从电影组件传递的电影 id 相同，并返回一个布尔值，这样我们就可以检查它是否已经存在于数组中。

如果它存在，那么我们只需将电影的`quantity`属性增加`1`
，但是如果它不存在，我们使用它在`movies`状态数组中的 id 来定位电影，然后将其属性和一些新属性一起复制到`cart`状态数组中，以帮助我们创建一个`quantity`和一个`total`属性/值。

`cart_item_total`函数计算`cart`数组中电影的总价。

**注意:**当在 hyperapp 中改变状态数据时，我们使用 JavaScript 的`.filter(), .concat(), .map(), .reduce()`函数，因为它们是纯粹的函数，不修改数组，而是在操作后返回一个新的数组。

#### `Filter movies by price range and shipping`动作:

```
 FILTER_BY_PRICE: (event) => (state) => ({ 

    range_value: state.range_value = event.target.value,   

    movie_list: state.movie_list = state.movies.filter( movies => 
      state.switch_value ? 
      movies.price <=  state.range_value && movies.planet_shipping == true :
      movies.price <= state.range_value ),
  }),

  FILTER_BY_SHIPPING: (event) => (state) => ({

    switch_value: state.switch_value = event.target.checked, 

    movie_list: state.movie_list = state.movies.filter( movies => 
      state.switch_value ? 
      movies.price <=  state.range_value && movies.planet_shipping == true :
      movies.price <= state.range_value ),   
  }), 
```

这些动作由我们组件中的范围滑块/开关`html elements`调用，你可以看到 hyperapp 是如何在值更新/改变时快速呈现状态变化/更新 DOM 的。

我们将值作为事件数据从 range slider 或 switch 元素中传递，这取决于使用了哪一个。

请记住，我们为这些过滤操作单独创建了一个`movie_list`状态数组，即使我们使用纯函数进行操作，我们也不希望像这样使用动态数据修改`movies`状态数组，因为在合并后很难再次过滤它。

## 使用 hyperapp *视图*功能组件渲染我们的状态数据/执行动作。

使用 Hyperapp，我们可以创建 **2** 种组件(组件是返回虚拟节点的纯函数)。

[常规组件](https://github.com/hyperapp/hyperapp#components)是可以访问 hyperapp 状态和动作的组件。我喜欢把它们看作是我们操纵状态/动作的组件。

[惰性组件](https://github.com/hyperapp/hyperapp#lazy-components)是不能访问/不能改变状态和动作的组件。我喜欢把它们看作是`presentational`组件，我们只是把状态值作为属性传递给视图中的`styling`、`re-use`、`applying behaviours`等。

### 创建我们的组件。

#### `<App/>`分量。

我们要创建的第一个组件是`App.js`组件。这是一个惰性/容器组件，当用户访问我们的应用程序时，它将作为我们的根`/`路由组件呈现。它在创建时从我们的`actions` api 调用`GET_ALL_MOVIES()`动作。

**导航至** `src/views/containers/App.js`并添加该代码。

```
 import { h } from 'hyperapp'
import { Link, Route, location, Switch } from "@hyperapp/router"

import { NavBar } from '../../components/NavBar'
import { MovieCard }  from '../../components/MovieCard'

export const App = () => ( state, actions ) =>

<div oncreate= { () => actions.GET_ALL_MOVIES() } >

      <NavBar cart_count= {state.cart_item_count}/>  
<section class="section">
<div class="container">    
<div class="columns">
<div class="column  is-3">    

    <div class="box">
    <div class="content">   
    <b> Highest Price: ${state.range_value} </b>
    <input   
    type="range" 
    oninput = { (event) => actions.FILTER_BY_PRICE(event) }    
    onchange = { (event) => actions.FILTER_BY_PRICE(event) }                          
    class="slider is-fullwidth is-small is-circle" 
    step="0.1" min="0" max="150" 
    value = {state.range_value}  />
    </div> 
    <h1 class="title is-6 has-text-white"> Only show mars shipping movies</h1> 
    <div class="field">
    <input id="switchMovie"
     type="checkbox" 
     name="switchMovie" 
     class="switch is-success" 
     checked={state.switch_value}
     onchange= { (event) => actions.FILTER_BY_SHIPPING(event) } />
     <label for="switchMovie"></label> 
     </div> 
    </div> 
    </div>  
<div class="column  is-9">    
<div class="columns is-multiline is-mobile">

  { state.movie_list &&
    state.movie_list.map(
   ({ id, title, poster_path, price, vote_average, planet_shipping, overview, release_date }) =>

<div className="column  is-half-mobile is-one-third-tablet is-one-third-desktop is-one-quarter-widescreen is-one-quarter-fullhd">

      <MovieCard movie_id = {id}
                 title = {title } 
                 poster = {poster_path }
                 price = {price}
                 rating = {vote_average}
                 planet_shipping = { planet_shipping } 
                 plot = { overview }
                 release_date = {release_date }  />  
          </div>  
                  )

  } 

</div> </div> </div> </div> </section> 
     </div> 
```

这里，我们导入 hyperapp `h`函数，将我们在`JSX`中编写的组件转换成虚拟 Dom 节点。

我们还从已安装的`hyperapp-router`包中导入 [hyperapp 路由 api 函数](https://github.com/hyperapp/router)。

然后我们导入两个`regular/presentational components`,接下来我们将创建它们用于样式等目的。(这样做通常是很好的实践，它鼓励代码重用)。

然后我们创建一个创建 App.js 组件的函数，并添加一个`hyperapp life-cycle`事件，当在 DOM 中创建 App.js 组件时，该事件从我们的 hyperapp 动作调用我们最初创建的`GET_ALL_MOVIES()`动作。结帐 [hyperapp 生命周期事件](https://github.com/hyperapp/hyperapp#lifecycle-events)

然后，我们使用 jsx 和 javascript 中的`&&`操作符创建一个函数来检查状态存储是否有数据，并对 movie_list 数组中的每一项执行 Javascript 的`.map()`函数，在本例中，这些项是我们伪造的收视率最高的电影数据。还记得我们不久前讨论过创建一个表示组件，在`.map()`函数中，我们将数据从返回的对象传递到我们称之为`<MovieCard/>`的组件中，以将每个对象项目作为一个独立的组件。

您还可以看到我们的`range slider`和`switch`元素，以及它们如何访问各自的状态数据属性，以及它们如何调用`actions`并将`payload`事件数据传递给它们。注意附加到每个元素的 javascript DOM 事件侦听器的种类。

由于牛逼的布尔玛，剩下的只是响应/网格样式。

#### `<MovieCard/>`分量。

**导航到** `src/components`并创建一个`MovieCard.js`文件并添加这段代码，这将是懒惰组件`App.js`的一个普通组件子组件。

```
 import { h } from 'hyperapp'
import { Enter } from "@hyperapp/transitions"
import { Link, Route, location, Switch } from "@hyperapp/router"

export const MovieCard = ({ movie_id, title, poster, price, rating, planet_shipping, plot }) => (

<div>

<Link to={`/details/${movie_id}`} >

 <Enter time={200} easing="ease-in-out" 
        css={{opacity: "0", transform: "scale(1,1)"}}>

    <div class="box">
    <div class="media">

    <div class="media-content">

      <div class="content">
      <span class="badge is-badge-warning is-badge-large" data-badge={rating}></span>
      </div> 
       <div class="content">
       <p class="title is-6 has-text-light"> {title} </p>
       </div> 
        <div class="content">
       <div class="tags has-addons">
         { planet_shipping &&
         <span class="tag is-success">ships to mars</span>  }         </div>
        </div> 

       <div class="content">
       <figure class="image">
       <img src={`http://${poster}`}/>
       </figure>
       </div> 

      <nav class="level is-mobile">

            <span class="level-item"> 
            <b> ${price} </b>
             </span> 

      </nav>
      </div> 

    </div>
    </div> 

    </Enter>
    </Link> </div>
    ) 
```

这里我们导入了和以前一样的包和一个来自`hyperapp-transitions`包的新的`{ Enter }`组件，我们将用它来为我们的`moviecard`组件创建一个漂亮、平滑的动画。

此外，我们使用 hyperapp 路由器中的`<Link/>`组件，我们将使用它来打开一个模态路由，用户可以在这里看到所选/点击的电影卡的更多细节。

需要注意的最重要的一点是，我们将电影数据作为属性从我们的`App.js`惰性组件传递到我们的普通组件，其余的代码只是覆盖了来自我们属性的数据的一些样式，如`{title}`是电影标题，`{poster}`是电影海报 url，`{price}`是电影价格，`{planet_shipping}`是布尔值 true/false，我们将使用它来检查一部电影是否被运送到火星。

您可以看到在我们评估了`{planet_shipping}`属性之后，我们如何使用`&&`操作符应用一些逻辑来有条件地呈现一个元素。如果你愿意，你可以使用`if/else`或者甚至`?`操作符，但是我更喜欢`&&`操作符，因为它最适合我做这种简单的评估。

剩下的代码只是样式。

#### `<ViewMovieDetails/>`分量。

现在，我们需要创建一个容器组件`<ViewMovieDetails/>`，当用户选择一部电影时，它将由`hyperapp-router`呈现，显示关于这部电影的更多信息。它的路线路径是`/details/:movie_id`。请注意路由路径后面的特殊术语`:movie_id`，它只是一个参数，路由器包使用这个参数从我们在前面的组件中选择的电影对象中获取`id`属性，并将其作为 url 中的路由参数传递给这个组件使用。

在`src/views/containers/`中创建一个`ViewMovieDetails.js`文件并添加代码

```
 import { h } from 'hyperapp';

import { MovieDetails  } from '../../components/MovieDetails'

export const ViewMovieDetails = ({match}) => (state, actions) => (

<div> 

{ 

    state.
    movie_list.
    filter( movie => movie.id == match.params.movie_id ).
    map( ({ id, title, overview, poster_path, vote_average, release_date , price }) => 

    <MovieDetails 
        cart_count={state.cart_item_count}
        id={id}
        title={title}
        plot={overview}
        poster={poster_path}
        price={price}
        rating={vote_average}
        release_date={release_date}
        addAction={  () => actions.ADD_MOVIE_TO_CART(id) }
        /> 
    )
  } 
</div> ) 
```

像往常一样，我们导入名为`<MovieDetail/>`的 pnormal 组件作为子组件(我们将很快创建它),然后我们过滤`movie_list`状态数组以找到一个电影，其`id`等于从路由传递的 id，然后将`.map()`函数应用于结果数据作为`<MovieDetail/>`组件，然后传递它的必要属性，其中还包括动作`ADD_MOVIE_TO_CART()`和状态值`cart_item_count`。

#### `<MovieDetails/>`分量。

现在让我们创建`<ViewMovieDetails/>`惰性组件的子组件。

在`src/components/`文件夹中创建一个文件`MovieDetails.js`并添加代码:

```
 import { h } from 'hyperapp'

export const MovieDetails = ({ cart_count, id, title, addAction, poster, price, rating, release_date, plot }) => (
    <div>
          <div class="modal is-active">
          <div class="modal-background"></div>
          <div class="modal-card">
          <header class="modal-card-head">
          <p class="modal-card-title">{title} </p> 
          <button class="delete" aria-label="close"  onclick= { () => { window.history.back() }}></button>
          </header>
          <section class="modal-card-body">

          <div class="columns">

          <div class="column">

         <figure class="media-left">

         <img src={`http://${poster}`} />      
         </figure> 
          </div> 
          <div class="column">
          <p class="title is-5 has-text-white"> Plot: </p>
          <p class="title is-6 has-text-white"> {plot} </p> 
          <p class="title is-6 has-text-white">Release date {release_date} </p>  
          <span class="tag is-warning">{rating}</span> 

          </div>
          </div> 
    </section>
    <footer class="modal-card-foot">

    <a class="button is-success" onclick={ addAction }> 
    <b>  Add to Cart  ${price}  </b>
    </a>
    <b> {cart_count} Items in cart</b>  

    </footer>
  </div> </div> 
    </div>
        ) 
```

在这里，我们从`<ViewMovieDetails/>`组件接收属性`cart_count`，`price` e.t.c，然后给它添加一些样式。请记住，当按钮 onclick 事件被触发为`{addAction}`并且购物车中的商品总数为`{cart_count}`时，我们传递了负责将选定的电影添加到购物车的动作。

#### `<ViewCart/>`分量。

现在让我们创建一个惰性组件，它将在用户访问`/cart` route 时呈现。在这个组件中，我们将显示用户添加到购物车中的电影。

在`src/views/containers/`中添加一个文件`ViewCart.js`并添加这段代码；

```
 import { h } from 'hyperapp';

import { CartItems } from '../../components/CartItems'
import { NavBar } from '../../components/NavBar'

export const ViewCart = ({match}) => ( state, actions ) => (

<div>

    <NavBar cart_count= {state.cart_item_count}/>  
<section class="section">
<div class="container">    

             <p class="title is-3 has-text-white"> Cart Items </p> 
{
    state.cart.filter(res => res.id )
    .map( res => 

        <CartItems movie_id={res.id}
        title={res.movie_title}
        price={res.price}
        quantity= {res.quantity}
        total= {res.quantity * res.price }
        poster= {res.movie_poster}
        /> 

    )

}
    { <p class="title is-5 has-text-white"> total price: ${state.cart_item_total}  </p> } 

</div> </section> </div> 
) 
```

在这段代码中，我们导入并使用了`<NavBar/>`,还映射了我们所在州的购物车数组中的项目，并将结果传递给它的子组件`<CartItems/>.`

#### `<CartItems/>`分量。

现在让我们创建`<CartItems/>`组件。

在`src/components`中创建一个文件`CartItems.js`并添加代码:

```
 import { h } from 'hyperapp'

export const CartItems = ({ movie_id, title, price, quantity, total, poster }) => (

    <div>

         <article class="media">

         <figure class="media-left">
         <p class="image is-64x64">
         <img src={`http://${poster}`}/>      
         </p>
         </figure> 
         <div class="media-content">
         <div class="content">

            <p class="title is-5 has-text-white"> {title} </p> 
            <small> ${price} x </small>             <b>{quantity} copies</b> 
            <p/>

            <small> Total price: </small> <b> ${total}</b>

            <hr/>

         </div>
         </div> 
          </article> 
    </div>
        ) 
```

我假设这个组件是自解释的，它只是对从它的父组件`<ViewCart/>`传递给它的属性进行样式化，并对它应用一些行为。

#### `<NavBar/>`分量。

最后是导航条组件。

导航到`src/components`并创建一个`NavBar.js`文件，然后添加这段代码，这样我们就可以创建一个漂亮的导航栏来保存到其他组件的链接，并从使用它的任何父惰性组件接收购物车的商品计数数据。

```
 import { h } from 'hyperapp'

import { Link, Route, location, Switch } from "@hyperapp/router"

export const NavBar =  ({cart_count}) => (
<nav class="navbar is-primary has-shadows" role="navigation" aria-label="main navigation">
<div class="navbar-brand">

<a class="navbar-item" href=""> 
<g transform="translate(-37.57 -49.048)"><rect width="132.292" height="90.714" x="38.554" y="90.625" ry="9.719" fill="#edc905" paint-order="stroke fill markers"/><rect width="5.292" height="61.988" x="12.631" y="72.602" ry="1.911" transform="rotate(-34.65)" fill="#edc905" paint-order="stroke fill markers"/><rect transform="rotate(-145.35)" ry="1.911" y="-39.86" x="-154.078" height="61.988" width="5.292" fill="#edc905" paint-order="stroke fill markers"/><ellipse cx="148.923" cy="115.949" rx="7.56" ry="7.182" paint-order="stroke fill markers"/></g>
</a> 
<Link to="/cart" class="navbar-item">
<span class="badge is-badge-danger is-badge-medium" data-badge={cart_count}>

<path d="M.34 0a.5.5 0 0 0 .16 1h1.5l.09.25.41 1.25.41 1.25c.04.13.21.25.34.25h3.5c.14 0 .3-.12.34-.25l.81-2.5c.04-.13-.02-.25-.16-.25h-4.44l-.38-.72a.5.5 0 0 0-.44-.28h-2a.5.5 0 0 0-.09 0 .5.5 0 0 0-.06 0zm3.16 5c-.28 0-.5.22-.5.5s.22.5.5.5.5-.22.5-.5-.22-.5-.5-.5zm3 0c-.28 0-.5.22-.5.5s.22.5.5.5.5-.22.5-.5-.22-.5-.5-.5z"
transform="translate(0 1)" />
 </span> </Link> </div> 
</nav> ) 
```

我们创建了一个带有`cart_count`属性的导航栏组件来显示购物车中的商品数量。

### 将我们的惰性组件注册到它们各自的路由路径。

现在我们已经为我们的应用程序创建了所有必要的组件，接下来我们需要做的是将父/惰性组件注册为路由组件，这样 hyperapp `View`函数就可以返回它们各自的虚拟节点，并观察状态变化，也就是呈现 UI。我们使用`hyperapp-router's <Switch/>`组件来声明多个`route paths`和它们各自的组件，以便在访问路由路径时呈现。

```
 import { h } from 'hyperapp'
import { Link, Route,location, Switch } from "@hyperapp/router"

import { App } from './views/containers/App'
import { ViewMovieDetails } from './views/containers/ViewMovieDetails'
import { ViewCart } from './views/containers/ViewCart'

export const view = ( state, actions ) => 

<div>

<Switch>    
    <Route path="/" render={ App } />   
    <Route path="/cart" render={ ViewCart } />         
    <Route path={ "/details/:movie_id"} render={ ViewMovieDetails} />
  </Switch> 
</div> 
```

[查阅 hyperapp-router 文档，了解有关 hyperapp 中路由的更多信息](https://github.com/hyperapp/router)，

### 连接一切。

现在我们需要将我们的整个应用程序挂载到 DOM，hyperapp 要求我们在一个`index.js`文件中这样做，这个文件作为`rollup`或任何其他模块捆绑器的入口文件，将我们的整个应用程序代码捆绑到一个 javascript 文件中。

让我们将这段代码添加到`/index.js`文件中；

```
 import { h , app } from 'hyperapp'

import {  location } from "@hyperapp/router"

import { state }  from './state/state'
import  { actions } from './actions/actions'
import { view }   from './routes'

import  './styles/app.scss'

const main = app(state, actions, view, document.querySelector('.hyperapp-root'))

const unsubscribe = location.subscribe(main.location) 
```

在这里，我们导入所需的 hyperapp api 函数以及我们的`state, actions`和`view`文件，然后使用 hyperapp 的`app`函数将它们挂载到 DOM。我们还导入了我们的 sass 文件，这样当我们的模块捆绑包处理`index.js`文件时，它也可以被编译。处理过程包括使用巴别塔编译我们的 JSX/ES6 语法，，编译 sass 到 css e.t.c

# 就是这样！

我们已经完成了本教程的项目，我想你已经在你的浏览器中通过 **localhost:8080** 查看了我们的最终输出。

您可以运行生产构建并部署到 github pages 或任何您想要的静态文件服务器，这样您就可以与其他人共享。如果这是你的第一次，有关于这方面的教程。

非常感谢您的阅读，我非常感兴趣的任何意见，以纠正或报告错误，或建议任何会使本教程更好，因为我期待改进。可以在 github 上访问[项目回购](https://github.com/ThobyV/Hyperapp-shopping-cart-demo)。请随意打开问题！