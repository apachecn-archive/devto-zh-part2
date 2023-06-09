# 节点事件发射器

> 原文：<https://dev.to/flaviocopes/the-node-event-emitter-1108>

如果您在浏览器中使用过 JavaScript，您就会知道有多少用户交互是通过事件来处理的:鼠标点击、键盘按键、对鼠标移动的反应等等。

在后端，Node 为我们提供了使用 [`events`模块](https://nodejs.org/api/events.html)构建类似系统的选项。

这个模块特别提供了`EventEmitter`类，我们将使用它来处理我们的事件。

你用
来初始化它

```
const eventEmitter = require('events').EventEmitter() 
```

Enter fullscreen mode Exit fullscreen mode

这个对象公开了`on`和`emit`方法。

*   `emit`用于触发一个事件
*   `on`用于添加事件触发时将要执行的回调函数

例如，让我们创建一个`start`事件，为了提供一个示例，我们只需登录到控制台:
就可以对其做出反应

```
eventEmitter.on('start', () => {
  console.log('started')
}) 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行

```
eventEmitter.emit('start') 
```

Enter fullscreen mode Exit fullscreen mode

事件处理函数被触发，我们得到控制台日志。

通过将参数作为附加参数传递给`emit()` :
，可以将参数传递给事件处理程序

```
eventEmitter.on('start', (number) => {
  console.log(`started ${number}`)
})

eventEmitter.emit('start', 23) 
```

Enter fullscreen mode Exit fullscreen mode

多个参数:

```
eventEmitter.on('start', (start, end) => {
  console.log(`started from ${start} to ${end}`)
})

eventEmitter.emit('start', 1, 100) 
```

Enter fullscreen mode Exit fullscreen mode

EventEmitter 对象还公开了其他几个与事件交互的方法，比如

*   `once()`:添加一次性监听器
*   `removeListener()` / `off()`:从事件中删除事件监听器
*   `removeAllListeners()`:删除事件的所有监听器

你可以在[https://nodejs.org/api/events.html](https://nodejs.org/api/events.html)的活动模块页面上阅读所有详细信息