# 快速简单...一个功能中的反应状态管理

> 原文：<https://dev.to/genta/fast--easy-react-states-management-in-one-function-2h08>

# 不重复代码...

在 React 中，就像你代码中的其他地方一样，你必须**永远不要**重复你自己，除非是绝对**必要的(几乎从不)。**

可能你要读的东西，很简单，但是在网上读一些代码，我想处理这个问题...我为这种平庸向专家们道歉。

## 举例...

我们必须管理一个文本输入标签，并确保记住用户输入的值，并在某个地方显示它，直到它再次被更改！

我们如何在 React 中做到这一点？

我们插入一个`onChange`，它将组件的`state`更新为最后一个输入值...然后我们将`state`的值传递给输入标签，或者页面上的任何其他标签，以显示最后的输入值。

## 到码！

```
 //we've a class... with a state defined

class App extends Component {
  constructor(props){
    super(props)
    this.state={
      myFirstState:"Labrador"
    }

//we're going to manage the changes, and setState() equal to the user input

   valueChange = (e) => {
      this.setState({myFirstState : e.target.value});
    }

//rendering time...we've an Input tag, with the state as value 
//and the function, calling to onChange

render() {
    return (
        <div className="just a class">
          <input placeholder="insertValue" value={this.state.myFirstState} 
                 onChange= {this.valueChange(e)}/>
        </div>
       )
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是最基本的东西。
这只是一条可以追求的道路。

但是如果我们有不止一个`<input>`呢？
不是两个...不是三个...我们有十多个`<input>`在做**同样的**事情？
(这是对帖子有用的夸张说法)

如你所见，我们的`handleChange`函数改变了它的`setState()`中的特定状态...如果我们对其他`<input>`使用相同的函数，我们将每次改变`myFirstState`的值。
(相信我...我见过人们使用许多函数做同样的事情，就像这些。)

## 我的想法是...

```
 //we've a class... with a more states defined

class App extends Component {
  constructor(props){
    super(props)
    this.state={
      myFirstState:"Labrador",
      mySecondState:"Akita"
    }

//we're going to manage the changes, and setState() equal 
//to the user input... for all the possible "keys" in the state object

      valueChange = (key) => {
        return function (e) {
          var obj= {};
          state[key] : e.target.value;
          this.setState(obj);
        }.bind(this);
       }

//rendering time...we've more Input tags, with the states as values and one
//function, calling onChange... we're passing the "key" as argument.

render() {
    return (
        <div className="just a class">
          <input placeholder="insertValue" value={this.state.myFirstState} 
                 onChange= {this.valueChange("myFirstState")}/>
          <input placeholder="insertValue" value={this.state.mySecondState} 
                 onChange= {this.valueChange("mySecondState")}/>
        </div>
       )
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们调用方法`setState()`传递一个对象，其中`key`是我们想要改变的状态，值是用户输入！。
我们也在绑定`this`，否则我们会收到一个*错误*。

(请记住...`setState()`触发渲染)

我希望我对你有用...我正在寻找建议，以更有效的方式管理这种情况！

<center>![hug](../Images/f1be7541634f8836fed015de51315027.png)</center>

<center>Cya adventurers</center>