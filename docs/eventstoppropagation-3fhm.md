# event.stopPropagation()

> 原文：<https://dev.to/samsha1/eventstoppropagation-3fhm>

目前，我正在学习 react，并开始知道 event.stopPropagation()函数是多么有用。所以，让我们开始吧。

让我们创建一个简单的功能组件

```
deleteTask(){

    let tasks=this.state.tasks;
    tasks.splice(index,1);
    this.setState({
       tasks 
    })

}

const ToDoItem = (props) => {
    return (
        <li onClick={ ()=> { props.clickHandler(props.index)}}
            className={props.details.completed} ? 'completed' : ''>
           Some Text 
        <button onClick={ ()=>{props.deleteTask(props.index)}}>Delete</button>
        </li>

    )

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您单击 Delete 按钮，那么在 li 和 button 元素上都会触发 onClick 事件。这里的要点是我们只想触发按钮 onClick 事件，而不是 li。如果在细节属性中没有定义属性(已完成),那么我们将得到控制台消息

```
Cannot read the property 'completed' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

这是因为当我们点击按钮元素时，我们也间接地触发了李。因为，李是按钮的父元素。触发了 li onClick 事件，并且在 props 详细信息中没有定义的“已完成”。因此，要解决这个问题，我们可以使用 event.stopPropagation()。

```
const ToDoItem = (props) => {
    return (
        <li onClick={ ()=> { props.clickHandler(props.index)}}
            className={props.details.completed} ? 'completed' : ''>
           Some Text 
        <button onClick={ (evt)=>
            evt.stopPropagation();
            {props.deleteTask(props.index)}}>Delete</button>
        </li>

    )

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，由于 evt.stopPropagation()，事件没有传播到父元素。所以，我们可以走了。