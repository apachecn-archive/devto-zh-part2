# Formik 材质 UI 使用 Formik、Material-UI 和 Yup 反应表单。

> 原文：<https://dev.to/finallynero/react-form-using-formik-material-ui-and-yup-2e8h>

## 简介

有时，在 react 中管理表单可能会很麻烦，如果您决定使用像 redux-form 这样的库，它们会带来很大的性能开销，这在您正在构建的应用程序中可能无法承受。Formik 是您的救星，它是一个小库，包大小为 12 kB，相比之下 redux-form 的包大小为 22.5 kB minified gzipped，这是最好的部分；Formik 帮助完成表单处理这一令人厌烦的任务

*   处理表单状态
*   处理表单验证和错误
*   处理表单提交

您可以在 [Formik](https://jaredpalmer.com/formik) 上查看关于该库的更多信息

Formik 还与 material-ui 无缝集成；这是一个 react 库，实现了 Google material design，提供了像输入、按钮、标签和其他一些现成的组件。

您也可以查看他们的文档了解更多信息 [Material-Ui](http://material-ui.com/)

最后，是的。什么是呀？它是一个 JavaScript 对象模式验证器和对象解析器。在这种情况下，Yup 只是帮助处理验证。这并不意味着您不能为 Formik 编写自己的自定义验证器，但是我发现我使用 Yup 的体验很好，它提高了我代码的可读性。

更多关于是在这里的文件[是](https://www.npmjs.com/package/yup)。

本文将解释如何用 Formik、Yup 和 Material-UI 构建表单和处理表单验证。

下面是我们在本指南中要做的事情的简要概述:

*   使用 create-react-app 创建 react 应用程序。
*   用 Material-UI 和 Formik 创建一个简单的表单。
*   用 Yup 编写验证规则/验证模式。
*   将 Yup 与 Formik 配合使用。

本教程假设您了解 react。

这里有一个我们要构建的表单的代码沙盒演示:
[Formik 演示应用](https://codesandbox.io/s/ol11mmk30z)

### 安装:

*   使用 CRA 创建 react 应用[创建 React 应用](https://github.com/facebook/create-react-app)

`Create-react-app formik-form-demo`

运行此程序后，我们的项目结构应该如下所示:

[![File structure](img/b86bf3e81593e045ac490b5e02a2b835.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E3uJzReP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fv238hoxcca3720tzhow.JPG)

现在打开`src`文件夹中的`App.js`文件，然后删除类名为 App 的父文件`div`的内容。

[![App.js](img/7be0ebd15c899ba30b8653bb36b1f0be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3iqG9gtB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/w8XxgNeL9WNB2ZrQ0MF_YPYB5FAAapixo1CeXdP3N-j_yHzio4J7Em5U2iBsk7UhsKFygnqZGOYLEPOfUjee11UCwuZZq8NtiIq_tRluLmy0x2tSyI-x1TMdipWKn6YfKg)

在你的终端运行

```
Yarn add or npm install formik yup @material-ui/core 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将 formik、Yup 和 material-UI 添加到我们的依赖项中。现在我们的依赖项已经安装好了，在`src`文件夹中创建一个名为`InputForm`的新文件夹，然后在`InputForm`文件夹中创建`index.js`和`form.js`文件。

您的 src 文件夹现在应该是这样的:

[![src now](img/9062ba84dae6ccffa99e325c7ef83065.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UPyyzDLN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ghktpphit5kkcc7vmvv.JPG)

`form.js`文件将包含演示文稿，而`index.js`将包含大部分逻辑。
目前你的应用程序应该显示一个空白页，所以现在让我们显示我们的表单。

在您的`form.js`文件中添加以下代码

```
import React from "react";
import Button from "@material-ui/core/Button";
import TextField from "@material-ui/core/TextField";

export const Form = (props) => {
  return (
   <form onSubmit={() => {}}>
     <TextField
       id="name"
       name="name"
       label="Name"
       fullWidth

     />
     <TextField
       id="email"
       name="email"
       label="Email"
       fullWidth
     />
     <TextField
       id="password"
       name="password"
       label="Password"
       fullWidth
       type="password"
     />
     <TextField
       id="confirmPassword"
       name="confirmPassword"
       label="Confirm Password"
       fullWidth
       type="password"
     />
     <Button
       type="submit"
       fullWidth
       variant="raised"
       color="primary"
     >
       Submit
     </Button>
   </form>
 );
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的是创建一个简单的表单，它有四个字段(姓名、电子邮件、密码和确认密码)和一个带有 material-UI 的按钮。

在`InputForm`文件夹中的`index.js`文件中添加以下代码:

```
 import React, { Component } from "react";
import { Formik } from "formik";
import withStyles from "@material-ui/core/styles/withStyles";
import { Form } from "./form";
import Paper from "@material-ui/core/Paper";

const styles = theme => ({
 paper: {
   marginTop: theme.spacing.unit * 8,
   display: "flex",
   flexDirection: "column",
   alignItems: "center",
   padding: `${theme.spacing.unit * 5}px ${theme.spacing.unit * 5}px ${theme
     .spacing.unit * 5}px`
 },
 container: {
   maxWidth: "200px"
 }
});

class InputForm extends Component {
 constructor(props) {
   super(props);
   this.state = {};
 }

 render() {
   const classes = this.props;
   return (
     <React.Fragment>
          <div className={classes.container}>
         <Paper elevation={1} className={classes.paper}>
           <h1>Form</h1>
           <Formik
             render={props => <Form {...props} />}
           />
         </Paper>
       </div>
     </React.Fragment>
   );
 }
}

export default withStyles(styles)(InputForm); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们创建了一个名为`InputForm`的类组件。在顶部，我们导入了刚刚创建的表单组件。然后将它作为一个`render prop`传递给`Formik`组件。

使用 Formik 有三种渲染方式

*   `<Formik component />`
*   `<Formik render />`
*   `<Formik children />`

我们在上面使用了`render props`。所有三个渲染方法都将被传递一些道具，包括:

*   错误
*   处理变更
*   处理
*   isValid
*   触摸
*   setFieldTouched

还有几个属性被传递给你的组件，检查所有属性的文档[来验证文档](https://github.com/jaredpalmer/formik)

接下来转到`src`文件夹中的`App.js`文件，导入`InputForm`组件，然后将其添加为`div`的子组件。这就是我们的`App.js`现在应该呈现的形式。

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import InputForm from './InputForm'

class App extends Component {
 render() {
   return (
     <div className="App">
       <InputForm/>
     </div>
   );
 }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

[![initial form](img/1430f6f1ba038f9214e1b5ee42f01ab6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eqfil1wj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh5.googleusercontent.com/jjBRs0HyIS4F-zT58_JGnyEFk95S2Sb7bBrqdR3gg8KS-44VfGz4KzYwqQX3MXpMAcZZRszgXVEb6KbZwzMeUiukWA5Gp2tkp-QOlc9dp2AXIr5L8ItPjr3v4BtY80eZJ_HnfFXk)

现在我们已经呈现了表单，让我们从表单验证开始。这就是需要 Yup 的地方，基本上，Yup 提供了帮助我们编写直观的验证规则的函数。
首先我们将 Yup 导入到`InputForm`文件夹中的`Index.js`文件中，然后我们使用它的 API 来编写我们的验证规则。

导入语句
`import * as Yup from "yup"`

注意:将一个库的所有函数/API 导入你的代码库并不是一个好的做法。

现在将以下代码添加到`InputForm`文件夹中的`Index.js`文件中，这是我们的验证规则或验证方案。

```
 const validationSchema = Yup.object({
name: Yup.string("Enter a name")
.required("Name is required"),
email: Yup.string("Enter your email")
.email("Enter a valid email")
.required("Email is required"),
password: Yup.string("")
.min(8, "Password must contain at least 8 characters")
.required("Enter your password"),
confirmPassword: Yup.string("Enter your password")
.required("Confirm your password")
.oneOf([Yup.ref("password")], "Password does not match") 
```

Enter fullscreen mode Exit fullscreen mode

我不知道你怎么想，但乍一看，这看起来非常直观。Yup 提供了几个 API 来简化对象验证。下面列出了其中的一些。

##### 原料药

`Yup.object()`:用于定义对象的键以及这些键的模式。在本例中，它用于定义我们正在验证的字段(姓名、电子邮件、密码、确认密码)，还用于定义这些字段的验证规则。

`Yup.string()`:定义字符串模式。这指定字段应该是一个字符串，它还接受一个用于设置错误消息的可选参数。我们定义的所有四个字段都是字符串。此外，我们可以链接函数或方法，以便每个字段可以有多个验证规则。

`Yup.required()`:指定字段为必填，不能为空。它还需要一个可选参数来定义错误消息。

`Yup.email()`:定义一个电子邮件模式，并带有一个可选参数。

`Yup.min()`:设置数值的最小长度。它接受两个参数，长度和错误消息。

`Yup.ref()`:创建对另一个兄弟字段或兄弟后代字段的引用。它接受一个强制参数，也就是我们所引用的字段。

`Yup.oneOf()`:将一组值列入白名单。它接受一组白名单值和一个设置错误消息的可选参数。

查看文档以获得 API 的完整列表。

既然我们已经定义了验证模式/规则，那么我们如何将它集成到我们的应用程序中呢？

记得我之前说过，Yup 与 Formik 无缝集成，Formik 为 Yup 提供了一个名为`validationSchema`的特殊支持，它会自动将 Yup 的验证错误转换成一个漂亮的对象。因此，我们将验证规则传递给`validationSchema` prop。Formik 还允许你使用`initialValues`道具为你的字段设置初始值。

所以当我们将`validationSchema`和`initialValues`道具添加到 Formik 组件时，我们的`InputForm`组件的渲染函数应该是这样的。

```
 render() {
  const classes = this.props;
  const values = { name: "", email: "", confirmPassword: "", password: "" };
  return (
<React.Fragment>
    <div className={classes.container}>
        <Paper elevation={1} className={classes.paper}>
        <h1>Form</h1>
        <Formik
            render={props => <Form {...props} />}
            initialValues={values}
            validationSchema={validationSchema}
        />
        </Paper>
    </div> </React.Fragment> );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经定义了验证规则和初始值，现在让我们使用传递给表单组件的 props 来处理验证输入。

在`InputForm`文件夹中的表单组件中，我们析构了 props 并创建了一个 change 函数来处理我们的输入更改

```
const {
values: { name, email, password, confirmPassword },
errors,
touched,
handleSubmit,
handleChange,
isValid,
setFieldTouched
} = props;

const change = (name, e) => {
e.persist();
handleChange(e);
setFieldTouched(name, true, false);
}; 
```

Enter fullscreen mode Exit fullscreen mode

有几个道具被`Formik`传递给表单组件，但是我不会在这个演示中使用它们。
使用的道具有:

*   values:包含表单字段初始值的对象。

*   errors:包含字段错误消息的对象。

*   touched:包含已被触摸/访问的字段的对象，已被触摸的字段设置为 true，否则设置为 false。

*   handleChange:通用输入处理程序，这将更新值[key],其中 key 是发出事件的输入的名称属性。如果 name 属性不存在，handleChange 将查找输入的 id 属性。

*   isValid:如果没有错误，即(errors 对象中没有错误)，则返回 true。

*   setFieldTouched:是一个用于设置字段的触摸状态的函数。第一个参数是字段的名称，第二个参数是要设置为 true 的触摸状态的值，最后一个参数是用于防止验证的布尔值。

现在让我们对表单组件进行更改，以便在出现错误时可以看到错误消息。
Material-UI `TextField`组件提供了两个可以帮助我们优雅地显示错误信息的道具，这两个道具分别是`helperText`和`error`用于显示错误。

当我们将这些道具添加到我们的`TextField`组件时，表单组件应该是这样的。

```
 export const Form = props => {
 const {
   values: { name, email, password, confirmPassword },
   errors,
   touched,
   handleChange,
   isValid,
   setFieldTouched
 } = props;

 const change = (name, e) => {
   e.persist();
   handleChange(e);
   setFieldTouched(name, true, false);
 };
 return (
   <form
     onSubmit={() => {
       alert("submitted");
     }}
   >
     <TextField
       id="name"
       name="name"
       helperText={touched.name ? errors.name : ""}
       error={touched.name && Boolean(errors.name)}
       label="Name"
       value={name}
       onChange={change.bind(null, "name")}
       fullWidth

     />
     <TextField
       id="email"
       name="email"
       helperText={touched.email ? errors.email : ""}
       error={touched.email && Boolean(errors.email)}
       label="Email"
       fullWidth
       value={email}
       onChange={change.bind(null, "email")}

     />
     <TextField
       id="password"
       name="password"
       helperText={touched.password ? errors.password : ""}
       error={touched.password && Boolean(errors.password)}
       label="Password"
       fullWidth
       type="password"
       value={password}
       onChange={change.bind(null, "password")}

     />
     <TextField
       id="confirmPassword"
       name="confirmPassword"
       helperText={touched.confirmPassword ? errors.confirmPassword : ""}
       error={touched.confirmPassword && Boolean(errors.confirmPassword)}
       label="Confirm Password"
       fullWidth
       type="password"
       value={confirmPassword}
       onChange={change.bind(null, "confirmPassword")}

     />
     <Button
       type="submit"
       fullWidth
       variant="raised"
       color="primary"
       disabled={!isValid}
     >
       Submit
     </Button>
   </form>
 );
}; 
```

Enter fullscreen mode Exit fullscreen mode

你应该注意到我给`Textfield`组件添加了三个道具，`helperText`，`error`和`onChange`。
`onChange`被设置为我们上面写的修改函数，用于处理输入字段的修改。
将`helperText`属性设置为三元运算符(If 语句),声明如果该字段被触摸，则将`helperText`属性设置为该字段的错误消息，否则将其设置为空字符串。将`error`属性设置为布尔值，以指示验证中的错误。

最后，`Button`组件有一个名为`disabled`的属性来禁用按钮，我们将其设置为非`!isValid`,因此如果 errors 对象中有任何错误，按钮将保持禁用状态，我的意思是我们不想提交无效的值。

[![working final form](img/535755a6090445102a67f582db3acb68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sx1htTrv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/g4xGDrOUKZzvQMjH9UgQI0IXuTwMw17-a_T6O0Zuj77Gsj8EhWcTUOBv_SjHscppBcM5d21IuecBP7ESA4Gou8nyafNKAEn-2F3TbXJAxNBzwM5IyRiew2xYKn-GFMPs3oVW-dET)

用 Formik，Material-UI 和 Yup 创建表单是很棒的。

这是我的第一篇技术文章/帖子，所以我愿意接受任何有助于提高我写作的建议。

如果您有任何问题或建议，请在下面评论。

*特别感谢我的朋友 [YJTheRuler](https://twitter.com/YJTheRuler) 编辑这篇文章，他为 [@radronline](https://t.co/kiG4o0nHFY) 撰写关于非洲节拍音乐和非洲文化的文章。*