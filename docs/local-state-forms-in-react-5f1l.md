# React 中的本地状态形式

> 原文：<https://dev.to/mrbenj/local-state-forms-in-react-5f1l>

从我工作过的前几家公司，无论是自由职业者还是全职员工，我都移植了自己的小 React 表单库，并将它们介绍给我工作过的每个团队。它很小，不可知，简单明了，允许用户进行客户端和服务器端验证，通过父组件显示错误。

下面是它的一点样子:

```
import React from 'react';
import FormManager from '../component/FormManager';
import { InputField, RadioButtonField } from '../component/FormManager/Fields';

class IndexPage extends Component {
  constructor(props) {
    super(props);

    this.state = {
      errors: {}
    };
  }

  onFormInput = values => {
    // Your own validation stuff happens here.
    // If there's an error, you do this:
    /*
      this.setState({ errors: { password: 'Incorrect password' } });
    */
  }

  onSubmit = values => {
    // Handle submit logic here!
  }

  render() {
    <div>
      <FormManager errors={this.state.errors} onValueChange={this.onFormInput} onSubmit={this.onSubmit}
        <InputField name="username" />
        <InputField name="password" type="password" />
        <p>Take me to:</p>
        <RadioButtonField 
          name="next_view" 
          value="dashboard" 
          label="Dashboard"
        />
        <RadioButtonField 
          name="next_view" 
          value="account_page" 
          label="My Account Page" 
        />
      </FormManager>
    </div>
  }
} 
```

事情是这样的，我希望*T2】发布这个开源软件，但是看着`Formik`，它在超过 10k 的明星中非常受欢迎，而且我的库肯定没有 Formik 附带的花哨功能。我只是想制作我自己的工具，它需要最少的样板文件，并且易于查看，只需查看代码即可。*

如果有的话，我会称之为`Reforma - A Local-State driven approach to forms in React`。

你们觉得怎么样？