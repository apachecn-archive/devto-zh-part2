# React-Final-Form 中的复选框组组件

> 原文：<https://dev.to/crazedvic/checkbox-group-component-in-react-final-form-5431>

*发布于 2018 年 11 月 6 日*

我会尽量简短，因为当你最有可能找到这篇文章的时候，你想要的只是看到它工作，然后复制粘贴，然后回到编码...

第一次进口这些是最低要求，虽然你也需要'最终形式'的软件包。

```
import React from "react";
import { Form, Field } from "react-final-form";
import { FieldArray } from 'react-final-form-arrays'
import arrayMutators from 'final-form-arrays'
import Checkbox from "@material-ui/core/Checkbox"; 
```

设置您的一系列选项:

```
const myOptions = ["Meat Lover", "Veggie Heaven", "Hawaii-5-0", "Inferno"]; 
```

设置您的 onSubmit 方法:

```
const onSubmit = async values => {
  window.alert(JSON.stringify(values, 0, 2));
}; 
```

这是您使用标准输入类型复选框的自定义组件:

```
const CheckboxGroup = ({ fields, options }) => {
  const toggle = (event, option) => {
    if (event.target.checked) fields.push(option);
    else fields.remove(option);
  };
  return (
    <div style={{ color: "blue" }}>
      {options.map(option => (
        <div key={option}>
          <input
            type="checkbox"
            onClick={event => toggle(event, option)}
          />
          {option}
        </div>
      ))}
    </div>
  );
}; 
```

这是一个使用 Material-UI 复选框的更奇特的自定义组件:

```
const CheckboxGroupMUI = ({ fields, options }) => {

  const toggle = (event, option) => {
    if (event.target.checked) fields.push(option);
    else fields.remove(option);
  };

  return (
    <div style={{ color: "darkblue" }}>
      {options.map(option => (
        <div key={option}>
          <Checkbox
            value={option}
            onChange={event => toggle(event, option)}
          />
          {option}         
        </div>
      ))}
    </div>
  );
}; 
```

好了，现在是主要部分:

```
function App() {
  return (
    <div>
      <h3>Checkboxes FML</h3>     
      <Form
        onSubmit={onSubmit}
        mutators={{
          ...arrayMutators
        }}
        render={({ handleSubmit, form, submitting, pristine, values }) => (
          <form onSubmit={handleSubmit}>

            <label>Pizza using Custom Checkbox Group</label>
            <FieldArray
              name="pizzas-1"
              component={CheckboxGroup}
              options={myOptions}
            />
            <hr />
            <label>Pizza using Custom Material-UI Checkbox Group</label>
            <FieldArray
              name="pizzas-2"
              component={CheckboxGroupMUI}
              options={myOptions}
            />
            <hr />
            <div>
              <button type="submit" disabled={submitting || pristine}>
                Submit
              </button>
            </div>
            <pre>{JSON.stringify(values, 0, 2)}</pre>
          </form>
        )}
      />
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

下面是它的动作:
[https://codesandbox.io/embed/5w511k5v0k](https://codesandbox.io/embed/5w511k5v0k)

希望这有所帮助。如果你有问题，请在下面发帖。