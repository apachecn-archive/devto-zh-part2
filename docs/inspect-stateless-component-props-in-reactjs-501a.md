# 检查 ReactJs 中的无状态组件属性

> 原文：<https://dev.to/azrizhaziq/inspect-stateless-component-props-in-reactjs-501a>

嗨，最近我分享了我的 javascript 技巧，[点击这里](https://dev.to/azrizhaziq/tip-when-in-javascript-497f)。在上一节中，我提到了如何使用`console.log`进行调试。

所以今天，我将分享一点相同的概念，但它将在 ReactJs 的背景下。我希望它对我们有用😊。

在我之前的文章中，你可以用
`console.log(data) || ...someFn()`
记录回调函数的当前参数

```
 const nameAndEmails = users.map((user) => console.log(user) || ({ 
    name: user.name, 
    email: user.email 
   })) 
```

Enter fullscreen mode Exit fullscreen mode

在 ReactJs 中，特别是对于无状态组件，有时我们的同事没有编写任何 [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html) 。因此，很难知道它会呈现什么样的形状。

让我们跳到例子:

```
// let say you have this kind of component
const Button = (props) => (
    <button class="btn btn-primary" type="button" {...props} >
        {`${props.children}`}    
    </button> );

// use it like this 
<Button type="Submit">Click Me</Button> 
```

Enter fullscreen mode Exit fullscreen mode

你不会想把这个组件转换成`{ ... return (); }`，因为它需要大量的输入，比如:

```
// 😓, need to type aloot of things here
const Button = (props) => {
    console.log(props);

    return (
        <button class="btn btn-primary" type="button" {...props} >    
           {`${props.children}`}
        </button>
    );
}; 
```

Enter fullscreen mode Exit fullscreen mode

因此，您可以尝试用这种方法记录`props`，而不是转换成普通函数。

```
const Button = (props) => console.log(props) || (
     <button class="btn btn-primary" type="button" {...props}>
        {`${props.children}`}
     </button> );

// It will logs: 
// { 
//    type: 'Submit',
//    children: 'Click Me'
// } 
```

Enter fullscreen mode Exit fullscreen mode

本质上，你可以对任何回调函数使用这个技巧，比如`map.`、`.filter`、`.reduce`

我希望你明白了，下次见。