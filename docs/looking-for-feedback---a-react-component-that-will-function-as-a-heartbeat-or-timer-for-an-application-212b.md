# 寻找反馈——React 组件将作为应用程序的“心跳”或计时器。

> 原文：<https://dev.to/sayes2x/looking-for-feedback---a-react-component-that-will-function-as-a-heartbeat-or-timer-for-an-application-212b>

我以前从未见过 React 这样使用，所以我很好奇其他开发者会怎么想。

这里是要点:[https://gist . github . com/sayes 2 x/72d 884 c 415662701 e 38 c 08 b 0d 696 c 768](https://gist.github.com/sayes2x/72d884c415662701e38c08b0d696c768)

这个组件是由@ Squeegy 开发的“精确间隔”的 React 实现，可以在这里找到:[https://gist.github.com/Squeegy/1d99b3cd81d610ac7351](https://gist.github.com/Squeegy/1d99b3cd81d610ac7351)。Squeegy 的函数是这样工作的:在每次调用 setTimeout 之后，一个函数会比较调用 setTimeout 的时间和应该调用 setTimeout 的时间。对 setTimeout 的下一次调用进行调整，使其尽可能接近所需的时间间隔。

该组件接受两个属性:

1.  heartbeatInterval:提供一个以毫秒为单位的时间间隔，心跳组件将在这个定时器间隔内重复调用一个函数，只要它被渲染。该时间间隔的默认值是 1000 毫秒。
2.  heartbeatFunction:每次 heartbeatInterval 到期时都会调用的函数。

该组件不呈现任何内容，所有呈现函数返回的都是片段。该组件唯一做的事情是在设定的时间间隔调用父组件中的函数。

我是这样使用它的:

1.  在父组件中，我有一个布尔值状态，在这种情况下，该状态称为暂停:

```
this.state = {
  paused: true
} 
```

1.  在父组件的 render 函数中，在返回之前，我有一个三元运算符，如果 paused 为 true，则将变量设置为 null，如果 paused 为 false，则将变量设置为 Heartbeat 组件。在这种情况下，我使用 heartbeatInterval 属性的默认值 1000 毫秒:

```
const timer = this.state.paused === true ? 
  null : 
  <Heartbeat heartbeatFunction={this.countdown} />; 
```

1.  在父组件的 render 函数的 return 部分，我呈现了步骤 2 中定义的变量，以及我希望该组件呈现的任何其他内容:

```
return (
  <Fragment>
  {timer}
  <OtherComponents or HTML tags />
  </Fragment> ) 
```

我喜欢这个实现，因为我所要做的就是在组件的状态中改变一个布尔值来打开或关闭计时器。因为我以前没有见过以这种方式实现的组件，所以我在寻找其他开发人员的反馈。

要查看项目中使用的组件，请转到[https://github . com/sayes 2 x/pomodoro-clock/tree/master/src/components](https://github.com/sayes2x/pomodoro-clock/tree/master/src/components)
该组件位于 heartBeat.js 文件中，并在 timer.js 文件中使用