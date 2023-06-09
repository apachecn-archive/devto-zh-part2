# 不要控制一切！反应形式

> 原文：<https://dev.to/aralroca/dont-control-everything-react-forms-4d9>

原帖:[https://Aral roca . com/blog/don t-control-everything-react-forms](https://aralroca.com/blog/dont-control-everything-react-forms)

* * *

表单几乎是所有应用程序的重要组成部分。其中至少有一个是必需的:“登录”页面。在本文中，我们将解释 React 中不受控制的表单的好处，以及如何尽可能简单地在每个表单中重用它。我们将以经典的“登录”页面为例。

[![signIn](img/ba79d0f864cdb0b90f3fda0d71b4fd26.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GRiCmtoK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/11/signin.png%3Fw%3D960)

### 受控与非受控的区别

要理解“不受控”是什么意思，首先，我们来看看“受控”的含义。

React 中的一个常见错误是试图使用 state 和 onChange 方法来控制表单的每个字段。通常选择这种方式来允许在 onSubmit 方法中使用这种状态，尽管这不是获取字段的唯一和最好的方式。

**受控字段**

```
<form onSubmit={onSignIn}>

      <div>
        <input
          required
          value={this.state.username}
          onChange={e => this.setState({ username: e.target.value )}
          name="username"
          type="text"
          placeholder={userNamePlaceholder}
        />
      </div>

      <div>
        <input
          value={this.state.password}
          onChange={e => this.setState({ password: e.target.value )}
          name="password"
          type="password"
          placeholder={passwordPlaceholder}
        />
      </div>

      <button type="submit">
         Sign In
      </button>
  </form> 
```

然后我们可以在 onSignIn 方法中直接使用状态。

```
onSignIn = () => {
  const { username, password } = this.state;
  // ...
} 
```

这些字段是受控的，因为每次状态改变时，输入中呈现的文本也会改变。此外，每次用户键入时，onChange 事件都会被触发以保存新状态。如果我们键入 15 个字符的用户名和 8 个字符的密码；24 个 react 渲染将在引擎盖下发生(每个角色一个+第一个渲染一个)。

如果在提交表单之前使用状态，这种受控行为是有用的。例如，动态验证字段。否则，如果我们想在提交表单后使用所有字段，那么不加控制会更有用。

不受控制的字段是在没有反应状态的情况下编写的自然方式:

**不受控制的输入**

```
<form onSubmit={onSignIn}>

      <div>
        <input
          required
          name="username"
          type="text"
          placeholder={userNamePlaceholder}
        />
      </div>

      <div>
        <input
          name="password"
          type="password"
          placeholder={passwordPlaceholder}
        />
      </div>

      <button type="submit">
         Sign In
      </button>
  </form> 
```

在这种情况下，国家是不必要的。我们需要在事件 onSubmit 上使用这些字段，但是没有必要在每次改变 React 状态时都存储这些字段，因为我们已经在事件中拥有了这些字段。这意味着我们只为这个组件做一次简单的渲染:第一次渲染。

在 onSignIn 函数中，我们可以在 event.target.
中找到用户名和密码字段

```
onSignIn = (event) => {
      const [username, password] = Array.prototype
          .slice.call(event.target)
          .map(field => field.value)

  // ...
} 
```

然而，尽管我们对它进行了一些简化，但在每个表单 submit 中重复这个 Array.prototype.slice.call 仍然很难看。我们来看看如何改进。

### 改善不受控制的方式

我们的目标是简化每个“提交”事件的逻辑，以避免连续查找 event.target 中的字段的需要

```
onSignIn = ({ username, password }) => {
 // ...
} 
```

在这种情况下，我们将直接提供字段作为参数。当关键字是 name 属性时，此参数是一个包含所有表单字段的对象。

为了实现我们的目标，我们可以将表单标记替换为我们的个人组件:

[![form](img/fb02679d4fd0446c5e4edd74f569793a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_bhvHyN---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aralroca.files.wordpress.com/2018/11/form.png%3Fw%3D960)

我们的可重用个人表单组件可以是:

```
function Form({ children, onSubmit, ...restOfProps }) {
  const onSubmitAllFields = useCallback(event => {
    event.preventDefault()
    event.stopPropagation()

    const fields = Array.prototype.slice.call(event.target)
      .filter(field => field.name)
      .reduce((form, { name, value }) => ({
        ...form,
        [name]: typeof value === 'string'
          ? value.trim()
          : value,
      }), {})

    onSubmit(fields)
  }, [onSubmit])

  return (
    <form {...restOfProps} onSubmit={onSubmitAllFields}>
      {children}
    </form>
  )
}

export default memo(Form) 
```

因此，我们正在移动我们在表单中经常使用的重复代码: **preventDefault** ， **stopPropagation** ， **extract 字段** + **trim 字符串字段**。

现在，我们可以使用这种新方法，只需改变一个字符，从“形式”到“形式”。

> **注意**:我正在使用新的 hooks API(提议)，尽管它也可以被写成一个类组件。

### 结论

两种方法；受控形式和非受控形式因为不同的原因而很棒。我们必须知道区别，以便为任何场合选择最好的。我的建议是:通常使用无控制，除非你真的需要状态来做动态检查或者动态改变每个输入的文本。

> 这是我处理表格的首选方式。如果我不需要值被控制，那么我就不控制它[![👍](img/60ca6372ec746e8f077d7739b4c3aed7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZZkWC9qn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f44d.png)[https://t.co/0TGb3Lrs9Y](https://t.co/0TGb3Lrs9Y)
> 
> —肯特·c·多兹([@肯特·多兹](https://dev.to/kentcdodds) ) [8 de julio de 2018](https://twitter.com/kentcdodds/status/1015954912075644930?ref_src=twsrc%5Etfw)

如果您想尝试表单组件，我在 npm 中添加了:

```
npm install react-form-uncontrolled --save 
```

回购:[https://github.com/SylcatOfficial/react-form-uncontrolled](https://github.com/SylcatOfficial/react-form-uncontrolled)