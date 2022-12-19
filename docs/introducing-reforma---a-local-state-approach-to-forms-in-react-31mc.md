# 引入 Reforma——React 中表单的本地状态方法

> 原文：<https://dev.to/mrbenj/introducing-reforma---a-local-state-approach-to-forms-in-react-31mc>

我在一两周前发表了一些关于用 React 制作表单库的东西。

我喜欢 Redux-Form，但是我不喜欢把表单的状态放在 redux-store 中。这有点违背了商店的宗旨...

我喜欢 Formik，但它就像一个图书馆，有很多我从未真正要求过的功能...

在我目前的工作场所( [The Bouqs Company](https://www.bouqs.com) )，我们制作了一个名为“FormManager”的小程序库，它非常适合我们快速轻松地构建带有嵌套字段的表单。我向我的 CTO 提出发布类似这样的开源软件，他让我以自己的名义发布，而不是以 Bouqs 的名义。

在我自己的时间里，我做了一些相当大的改动，使用了所有的 React 16 优点，比如`Fragment`，并且使用了一些不错的 es 语法建议，比如`optional chaining`。

我很高兴地宣布这个图书馆完工了！今天就可以开始用了！

```
npm install react-reforma --save 
```

```
import React from 'react';
import Reforma, { 
    InputField,
    CheckboxField } from 'react-reforma';

export default function LoginForm(props) {
  const { onSubmit, errors } = props;
  return (
    <Reforma onSubmit={onSubmit} errors={errors}>
        <InputField name="username" />
        <InputField name="password" type="password" />
        <CheckboxField name="remember" label="Remember on this computer?" />
        <button type="submit">Login</button>
    </Reforma>
  );
} 
```

只需要几行代码，而且不需要担心`onChange`和`value`道具。所有这些东西都在引擎盖下为你处理好了。

组件也不需要是直接子组件...您可以嵌套它们并设计容器的样式:

```
<Reforma onSubmit={this.props.onSubmit} errors={this.props.errors}>
  <div className="input-fields">
    <InputField name="username" />
    <InputField type="password" />
  </div>
  <div className="remember-field">
    <CheckboxField name="remember" label="Remember on this computer?" />
    <p className="remember-field-text">
       Note: This option not recommended for shared computers.
    </p>
    <p className="bold">
      Only use check this box if you are the only user of this device!
    </p>
  </div>
  <div className="submit-button-container">
    <button type="submit">Login</button>
  </div> </Reforma> 
```

Reforma 将渲染所有子元素，并检测何时有一个`<*Field>`组件，并自动将`onChange`、`value`和`error`道具注入这些字段，所以你根本不需要担心它们。

如果你想了解更多并开始使用 Reforma，你可以在这里看到完整的文档和 Github repo

感谢阅读！我希望你喜欢使用我的工具:)