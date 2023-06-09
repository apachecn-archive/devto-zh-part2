# 反应式^0.54.2 的 TextInput 组件

> 原文：<https://dev.to/chansuke/textinput-component-for-reactnative-0542-che>

[![zuckerburg](img/db2dfa8c35d3d603c431f0350bbc7895.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--un7yinNy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cygqsps3c571o8szgjcn.jpg)

从 v0.54.2 开始，`TextInput`的一个行为有点变化。如果您将`onChangeText`与`value`或“默认值”一起使用，默认情况下，日语(和任何其他非字母文本)文本输入不可用。
如果要输入非字母文本，`value(or defaultValue)`需要作为`props`处理。所以创建另一个`TextInput`组件来捕捉道具值是解决方案之一。

创建组件。

```
import React, { Component } from 'react';
import { Platform, TextInput } from 'react-native';

class CustomTextInput extends Component {
  shouldComponentUpdate(nextProps) {
    return Platform.OS !== 'ios' || this.props.value === nextProps.value;
  }
  render() {
    return <TextInput {...this.props} />;
  }
}

export default CustomTextInput; 
```

Enter fullscreen mode Exit fullscreen mode

并将该值作为道具发送

```
<CustomInputForm
  style={{
    height: 40,
    borderColor: 'gray',
    borderRadius: 5,
    borderWidth: 1,
    backgroundColor: 'white',
    margin: 5
  }}
  onChangeText={value => this.setState({ value })}
  value={this.state.value}
  placeholder="Enter your name"
  placeholderTextColor="gray"
/> 
```

Enter fullscreen mode Exit fullscreen mode