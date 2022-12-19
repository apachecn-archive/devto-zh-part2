# React 中表单的必要指南

> 原文：<https://dev.to/bnevilleoneill/an-imperative-guide-to-forms-in-react-5162>

[![](img/a3311a64ccb04bc101f82c7019f226c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V0NELw0V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/842/1%2AbPbp6STKWvl8Y-hC2jpyEg.jpeg)

所以，你刚刚开始使用 React，你已经开始构建 React 应用程序，你想知道，我如何在 React 中使用表单？不要担心。

在本教程中，我将解释 React 如何处理表单和事件。我还将带您了解如何编写代码来帮助从不同的表单组件获取输入，并在 React 应用程序中提交数据。

最后，我将展示如何使用 [Formik](https://github.com/jaredpalmer/formik) 库构建各种表单，从简单到复杂。

### 对形式和事件做出反应

在我们开始编写代码之前，重要的是要注意 React 如何处理表单和事件。考虑下面这个简单的输入标记:

`<input type="text" placeholder="First Name">`

为了获取输入标签中的数据，您需要能够以某种方式获取内容。这对于 React 来说并不容易，因为没有像 Vue.js 的 v-model 或 Angular 的 ng-model 这样的预置 API 来帮助实现这一点。

这并不容易，但可以做到。怎么会？

通过确保视图(输入、选择或文本区域字段)总是与状态同步。这意味着输入元素的值必须首先在状态中创建，然后在 render()函数中设置为该状态值。

类似下面的代码块:
[https://jsfiddle.net/yomi/z7gnLmds/embedded//dark](https://jsfiddle.net/yomi/z7gnLmds/embedded//dark)T3】在上面的代码块中可以看到，input 元素有一个值 this.state.inputvalue，这意味着 input 元素的值被设置为与 state 中的 inputvalue 同步。这实质上转化为这样一个事实，即在输入字段中输入的任何内容都将存储在状态中，但是这里有一个问题。

React 不知道如何检测输入元素是否有变化，如果有添加或删除。使用窗体事件处理程序 onChange 检查更改。

这将导致下面的代码块:
[https://jsfiddle.net/yomi/r5ofyqLz/embedded//dark](https://jsfiddle.net/yomi/r5ofyqLz/embedded//dark)
在上面的代码块中，input 元素现在对 onChange 事件增加了一个。只要 input 元素发生变化，就会执行 onChange 事件，并设置为执行 handleChange()函数。

handleChange()函数总是自动将状态设置为输入的当前值。还有一点需要注意的是在构造函数中添加了一行新的代码。

`this.handleChange = this.handleChange.bind(this);`

上面的代码行用于绑定 handleChange()函数。这样做是因为在 JavaScript 中，类方法在默认情况下是不绑定的。如果不这样做，调用 onChange 事件时，handleChange()函数将返回 undefined。

所以 React 现在知道如何将输入元素的值存储到状态，但是我们如何处理表单提交。很简单。看一下下面的代码块:
[https://jsfiddle.net/yomi/v7nh6op8/embedded//dark](https://jsfiddle.net/yomi/v7nh6op8/embedded//dark)
这基本上与上面看到的代码块相同，但是增加了一些内容。表单现在有一个执行 handleSubmit 函数的 onSubmit 事件。

handleSubmit()函数做两件事；每当提交表单时，它记录输入元素的当前值，最重要的是，它防止了浏览到新页面的默认 HTML 表单行为。下面的代码行也被添加到构造函数中，以便绑定 handleSubmit 函数。

`this.handleSubmit = this.handleSubmit.bind(this);`

需要注意的是，绑定函数有不同的方法。在构造函数中绑定它们是一种方法，但是我们也可以使用粗箭头。

参见下面的代码块以供参考:
[https://jsfiddle.net/yomi/t1p38h4m/embedded//dark](https://jsfiddle.net/yomi/t1p38h4m/embedded//dark)
上面的语法确保了这是在 handleSubmit 函数内绑定的。

上述将输入元素的值与状态同步的整个过程称为[受控组件](https://reactjs.org/docs/forms.html#controlled-components)。我们基本上使反应状态成为真理的唯一来源。负责呈现表单的 React 组件也负责每当用户向表单添加任何内容时发生的事情。

现在我们已经看到了 React 如何处理表单，以及如何确保值与状态同步，让我们用不同类型的字段构建一个适当的表单，即`<input>`、`<textarea>`、`<select>`、`radio`等等

下面的代码块/演示包含了演示如何处理不同表单字段所需的所有代码。我们将逐一介绍它们，看看它们是如何工作的。
[https://codesandbox.io/embed/8kmo7v8139](https://codesandbox.io/embed/8kmo7v8139)
**输入**
[https://jsfiddle.net/yomi/ewgavftt/embedded//dark](https://jsfiddle.net/yomi/ewgavftt/embedded//dark)
输入字段的代码实现很简单。该值被设置为与已经在状态中声明的 fullname 同步。onChange 事件用于在输入字段发生变化时执行 handleChange 函数。

上面输入字段中唯一的新东西是添加了 name 属性。因为我们将处理多个输入字段，所以注意它们中的哪一个实际上被修改是很重要的，name 属性有助于这一点。name 属性的值必须与构造函数中声明的状态值相同。

这也是 handleChange 函数发生变化的原因。
[https://jsfiddle.net/yomi/e4ah0c3n/embedded//dark](https://jsfiddle.net/yomi/e4ah0c3n/embedded//dark)
在上面的代码块中，handleChange 函数使用分配给不同输入元素的 name 属性来根据 event.target.value 的值确定要做什么

**文本区**
[https://jsfiddle.net/yomi/g5pf8d5r/embedded//dark](https://jsfiddle.net/yomi/g5pf8d5r/embedded//dark)
文本区字段的工作方式也与输入字段类似。该值被设置为与已经在状态中声明的消息同步。它还具有 name 属性，并被设置为 message。

**Select**
[https://jsfiddle.net/yomi/6qakhxhj/embedded//dark](https://jsfiddle.net/yomi/6qakhxhj/embedded//dark)
Select 元素有一个 value 属性，设置为与编辑器同步，该属性已经在状态中声明。因为这是一个选项下拉列表，所以知道选择了什么是很重要的，这就是为什么每个选项标签都有自己的值属性和独特的内容。

**Checkbox**
[https://jsfiddle.net/yomi/Lmssq9bb/embedded//dark](https://jsfiddle.net/yomi/Lmssq9bb/embedded//dark)
Checkbox 元素在 React 表单中的实现与上面的有些不同。不是将 this.state.terms 值设置为输入字段的 value 属性，而是将其设置为 checked 属性。状态值也必须是一个布尔值，即 The 或 falsy 值。
[https://jsfiddle.net/yomi/yaccqsht/embedded//dark](https://jsfiddle.net/yomi/yaccqsht/embedded//dark)
**单选**
[https://jsfiddle.net/yomi/d7f7zmsu/embedded//dark](https://jsfiddle.net/yomi/d7f7zmsu/embedded//dark)
在 React 表单中实现单选元素的方式与上面的复选框类似。单选按钮元素都具有相同的名称属性，但具有不同的值属性，如上所示，其中“是”单选按钮的值是**是**，而“否”单选按钮的值是**否**。选中的属性用于将状态值设置为 Yes 或 No，如上所示，只要选择了这两个值中的任何一个。

### 使用 Formik

[![](img/91c0b3e582d8b485dc51390d7b65f852.png)](https://github.com/jaredpalmer/formik)

<figcaption>[https://github.com/jaredpalmer/formik](https://github.com/jaredpalmer/formik)</figcaption>

如果您认为以上述方式设置 React 表单有点紧张和令人担忧，那么我有好消息要告诉您。Formik 库有助于在 React 中创建强大而轻量的表单。它让您能够获取和操作值，设置错误和警告，自定义输入和许多其他功能，使构建表单变得容易。

> Formik 跟踪表单的状态，然后通过 props 向表单公开它以及一些可重用的方法和事件处理程序(handleChange、handleBlur 和 handleSubmit)。handleChange 和 handleBlur 完全按照预期工作—它们使用 name 或 id 属性来确定要更新哪个字段。——[https://github.com/jaredpalmer/formik](https://github.com/jaredpalmer/formik)

Formik 组件结合 [Yup](https://github.com/jquense/yup) 可以用来编写非常棒的表单验证。Yup 用于对象模式验证，这意味着在用 formik 构建 React 表单时，它可以用作验证器。

对于 formik API，有三个重要的 API 需要考虑和理解:

1.  withFormik(选项)

**withFormik(选项)**

withFormik(选项)允许您创建更高阶的 React 组件类。然后，您可以根据提供的选项在组件中传递一些道具和表单处理程序。让我们来看看 withFormik 中的一些可用选项。

*处理提交*

顾名思义，handleSubmit 有助于 formik 中的表单提交。它被自动传递表单值和包装在组件中的任何其他属性。

*mapPropsToValues*

mapPropsToValues 用于初始化表单状态的值。Formik 将 mapPropsToValues 的结果转换为可更新的表单状态，并使这些值作为 props.values 供新组件使用。

*验证模式*

这可能是一个函数，它返回一个 Yup 模式或一个实际的 Yup 模式本身。这有助于表单内部的验证。

**<字段/ >**

Formik 中的 Field 组件用于使用 Formik 自动设置 React 表单。它能够通过使用 name 属性来获取值，它使用 name 属性来匹配 Formik 状态，并且它总是设置为 input 元素。这可以很容易地通过指定组件属性来更改。
[https://jsfiddle.net/yomi/9rugwfe8/embedded//dark](https://jsfiddle.net/yomi/9rugwfe8/embedded//dark)
**<形式/ >**

是一个帮助节省时间的助手组件。有助于防止打出。你所要做的就是在一个

标签中包含所有的表单细节，就像下面的代码:
[https://jsfiddle.net/yomi/q40t6orL/embedded//dark](https://jsfiddle.net/yomi/q40t6orL/embedded//dark)
了解了这些基础知识之后，让我们看看如何使用 Formik 来构建和验证 React 表单。

像上面的控制组件一样，一个完整的表单将被构建，然后我们将检查表单的不同部分以及它是如何工作的。完整的代码/演示可以通过下面的链接查看。
[https://codesandbox.io/embed/8p25nojzk2](https://codesandbox.io/embed/8p25nojzk2)
在上面的代码块中，第一行代码都是导入的。我们从 react-dom 导入 React、render，从 formik 导入一些带有析构赋值的组件。还导入了一个 JavaScript 对象模式验证器。
[https://jsfiddle.net/yomi/4798vurp/embedded//dark](https://jsfiddle.net/yomi/4798vurp/embedded//dark)
下一块代码是带道具参数的胖箭 App 函数。props param 被设置为一个包含将在应用程序中使用的值的对象。
[https://jsfiddle.net/yomi/qnpwdpmt/embedded//dark](https://jsfiddle.net/yomi/qnpwdpmt/embedded//dark)
从表单中获取的所有详细信息都存储在值中，验证错误存储在错误中，touched 是一个布尔值，用于检查输入字段是否处于焦点中，handleChange 有助于在输入字段发生变化时执行特定功能，issue submit 也是一个布尔值，在单击 submit 时设置为 true。

App 函数还返回一个包含表单 JSX 标记的 div。

在第 **17** 行，<表单>组件用于包含表单所需的全部代码。如上所述，它有助于防止在 submit = { props . handle submit }/>上打出<表单。

我将重点介绍表单组件中不同的字段组件和输入元素，以及它们如何与 Formik 一起工作。

**文本输入**
[https://jsfiddle.net/yomi/934j0hmy/embedded//dark](https://jsfiddle.net/yomi/934j0hmy/embedded//dark)
字段组件总是要设置为输入元素。所以剩下的就是定义 name 属性，以便 Formik 可以自动获取值。

Field 组件上面的代码行只是用于验证目的。它使用 touched.fullname 检查输入元素是否处于焦点上，然后使用 errors.fullname 检查是否有任何错误，如果有错误，则在对象模式验证器中显示自定义消息。稍后我将介绍验证的设置。

**选择**
[https://jsfiddle.net/yomi/tLcnbc8z/embedded//dark](https://jsfiddle.net/yomi/tLcnbc8z/embedded//dark)
如上所述，字段组件的默认状态被设置为输入，但这可以通过如上所示指定组件属性来轻松更改。name 属性被设置为 editor，并且有两个值不同的 option 元素。如上所述，第一行代码也用于验证。

**radio**
[https://jsfiddle.net/yomi/p7wwvLn1/embedded//dark](https://jsfiddle.net/yomi/p7wwvLn1/embedded//dark)
对于 Radio 元素，我们不能使用 Field 组件，而是使用老式的输入和一种类型的 Radio。两个单选选项都设置为相同的名称属性，但每个单选选项都有不同的值。

**复选框**
[https://jsfiddle.net/yomi/nt1nfj2z/embedded//dark](https://jsfiddle.net/yomi/nt1nfj2z/embedded//dark)
这里的 Checkbox 元素是复选框类型的字段组件。选中的事件被设置为将新闻稿的值更改为 true 或 falsy 值。

**提交按钮**

另一个需要注意的是按钮元素。Formik 自动检测到单击表单末尾的按钮元素表示用户打算提交所有的表单细节。

`<button disabled={isSubmitting}>Submit</button>`

问题提交是一个提交状态。它要么被设置为真值，要么被设置为假值。如果设置为 true，当 Formik 调用 handleSubmit 处理程序时，该按钮将被禁用。

DisplayFormikState 函数是一个无状态函数，它通过 props 在表单中显示原始数据和错误值。
[https://jsfiddle.net/yomi/d0dz38th/embedded//dark](https://jsfiddle.net/yomi/d0dz38th/embedded//dark)
FormikApp 用于包含 withFormik 高阶分量(HoC)中的整个形态。
[https://jsfiddle.net/yomi/24j4ha3v/embedded//dark](https://jsfiddle.net/yomi/24j4ha3v/embedded//dark)
mapsPropsToValues 函数有助于将我们之前在 App 函数中定义的外部属性转化为表单值。它返回从表单细节中获取的所有值。接下来要注意的是 validationSchema。

validationSchema 有助于表单验证。它使用 [Yup](https://github.com/jquense/yup) ，这是一个对象模式验证器来实现这一点。

正如你在上面看到的，该值的名称被设置为 Yup，然后你决定该值是否应该是一个[字符串](https://github.com/jquense/yup#string)、[数字](https://github.com/jquense/yup#number)、[布尔](https://github.com/jquense/yup#boolean)或[日期](https://github.com/jquense/yup#date)。还可以通过链接 required()并将错误消息放在括号内来确保该值是必需的。

Yup 还允许您用 min() API 设置字符串的最小长度。它接受两个值，字符数和错误消息，如果值没有达到这个数量。

您可以查看 Yup [文档](https://github.com/jquense/yup)以获得更多 API 以及如何更好地验证表单。

handleSubmit 函数是提交处理程序，它接受值(包含表单细节)，resetForm 用于重置所有表单值，setErrors 用于在有错误时设置错误消息，setSubmitting 用于强制设置[issue submitting](https://github.com/jaredpalmer/formik#issubmitting-boolean)。

然后，我们的表单被包装在 withFormik HoC 中，FormikApp 被呈现在 React 应用程序上。

通过上面的所有代码，您可以看到 Formik 使构建 React 应用程序变得多么容易。

### 结论

在本教程中，我们浏览了如何使用 React 构建表单的指南。我强调了这样一个事实，React 没有提供处理表单的默认方式，相反，您必须使用 handleChange 事件来检查更改，同时与状态同步。我还解释了 React 中受控组件的基础知识

我们还使用了 Formik，它基本上是一个帮助构建表单的特设工具。我们介绍了 Formik 附带的不同 API 以及如何使用它们。

使用受控组件构建的表单的演示和代码可以在 [CodeSandbox](https://codesandbox.io/embed/8kmo7v8139?module=%2FApp.js&view=editor) 上看到，Formik 的演示和代码也可以在[这里](https://codesandbox.io/s/8p25nojzk2?autoresize=1&view=editor)看到。

* * *

### Plug: LogRocket，一款用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

《React 中表单的必要指南》这篇文章最早出现在[博客](https://blog.logrocket.com)上。