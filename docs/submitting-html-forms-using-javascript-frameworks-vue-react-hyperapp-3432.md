# 使用 JavaScript 框架(Vue、React、Hyperapp)提交 HTML 表单

> 原文：<https://dev.to/bnevilleoneill/submitting-html-forms-using-javascript-frameworks-vue-react-hyperapp-3432>

[![](../Images/03dae840b37103e99058ab0bbf35a4f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A8RyhUTj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/512/1%2A8CbHmOJSxE53VvpHJdPulw.png)

HTML 表单可以在提交表单和等待响应时声明性地发送一个 [HTTP](https://developer.mozilla.org/en-US/docs/HTTP) 请求。但是，在得到结果之前，您必须等待整个页面重新加载，这通常不是最佳的用户体验。

表单还可以准备一个 HTTP 请求，通过 JavaScript 发送，这有助于获得更好的用户体验。本文使用三种不同的框架来探索实现这一点的方法:Vue、React 和 Hyperapp。

### 使用 Vue 提交表单

Vue 是一个用于构建用户界面的渐进式框架。与其他整体框架不同，Vue 从一开始就被设计成可增量采用的。想了解更多关于 Vue 的信息，可以访问官方主页[这里](https://vuejs.org/)。

首先，让我们定义我们的 HTML 结构。创建一个名为 vue.html
的文件

```
<link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<div class="container" id="app">
    <div class="row">
<div class="col-md-4">
        <div class="panel">
        <h4 class="heading"><strong>Quick </strong> Contact <span></span></h4>
        <div class="form">
            <input type="text" required="" placeholder="Please input your Name" value="" v-model="form.name" class="form-control">
            <input type="text" required="" placeholder="Please input your mobile No" value="" v-model="form.mob" class="form-control">
            <input type="text" required="" placeholder="Please input your Email" value="" v-model="form.email" class="form-control">
            <textarea placeholder="Your Message" v-model="form.mess"  class="form-control"></textarea>
            <input type="submit" value="submit" name="submit" class="btn btn-primary" @click="submitForm()">
        </div>
      </div>
    </div>
</div>
</div> 
```

上面的代码片段是一个基本的 HTML 声明，其中我们:

*   需要引导 CSS 库
*   需要 Vue JavaScript 库
*   需要 Axios JavaScript 库，这个库会发出 POST 请求。
*   声明了 5 个元素，包括 3 个输入文本框、一个文本区域和一个按钮，用于提交表单。

您会注意到，在 5 个元素的每一个中，前 4 个都声明了表单的某些属性的 v-model 属性。

V-model 是一种将输入绑定到 Vue 的方式，这样当这些输入发生变化时，Vue 就拥有它们的值。

Form 不是指 HTML 表单，而是指我们在 Vue 组件中用于绑定的对象。

最后，如果您查看 button 元素，您会注意到一个名为@click 的小指令。这个指令将按钮的 click 事件绑定到 Vue，指示 Vue 在按钮被点击时做什么。

[![](../Images/184dfcc3649f17c50808cfdfb9409f6a.png)T2】](https://logrocket.com/?cid=banner_b)

**在表单中实现 Vue**

在上一节中，我们已经解释了在 HTML 结构和@click 指令中看到像 v-model 这样的属性的原因。这里，我们展示了处理其余部分的 Vue 部分是什么样子的。

在 HTML 文档中打开一个脚本文件并粘贴到:

```
<script>
var app = new Vue({
    el: '#app',
    data: {
    form: {
    name: '',
    mob: '',
    email: '',
    mess: ''
    }
},
methods: {
  submitForm: function(){
      axios.post('https://httpbin.org/anything', this.form)
      .then(function (response) {
        console.log(response.data);
      })
      .catch(function (error) {
        console.log(error);
      });
  }
}
})
</script> 
```

在上面的代码块中，我们定义了一个名为 form 的对象，它包含了我们的数据。接下来，我们定义了一个名为 submitForm 的方法，它向 https://httpbin.org/anything 发出 Ajax 请求。我们使用 httpbin，因为他们的服务允许我们执行免费的 HTTP 方法。/any 路由将返回我们发送给它的确切数据。

看到使用 JavaScript 提交表单有多简单了吗？您所需要做的就是将 URL 更改为您的服务器的 URL。

**为什么我的表格没有提交？**我们经常注意到，在编写了看起来正确的代码之后，表单没有提交。我们如何解决这个问题？让我强调一下您的 Vue 表单可能无法提交的常见原因。

*   通过 el 键传递到 Vue 对象中的 id 为 app 的已装载元素不存在，并且 app 未绑定到 Vue
*   提交按钮上的点击处理程序不存在/未附加
*   没有引用 axios 库
*   没有引用 vue 库

### 使用 React 提交表单

React 是一个由脸书开发和维护的用于构建用户界面的 JavaScript 库。React 使得创建交互式 ui 变得不那么痛苦。为应用程序中的每个状态设计简单的视图，当数据发生变化时，React 将有效地更新和呈现正确的组件。

首先，让我们定义我们的 HTML 结构。创建一个名为 react.html 的文件，并添加:

```
<link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<div class="container" id="app">
</div> 
```

上面的代码片段是一个基本的 HTML 声明，其中我们:

*   需要引导 CSS 库
*   需要 React JavaScript 库
*   需要 React-Dom JavaScript 库
*   需要 Axios JavaScript 库，这个库会发出 POST 请求
*   声明一个 id 为 app 的 div，这将是我们的根组件

**将 React 应用到混音中**

我们有了一个基本的设置，其中包含所需的可用库和一个 react 将附加到的根元素。让我们继续 react 实现。打开脚本标签，输入:

```
class Root extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          form: {
            name: "",
            mob: "",
            email: "",
            mess: ""
          }
        };
        this._onInputChange = this._onInputChange.bind(this);
        this._onSubmit = this._onSubmit.bind(this);
      }
      _onInputChange(name, e) {
        var form = this.state.form;
        form[name] = e.target.value;
        this.setState(form);
      }
      _onSubmit() {
        axios
          .post("https://httpbin.org/anything", this.state.form)
          .then(function(response) {
            console.log(response.data);
          })
          .catch(function(error) {
            console.log(error);
          });
      }
      render() {
        return (
          <div className="row">
            <div className="col-md-4">
              <div className="panel">
                <h4 className="heading">
                  <strong>Quick </strong> Contact <span />
                </h4>
                <div className="form">
                  <input
                    type="text"
                    required=""
                    placeholder="Please input your Name"
                    className="form-control"
                    onChange={e => this._onInputChange("name", e)}
                  />
                  <input
                    type="text"
                    required=""
                    placeholder="Please input your mobile No"
                    className="form-control"
                    onChange={e => this._onInputChange("mob", e)}
                  />
                  <input
                    type="text"
                    required=""
                    placeholder="Please input your Email"
                    onChange={e => this._onInputChange("email", e)}
                    className="form-control"
                  />

                  <textarea
                    placeholder="Your Message"
                    className="form-control"
                    onChange={e => this._onInputChange("mess", e)}
                  />
                  <input
                    type="submit"
                    value="submit"
                    name="submit"
                    className="btn btn-primary"
                    onClick={this._onSubmit}
                  />
                </div>
              </div>
            </div>
          </div>
        );
      }
    }
    ReactDOM.render(<Root />, document.getElementById("app")); 
```

让我们回顾一下上面的内容。这里，在我们的构造函数中，我们声明了一个包含表单对象的初始状态，然后我们绑定了两个函数，我们将在输入改变时设置状态并提交表单。

在 _onInputChange 函数中，我们接收两个参数，它们是:

*   名称:元素的名称
*   事件:发生的变更事件

我们使用这两个参数来设置被改变的确切输入的状态。

在`_onSubmit`函数中，我们向`https://httpbin.org/anything`端点发出一个 post 请求，后者返回发送的确切参数。在这里，这是我们用来作为我们的服务器。

让我们仔细看看渲染函数，元素在这里被渲染。

这里，我们定义了 5 个元素，包括 3 个输入、一个文本区域(其更改事件绑定到`_onInputChange`函数)和一个按钮元素(其点击事件绑定到`_onSubmit`方法)。

最后，我们将应用程序附加到 HTML 标记的一个元素上。

为什么我的表单不显示？我敢打赌，你已经得到了一个空白的屏幕，不知道错误来自哪里。

快速浏览一下 render 函数，您会注意到其中有 jsx 语法。现在，这就是问题所在。除非您使用 babel 来编译您的应用程序，否则 jsx 很可能会失败。这是因为 jsx 不是常规的 javascript 语法，在这里，我们使用 React 的浏览器构建。

那么我们如何解决这个问题呢？这是一个简单的修复。

任何 JSX 块都可以转换为对 React.createElement 的调用，该调用有三个参数:

*   要创建的元素，例如 div、span、ul、e.t.c
*   一个属性对象，指定任何要在该元素上设置的属性值，如类、样式、必需等
*   要放入其中的任何子元素。这可能是一个字符串或对 React.createElement 的其他调用，以获取更多元素。

将渲染函数替换为:

```
render() {
        return (
            React.createElement("div", { className: 'row' }, [
                React.createElement("div", { className: 'col-md-4' }, [
                    React.createElement("div", { className: 'panel' }, [
                        React.createElement("h4", {}, 'Quick Contact'),
                        React.createElement("div", { className: 'form' }, [
                            React.createElement("input", {
                                type: 'text',
                                placeholder: "Please input your Name",
                                className: "form-control",
                                name: 'name',
                                onChange: (e) => this._onInputChange('name', e)
                            }),
                            React.createElement("input", {
                                type: 'text',
                                placeholder: "Please input your Mobile number",
                                className: "form-control",
                                name: 'mob',
                                onChange: (e) => this._onInputChange('mob', e)
                            }),
                            React.createElement("input", {
                                type: 'text',
                                placeholder: "Please input your Email",
                                className: "form-control",
                                name: 'email',
                                onChange: (e) => this._onInputChange('email', e)
                            }),
                            React.createElement("textarea", {
                                placeholder: "Please your message",
                                className: "form-control",
                                name: 'mess',
                                onChange: (e) => this._onInputChange('mess', e)
                            }),
                            React.createElement("button", {
                                type: 'button',
                                className: "btn btn-primary",
                                onClick: () => this._onSubmit()
                            }, "submit"),

                        ])
                    ])
                ]),

            ])
        );
    } 
```

另外，将 ReactDom.render 调用更新为:

```
ReactDOM.render(
    React.createElement(Root, null),
    document.getElementById('app')
); 
```

**为什么我的表格没有提交？**即使执行了我们认为必要的每个步骤并交叉检查了我们的代码，您的表单仍有可能无法提交，我们该如何解决这个问题？

*   确保您的控制台没有抛出错误
*   确保单击和更改事件被正确绑定
*   交叉检查 axios 库或您用于 post 请求的库是否被引用

### 使用 HyperApp 提交表单

HyperApp 是一个用于构建 web 应用程序的 JavaScript 微框架。这个框架极大地减少了您需要理解的概念，以便在保持与其他框架同等水平的同时保持高效。

HyperApp 在管理状态时坚持使用函数式编程，但采取了一种务实的方法来考虑副作用、异步操作和 DOM 操作。

首先，让我们定义我们的 HTML 结构。创建一个名为 hyper.html 的文件，并添加:

```
<link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
<script src="https://unpkg.com/hyperapp"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<div class="container" id="app">
</div> 
```

上面的代码片段是一个基本的 HTML 声明，其中我们:

*   需要引导 CSS 库
*   需要 Hyperapp JavaScript 库
*   需要 Axios JavaScript 库，这个库会发出 POST 请求
*   声明一个 id 为 app 的 div，这将是我们的根组件

**向应用程序介绍 Hyperapp**我们有了一个基本设置，其中包含所需的可用库和 Hyperapp 将附加到的根元素。让我们继续 react 实现。打开脚本标签，输入:

```
const h = hyperapp.h;
    const app = hyperapp.app;
    const state = {
      form: {
              name: '',
              mob: '',
              email: '',
              mess: '',
            }
    }

    const actions = {
      onInputChange: (event) => state => {
        state.form[event.target.name] = event.target.value;
        return state;
      },
      submitForm: () => {
        console.log(state.form)
    axios.post('https://httpbin.org/anything', state.form)
          .then(function (response) {
          console.log(response.data);
        })
          .catch(function (error) {
          console.log(error);
        });
      }
    }

    const view = (state, actions) => (
      h("div", {class: 'row'}, [
        h("div", {class: 'col-md-4'}, [
          h("div", {class: 'panel'}, [
            h("h4", {}, 'Quick Contact'),
            h("div", {class: 'form'}, [
              h("input", {type: 'text', placeholder: "Please input your Name", class:"form-control", 
                          name: 'name',
                         oninput: (e)=>actions.onInputChange(e)}),
              h("input", {type: 'text', placeholder: "Please input your Mobile number", class:"form-control", 
                          name: 'mob',
                         oninput: (e)=>actions.onInputChange(e)}),
              h("input", {type: 'text', placeholder: "Please input your Email", class:"form-control", 
                          name: 'email',
                         oninput: (e)=>actions.onInputChange(e)}),
               h("textarea", {placeholder: "Please your message", class:"form-control",
                              name: 'mess',
                         oninput: (e)=>actions.onInputChange( e)}),
              h("button", {type: 'button', class:"btn btn-primary", 
                         onclick: ()=>actions.submitForm()}, "submit"),

            ])
          ])
        ]),
      ])
    )
    app(state, actions, view, document.getElementById('app')) 
```

让我们回顾一下上面的内容。在这里，我们声明了一个包含表单对象的初始状态，然后继续声明两个动作，我们将在输入改变时设置状态并提交表单。

在 onInputChange 函数中，我们接收一个参数，它是:

*   事件:发生的变更事件

我们使用这两个参数来设置被改变的确切输入的状态。

在`_onSubmit`函数中，我们向`https://httpbin.org/anything`端点发出一个 post 请求，后者返回发送的确切参数。在这里，这是我们用来作为我们的服务器。

在这里，我们一定看到了 React 和 Hyperapp 的相似之处。出于我们的目的，我将 Hyperapp 描述为 React 的一个轻量级替代方案。

在上面代码的 render 函数中，我们会注意到 React 的确切相似之处。事实上，您会注意到的唯一区别是使用了 class 而不是 React 的 className 和 onInput 而不是 onChange。

> 出于同样的原因，我们没有在 React 表单中使用 *jsx，同样的原因，我们也没有在这里使用* *jsx。如果你使用* *npm 包，更喜欢使用* *jsx，请随意。*

### 结论

在本教程中，我们看到了使用 3 种不同的 JavaScript 框架提交表单是多么容易。我们还看到了当我们的表单没有按预期显示或提交时如何解决常见问题。你对本教程有什么看法或观点想分享吗？请在评论中告诉我们。

* * *

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[使用 JavaScript 框架(Vue，React，Hyperapp)](https://blog.logrocket.com/submitting-html-forms-using-javascript-frameworks-vue-react-hyperapp-44664dba1218/) 提交 HTML 表单最先出现在[的 LogRocket 博客](https://blog.logrocket.com)上。