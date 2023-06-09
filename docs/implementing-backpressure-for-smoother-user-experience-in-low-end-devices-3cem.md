# 在低端设备中实施背压以实现更流畅的用户体验

> 原文：<https://dev.to/avraammavridis/implementing-backpressure-for-smoother-user-experience-in-low-end-devices-3cem>

如果您正在构建使用实时数据的应用程序，您可能会遇到这样的情况:使用数据的组件或服务跟不上生成数据的数量或速度。系统的**生产者**模块发送数据的速度快于系统的**消费者**模块的处理速度。

消费者试图通过增加使用的系统资源(CPU、内存)来继续处理数据。这在系统资源不受限制的高端设备中可能没问题，但在低端设备中，这可能会导致电池耗尽或用户体验不流畅。

## 拉 VS 推策略

如果您设计的系统采用了拉取策略，在这种策略下，只要消费者觉得可以处理数据(或以指定的时间间隔),就会向生产者请求数据，大多数情况下，您可以通过增加两次数据拉取之间的时间间隔来解决这个问题。假设您有一个 web 应用程序，它每隔 50 毫秒向一个后端端点发送 GET 请求，并用一些漂亮的动画更新 UI。可能会出现更新 UI 的进程挂起的情况，因为它比请求和处理数据的进程慢。在这种情况下，我们可以增加时间间隔，例如增加到 200 毫秒，用户界面会不那么“实时”，但至少会更平滑。

```
setInterval(function(){
  axios.get('some-data-endpoint')
       .then(function(response){
           updateUI(response.data)
        })

}, 200) 
```

如果您的系统不是或不能基于拉策略，而是需要以推的方式运行，即生产者将数据推给消费者，那么您必须采取不同的方法来解决问题。想象一下您的 web 应用程序使用 websockets 的场景，其中服务器将实时事件(例如金融交易)推送到 UI。

```
socket.on('message', updateUI); 
```

在这些情况下，解决问题的方法通常是建立背压机制。

## 背压

反应宣言对背压有一个比我可能写的更好的定义:

> 当一个组件难以跟上时，整个系统需要以合理的方式做出响应。承受压力的组件发生灾难性故障或以不受控制的方式丢弃消息是不可接受的。因为它不能应付，也不能失败，所以它应该向上游组件传达它处于压力下的事实，从而让它们减少负载。这种反压力是一种重要的反馈机制，允许系统优雅地响应负载，而不是在负载下崩溃。背压可能会一路级联到用户，此时响应性可能会下降，但这种机制将确保系统在负载下具有弹性，并将提供允许系统本身应用其他资源来帮助分配负载的信息。

有两种实现背压的方法，我们必须根据我们的应用需求进行选择，即**无损策略**和**有损策略**。

## 无损耗 VS 有损耗

在有损策略中，我们可以跳过值，直到经过了一定的时间量，或者在事件(例如，鼠标点击)发生之后。在这种情况下，我们只详细说明最近的值，我们可以接受我们可能会丢失一些值的事实。当数据不重要时，这通常是公平的。

| 无损失策略 | 损耗策略 |
| --- | --- |
| 值被丢弃，永远不会出现在观察点。 | 值是成批堆叠和发出的。 |
| 示例:一段时间内采样的鼠标位置 | 示例:来自使用缓冲操作符的套接字的实时数据。 |
| 该应用程序正在使用最新的位置，并忽略以前的位置。 | 应用程序正在批量处理数据 |

## 举例

为了演示我们如何实现背压，我使用 RxJS 和 Websockets 创建了一个小例子。我们的虚拟应用程序正在连接一个远程套接字服务器，该服务器正在推送与加密货币价格相关的数据并更新 UI。首先让我们创建一个流:

```
function getStream(){
  const socket = io.connect('streamer.cryptocompare.com');
  const subscription = ['ID-1', 'ID-2'];
  socket.emit('SubAdd', { subs: subscription });

  return Rx.Observable.create(function(observer){
    socket.on('m', function(data){
      observer.next(data);
    })
  })
} 
```

然后我创建了一个简单的 react 组件，该组件订阅流并在消息到达时更新 UI:

```
class App extends Component {
  state = {
    messages: []
  };

  componentDidMount() {
    const stream$ = getStream();
    stream$.subscribe(m => {
      this.setState({
        messages: this
          .state
          .messages
          .concat(m)
      })
    })
  }

  ...
  ...

  render() {
    return (
      <ul>
        {
         this
          .state
          .messages
          .map(msg => <li key={msg.id}>{msg.label}</li>)
        }
      </ul>
    );
  }
} 
```

我运行了这个应用程序，并开始测量它的性能。你可以从下面的 gif 中看到，即使在我的高端设备中，当我试图滚动时，帧速率明显下降，UI 体验很糟糕:

[![](img/7f9ffc0a21afb9b01ad42000f8d64bc9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P1jYQKJq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q51n9llpgt0rm2n69n8h.gif)

## 使用背压

有各种各样的运算符可以帮助我们实现背压

*   样本()
*   throttleFirst()
*   缓冲区()
*   窗口()

让我们用大理石图来看看其中的一些。

#### 采样

在采样中，我们定期查看发射值的序列，并使用每个周期最后发射的值:

[![](img/3fc9effc2938307576ddce6b7f99cf83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--urLPkFPf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9evfs4qbkh0o5fmnkfhv.png)

采样是一种有损背压策略。

#### 节流优先

throttleFirst 与 sampling 相同，但是我们不使用最后发出的值，而是使用在指定时间段内发出的第一个值:

[![](img/01ef4d241c1c830c2420d874f25cb820.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vUEiFCgt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yej6sp708d7be24y7a3o.png)

throttleFirst 是一种有损背压策略。

#### 缓冲区

使用 buffer，我们可以创建一批发出的项目，然后消费者可以决定是只处理每个集合中的一个特定项目，还是处理这些项目的一些组合。

[![](img/8868f109bacd18be0ccb006cd08e1239.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oRBoYw5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sr9kp6lal11fscc80zeg.png)

缓冲是一种无损反压策略。

#### 窗口

通过 window，我们可以指定在关闭和发出批次之前要收集多少项目。

[![](img/561c858028609b5b654632a83ece2246.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cY8hFyf8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4gvkaudv7h4jto6whdlf.png)

窗口是一个无损失策略背压策略。

## 施加背压的例子

为了在我们的示例中应用背压，我们唯一要做的就是使用 sample 操作符添加采样:

```
class App extends Component {
  state = {
    messages: []
  };

  componentDidMount() {
    const stream$ = getStream();
    stream$.sample(500).subscribe(m => {
      this.setState({
        messages: this
          .state
          .messages
          .concat(m)
      })
    })
  }

  render() {
    return (
      <ul>
        {
         this
          .state
          .messages
          .map(msg => <li key={msg.id}>{msg.label}</li>)
        }
      </ul> 
    );
  }
} 
```

## 总结

背压是实现平滑用户体验的有用技术，即使对于没有强大设备的用户也是如此。不幸的是，大多数浏览器并不暴露用户机器的硬件特征(可能是出于隐私/安全原因)，所以作为开发人员，我们必须要么嗅探浏览器，然后猜测设备的功能，要么试图找到一个最佳点，为所有用户提供愉快的体验。