# 使用 Vue 无法实现的功能

> 原文：<https://dev.to/winnercrespo/things-that-wont-work-using-vue-384e>

在使用 Vue 一段时间后，我发现了一些问题，后来我才知道这些问题实际上是他们在文档中警告我们的。

我把这些问题列了一个清单，这样你也可以知道。让我们来描述一下每一个，看看我们有什么选择来解决它们。

## 阵列变化

假设我们有这个:

```
const vm = new Vue({
  data: {
    titles: ['Ready Player One', 'The Power of Less', 'The 10x rule']
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

在尝试对`titles`进行更改时，我们可能会遇到两个不同的问题:

*   使用索引设置项目:`vm.titles[index] = newTitle`
*   修改长度:`vm.titles.length = length`

您可以使用`Vue.set` :
代替直接使用索引设置项目

```
Vue.set(vm.titles, index, newTitle) 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是使用`splice` :

```
vm.titles.splice(index, 1, newTitle) 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，要修改长度，你也可以使用`splice`:

```
vm.titles.splice(length) 
```

Enter fullscreen mode Exit fullscreen mode

## 给对象添加属性

具有以下目标:

```
const vm = new Vue({
  data: {
    top: {
      bestMovie: 'Avengers: Infinity War'
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们可能会尝试添加一个新的属性，做下一步:

```
vm.top.bestShow = 'Breaking Bad' 
```

Enter fullscreen mode Exit fullscreen mode

但是`bestShow`将不会是被动的，因为 Vue 在初始化时添加了被动功能。这意味着属性必须在`data`对象中才能起作用。

我们可以再次使用`Vue.set`来完成这个任务:

```
Vue.set(vm.top, 'bestShow', 'Breaking Bad') 
```

Enter fullscreen mode Exit fullscreen mode

虽然，如果你需要添加几个属性，也许创建一个新的对象来组合它们会更好:

```
vm.top = Object.assign({}, vm.top, { bestShow: 'Breaking Bad', bestBook: 'Ready Player One' }) 
```

Enter fullscreen mode Exit fullscreen mode

## 在组件安装前使用`$refs`

假设我们想在组件上集中一个输入。我们可以使用`ref`属性来表示:

```
<input ref="input" /> 
```

Enter fullscreen mode Exit fullscreen mode

然后:

```
methods: {
  focusInput: function () {
    this.$refs.input.focus()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想在组件一创建好就关注它呢？你会说我们可以使用`created`钩子:

```
created() {
  this.$refs.input.focus()
} 
```

Enter fullscreen mode Exit fullscreen mode

但实际上，这会产生一个错误，因为`$refs`是在组件安装后填充的。因此，我们应该使用`mounted`钩子，因为它已经被填充了:

```
mounted() {
  this.$refs.input.focus()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 切换相似元素

当在元素上使用`v-if`时，Vue 有时会重用具有相同标签名的元素。正如您在下一个示例中看到的，当按下切换按钮时，input 元素被重用。

要注意到这一点，您可以在输入中写一些东西，然后按下按钮(值不会改变),或者您可以使用 dev 工具，看到元素没有被替换:

[https://codepen.io/wistcc/embed/OwgbMg?height=600&default-tab=result&embed-version=2](https://codepen.io/wistcc/embed/OwgbMg?height=600&default-tab=result&embed-version=2)
[![](img/50543f33525f8e202e99f6dfb6e1ad64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KV2IsnIX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://winnercrespo.com/asseimg/2018-6-21-things-that-wont-work-using-vue-1.gif)

要解决这个问题，我们只需要给它们添加一个键，这样 Vue 就知道它们是不同的元素:

[https://codepen.io/wistcc/embed/PBjbNm?height=600&default-tab=result&embed-version=2](https://codepen.io/wistcc/embed/PBjbNm?height=600&default-tab=result&embed-version=2)
[![](img/432834297165abc048c1cf0cad2c6dd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bf1mn2Z1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://winnercrespo.com/asseimg/2018-6-21-things-that-wont-work-using-vue-2.gif)

现在我们可以看到，每次单击按钮时，元素都会被替换。

你有没有注意到其他在 Vue 上不起作用的东西？请在评论中分享。