# VueJs 中的组件

> 原文：<https://dev.to/faradayyg/components-in-vuejs-j5g>

组件使我们的代码可重用，并使我们能够坚持 D.R.Y 的软件开发原则(不要重复自己)。组件是扩展基本 HTML 标记行为的代码块，可以反复用于不同的目的。这意味着 Vue 中的组件看起来像基本的 HTML 元素，但它们更具可配置性，因此可以执行比普通 HTML 元素更多的功能。组件也可以包含其他组件，这使得我们的前端非常健壮，非常模块化。

现代 web 应用程序由许多部分组成，跟踪所有移动部分的最佳方式是将它们抽象成不同的小部分(组件)，使它们易于构建、使用和维护。所以最后，你可能会得到一整页类似这样的代码，它执行很多功能:

```
 <html>
  <head>
    <script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.js'></script>
  </head>

  <div id='app'>
    <app-header :links="links"></app-header>
    <app-sidebar :items="items"></app-sidebar>
    <app-body></app-body>
    <app-footer></app-footer>
  </div>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

你会同意我的观点，作为一名维护人员，像这样的代码非常简洁、直截了当，并且不需要花太多时间就能弄清楚发生了什么以及哪个部分做了什么。

Vue 中的组件可以通过两种方式创建，它们可以在一个单独的文件中创建，然后使用 Es6 `import`语句导入。或者它们可以在基本 JavaScript 文件中注册并直接使用。

出于本文的目的，我们将创建一个基本组件，它接收一个用户对象，输出一个列表，并在每个用户被单击时提醒用户详细信息。
这样，我们将展示:

1.  创建组件，
2.  通过 Props 向组件传递数据，
3.  列表呈现，
4.  从子组件发出事件，
5.  侦听父组件上的事件，以及
6.  处理发出的事件

如果你是 Vue 新手，可以查看[官方文档](https://vuejs.org/v2/guide/)开始使用。

如果你想跳到已完成的项目，有一个[代码笔](https://codepen.io/faradayyg/pen/gKwpqQ)存放已完成的项目。

## 设置

有两种方法可以设置您的 Vue 项目。

1.  使用 Webpack 构建工具或
2.  通过 Vue CDN 使用 Vue。

让我们探索如何使用 Vue CDN 在 JavaScript 文件中定义组件。

首先，创建一个普通的 HTML 文件，并包含来自 CDN 的 Vue JavaScript 文件。

```
<script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.js'></script> 
```

Enter fullscreen mode Exit fullscreen mode

这确保 Vue 在项目中可用。接下来，我们在 body 标签中添加一个`<script>`标签。这是我们的代码将存在的地方。

到目前为止，您的项目应该是这样的:

```
 <!DOCTYPE html>
<html>
<head>
    
    <script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.js'></script>
</head>
<body>   

    <script type="text/javascript">
      //Our Codes, Here 
    </script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们现在准备开始编写代码。
为了确保 Vue Js 安装正确，让我们创建一个“Hello Vue”应用程序。
在 HTML 文件的 body 标签中添加一个 div 标签，给它一个 id“app”。
在`div`标签中，复制并粘贴这个:`{{message}}`双花括号中的文本表示 Vue 中的一个变量。

在您的脚本标记中，复制并粘贴以下内容，我们稍后将探讨这些内容的含义。

```
 let app = new Vue({
    el : "#app",
    data(){
      return{
        message: 'Hello Vue'
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们在上面所做的是，

1.  实例化一个新的 Vue 对象，告诉它应该使用的 HTML 元素的 ID 是`app`
2.  然后我们提供了一个数据对象，它返回`message`
3.  最后，我们将上面数据对象中定义的消息变量打印到 HTML 中，输入:`{{message}}`双花括号表示它们的内容是一个变量。

现在，我们的整个代码应该是这样的:

```
 <!DOCTYPE html>
<html>
<head>
    
    <script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.js'></script>
</head>
<body>

    <div id="app">
            {{message}}
    </div>
    <script type="text/javascript">
       let app = new Vue({
           el : "#app",
            data(){
              return{
                 message: 'Hello Vue'
              }
            }
       });

    </script>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在浏览器中运行它，我们应该得到一个“Hello Vue”的输出。现在我们已经确认 Vue 设置正确，让我们开始定义 Vue 中的组件。

## 定义我们的组件。

如前所述，组件可以作为一个单独的文件创建，或者直接在主 JavaScript 文件中创建。对于本教程，我们将直接在 JavaScript 文件中定义组件。

使用`Vue.component(` `'` `tag-name` `'` `, options)`命令注册组件，其中`tag-name`是您希望组件使用的名称，`options`是定义组件行为的对象。这使得组件在文件中全局可用，因此现在可以在不同的情况下使用。

让我们从定义一个除了在屏幕上显示一条消息之外什么也不做的虚拟组件开始。姑且称之为`user-list`。接下来，创建一个新的 HTML 文件，或者修改“hello vue”文件，如下所示:

```
 <!DOCTYPE html>
<html>
<head>
    
    <script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.js'></script>
</head>
<body>

    <div id="app">
      <user-list></user-list>
    </div>

    <script type="text/javascript">

      let userList = Vue.component('user-list', {
      template : '<div>I am a component</div>'

      });

       let app = new Vue({
           el : "#app"

       });

    </script>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们所做的是创建一个 Vue 组件，命名为“user-list ”,然后在我们的 HTML 中，我们像普通的 HTML 标签一样使用了`user-list`。这是您将组件输出到前端的方式。
你可以在 Vue 组件定义中看到`template`属性，它指定了组件挂载时输出的 HTML 标签。注意，一个 Vue 组件只能有一个根元素。
这就是在 Vue 中创建基本组件的全部内容。

[![Our Component](../Images/ccebd88dd2470669c5cfd93551ed9188.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YjsWYvll--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ddbm6rq61/image/upload/v1528310225/1.png)

尽管这样做是可行的，但并不是很有用，因为它没有展示组件的强大功能和可重用性。让我们通过定义道具来进一步扩展这个组件。

## 道具和组件

每个 Vue 组件都在自己的范围内，不应该从外部访问数据。
Props 为我们提供了一种将数据从父组件(外部)传递到子组件的方法。在我们的例子中，我们将把数据从`app`传递到`userList`组件。但是在我们这样做之前，我们必须明确指定我们在`user-list`组件中期望的道具。给`user-list`组件添加另一个属性，称之为`props`。这将是我们期望传递给`user-list`组件的所有道具的数组。让道具属性的内容为`['users']`当我们这样做的时候，让我们修改`template`属性，并删除`div`的所有内容，用`{{users}}`替换它们。

同样在主 HTML 文件中，让我们向`<user-list>`标签添加一个名为“users”的新属性，并将值设置为`users=` `"` `list of users` `"`。

现在，我们的应用程序的源代码应该看起来有点像这样:

```
 <!DOCTYPE html>
<html>
<head>
    
    <script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.js'></script>
</head>
<body>

    <div id="app">
      <user-list users="list of users"></user-list>
    </div>

    <script type="text/javascript">

      let userList = Vue.component('userList', {
      template : '<div>{{users}}</div>',
      props: ['users']

      });

       let app = new Vue({
           el : "#app"

       });

    </script>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，输出应该是这样的:
[![Displaying data passed as Prop](../Images/ab374114ead5d1fe2f44b9b4e72d58ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e36HnyZJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ddbm6rq61/image/upload/v1528310222/2.png)

正如我们所看到的，我们的组件变得更加智能，现在可以使用`users`属性将数据从父组件传递给它。

这并不意味着只有字符串可以作为道具传递，变量也可以使用`v-bind` Vue 属性传递。让我们把事情延伸得更远一些。在我们的主 Vue 应用程序中，我们将定义一个数据属性，并传入 Vue 组件将使用的变量。数据属性现在应该是:

```
 data(){
            return{
              allUsers : [
                {
                  name : 'John Doe',
                  about : 'Really nice guy'
                },
                {
                  name : 'Jane Dean',
                  about: 'Loves eggs'
                },
                {
                  name : 'Clark Kent',
                  about: 'Not your everyday reporter'
                }
              ]
            }
           } 
```

Enter fullscreen mode Exit fullscreen mode

这基本上只是返回了一个由三个对象组成的数组，每个对象有两个键，`name`和`about`。
为了将我们新定义的用户列表传递给组件，我们只需将`v-bind:users`属性添加到组件中，并将数组的名称传递给它，于是我们有了`<user-list v-bind:users=` `"` `allUsers` `"` `></user-list>`。前缀`v-bind:`告诉 Vue 我们想要动态地将`users`属性绑定到一个变量，而不是直接传入一个字符串。

现在，我们把这个作为我们的应用程序源代码:

```
 <!DOCTYPE html>
<html>
<head>
    
    <script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.js'></script>
</head>
<body>

    <div id="app">
      <user-list v-bind:users="allUsers"></user-list>
    </div>

    <script type="text/javascript">

      let userList = Vue.component('userList', {
      template : '<div>{{users}}</div>',
      props: ['users']

      });

       let app = new Vue({
           el : "#app",

           data(){
            return{
              allUsers : [
                {
                  name : 'John Doe',
                  about : 'Really nice guy'
                },
                {
                  name : 'Jane Dean',
                  about: 'Loves eggs'
                },
                {
                  name : 'Clark Kent',
                  about: 'Not your everyday reporter'
                }
              ]
            }
           }

       });

    </script>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

还记得我们之前说过，我们希望我们的组件能够列出传递给它的所有用户。为此，我们需要使用`v-for`指令来执行列表呈现。指令用于呈现基于数组的项目列表。
语法是这样的:

```
<li v-for="item in items"></li> 
```

Enter fullscreen mode Exit fullscreen mode

这里，items 是一个数组，item 是被迭代的当前数组项的别名。有了列表呈现的知识，让我们扩展我们的用户列表组件来迭代所有用户。为此，我们将模板替换为:

```
 template : `
        <ul>
          <li v-for="user in users">
            {{user.name}}
          </li>
        </ul>
`, 
```

Enter fullscreen mode Exit fullscreen mode

如果您不熟悉，反勾号在现代 JavaScript 中被称为模板文字，它们允许我们使用多行语句，如上图所示。你可以在这里了解更多关于模板文字的信息

我们上面所做的是定义一个基本的`ul`元素，然后使用`v-for`列表呈现指令遍历并动态创建元素中的所有`<li>`标签。如果您运行我们当前拥有的代码，这应该是您的输出:

[![Looping through the user object](../Images/eb81f31785e7e584ce60c4a087643813.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dkiA5VmE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ddbm6rq61/image/upload/v1528310219/3.png)

## 处理点击事件

因为我们希望我们的组件是可重用的，所以我们不会处理组件内部的 click 事件，相反，我们会将事件返回给父组件，父组件将使用有效负载中传递的来执行它喜欢的任何操作。这样做的好处是，我们可以将同一个组件用于许多不同的目的。

我们将通过让`user-list`组件在一个项目被点击时发出一个事件来做到这一点，并且我们将在父组件上处理这个事件。

让我们给`<li>`元素添加一个 onclick 监听器，我们在 Vue 中通过添加`@click`属性来完成。这个点击事件将调用一个内部方法，并将用户的`about`属性传递给该方法。

```
 <li v-for="user in users" @click="emitClicked(user.about)">
  {{user.name}}
</li> 
```

Enter fullscreen mode Exit fullscreen mode

您可以在上面看到，有一个方法传递给了 click 处理程序，称为 emitClicked 方法，我们将通过向 Vue 组件添加 methods 属性来定义此方法。

```
 methods : {
  emitClicked(data){
      this.$emit('item-clicked',data)
} 
```

Enter fullscreen mode Exit fullscreen mode

此方法发出一个带有有效负载的事件，父级可以侦听该事件并将其用于操作。

## 监听事件

监听父组件中事件的最简单方法是使用`v-on`属性。还记得在子组件中，我们发出了一个`item-clicked`事件，所以我们可以通过将`v-on:item-clicked`属性添加到`<user-list>` HTML 标签中来方便地监听事件。

```
<user-list v-bind:users="allUsers" v-on:item-clicked="alertData"></user-list> 
```

Enter fullscreen mode Exit fullscreen mode

从上面的代码中，我们可以看到有一个名为`alertData`的新方法，这个方法处理子组件发出事件时传递的有效载荷(数据)。

我们将通过添加 methods 属性在主组件中定义`alertData`方法。

```
 methods: 
{
  alertData(data)
  {
    alert(data)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法只是使用本机 alert 方法来显示从子组件传递过来的数据。

现在我们的整个代码应该是这样的:

```
 <!DOCTYPE html>
    <html>
    <head>
        
        <script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.js'></script>
    </head>
    <body>

        <div id="app">
          <user-list v-bind:users="allUsers" v-on:item-clicked="alertData"></user-list>
        </div>

        <script type="text/javascript">

          let userList = Vue.component('userList', {
          template : `
            <ul>
              <li v-for="user in users" @click="emitClicked(user.about)">
                {{user.name}}
              </li>
            </ul>
          `,

          props: ['users'],

          methods : {
            emitClicked(data){

              this.$emit('item-clicked',data)

            }
          }

          });

           let app = new Vue({
               el : "#app",

               data(){
                return{
                  allUsers : [
                    {
                      name : 'John Doe',
                      about : 'Really nice guy'
                    },
                    {
                      name : 'Jane Dean',
                      about: 'Loves eggs'
                    },
                    {
                      name : 'Clark Kent',
                      about: 'Not your everyday reporter'
                    }
                  ]
                }
               },

               methods: 
               {
                  alertData(data)
                  {
                    alert(data)
                  }
               }

           });

        </script>

    </body>
    </html> 
```

Enter fullscreen mode Exit fullscreen mode

[![Our Final Application](../Images/1b3ad3849ca50d035728553742e44b0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EEO27B1o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ddbm6rq61/image/upload/v1528310221/4.png)

该组件的可重用性在于,`v-on:item-clicked`可以接受不同的方法，并产生不同的输出，因此,`user-list`组件可以在整个应用程序中分别重用。

### 结论

组件在分离我们的前端应用程序的关注点和将它分解成更小的逻辑块时非常有用。根据经验，组件应该执行单一的功能，以实现最大的可重用性。在本文中，我们已经能够探索如何创建一个组件，并在组件和它的父组件之间传递数据。