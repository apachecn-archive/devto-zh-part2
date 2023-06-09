# 带有 Vue.js 的数据表

> 原文：<https://dev.to/raymondcamden/datalists-with-vuejs-3go6>

这不一定是一个非常令人兴奋的帖子，但几天前有人问我关于将 Vue.js 与 [datalist](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/datalist) 标签集成的问题。datalist 标签是我最喜欢的 HTML 标签之一，也是我过去曾[在博客上写过几次](https://www.raymondcamden.com/search/?q=datalist)的东西。如果您不熟悉它，它基本上为输入标签提供了“自动建议”风格的体验。

HTML 非常简单。下面是我在上面链接的 MDN 文章中使用的例子:

```
<label for="myBrowser">Choose a browser from this list:</label>
<input list="browsers" id="myBrowser" name="myBrowser" />
<datalist id="browsers">
  <option value="Chrome">
  <option value="Firefox">
  <option value="Internet Explorer">
  <option value="Opera">
  <option value="Safari">
  <option value="Microsoft Edge">
</datalist> 
```

Enter fullscreen mode Exit fullscreen mode

基本上——你创建一个`<datalist>`元素并提供选项。然后获取输入并添加`list="id of the list"`属性。现在，当用户输入时，他们将根据列表和他们输入的内容获得建议。它得到了很好的支持(当然，除了 Safari 和 Mobile Safari 之外，基本上所有人都支持它)，并且很优雅地失败了(用户仍然可以键入他们想要的任何东西)。你会怎么把这个功能和 Vue.js 结合起来？让我们看一个静态的例子。首先，HTML:

```
<div id="app">
  <input type="text" v-model="film" list="films">
  <datalist id="films">
    <option v-for="film in films">{{film}}</option>
  </datalist>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到输入字段和列表。`option`标签被绑定到一个名为`films`的变量。现在让我们看看 JavaScript:

```
const app = new Vue({
  el:'#app',
  data() {
    return {
      film:'',
      films:[
        "A Throne Too Far",
        "The Cat Wasn't Invited",
        "You Only Meow Once",
        "Catless in Seattle"
        ]
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

不太令人兴奋，但它工作得相当好。你可以在下面测试一下:

[https://codepen.io/twhite96/embed/PRvZyG?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/PRvZyG?height=600&default-tab=result&embed-version=2)

你如何让它充满活力？很简单——只需改变数据的生成方式。这里有一个例子:

```
const app = new Vue({
  el:'#app',
  data() {
    return {
      film:'',
      films:[]
    }
  },
  created() {
    fetch('https://swapi.co/api/films/')
    .then(res => res.json())
    .then(res => {
      this.films = res.results.map(f => {
        return f.title;
      })
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我所做的就是添加一个`created`事件处理程序，并点击[星球大战 API](https://swapi.co) 获取我的数据。您可以测试以下结果:

[https://codepen.io/twhite96/embed/KoYrrg?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/KoYrrg?height=600&default-tab=result&embed-version=2)

我可能有偏见——但在 Vue 一切都更好。