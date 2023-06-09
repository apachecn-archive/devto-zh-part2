# 为什么不使用 setInterval

> 原文：<https://dev.to/akanksha_9560/why-not-to-use-setinterval--2na9>

> 最近，我遇到一个需求，我必须在特定时间间隔后重复调用一个函数，比如每 10 秒发送一次 ajax 调用。当然，最好的选择似乎是`setInterval`，但是它像饼干一样炸了我的脸:)

为了理解为什么 setInterval 是邪恶的，我们需要记住一个事实，javascript 本质上是单线程的，这意味着它不会一次执行多个操作。

如果函数花费的时间比`setInterval`中提到的延迟更长(比如 ajax 调用，这可能会妨碍按时完成)，我们会发现要么函数没有喘息的空间，要么 setInterval 打破了它的节奏。

```
 var fakeCallToServer = function() {
        setTimeout(function() {
            console.log('returning from server', new Date().toLocaleTimeString());
        }, 4000);
    }

    setInterval(function(){ 

        let insideSetInterval = new Date().toLocaleTimeString();

        console.log('insideSetInterval', insideSetInterval);

        fakeCallToServer();
    }, 2000);

//insideSetInterval 14:13:47
//insideSetInterval 14:13:49
//insideSetInterval 14:13:51
//returning from server 14:13:51
//insideSetInterval 14:13:53
//returning from server 14:13:53 
//insideSetInterval 14:13:55
//returning from server 14:13:55 
```

Enter fullscreen mode Exit fullscreen mode

*在你的控制台上尝试上面的代码片段*

从打印的 console.log 语句中可以看到,`setInterval`继续无情地发送 ajax 调用，而不关心前一个调用是否返回。
这可以在服务器上一次排队很多请求。

现在，让我们在`setInterval` :
中尝试同步操作

```
var counter = 0;

var fakeTimeIntensiveOperation = function() {

    for(var i =0; i< 50000000; i++) {
        document.getElementById('random');
    }

    let insideTimeTakingFunction  = new Date().toLocaleTimeString();

    console.log('insideTimeTakingFunction', insideTimeTakingFunction);
}

var timer = setInterval(function(){ 

    let insideSetInterval = new Date().toLocaleTimeString();

    console.log('insideSetInterval', insideSetInterval);

    counter++;
    if(counter == 1){
        fakeTimeIntensiveOperation();
    }

    if (counter >= 5) {
       clearInterval(timer);
    }
}, 1000);

//insideSetInterval 13:50:53
//insideTimeTakingFunction 13:50:55
//insideSetInterval 13:50:55 <---- not called after 1s
//insideSetInterval 13:50:56
//insideSetInterval 13:50:57
//insideSetInterval 13:50:58 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里看到，当`setInterval`遇到时间密集型操作时，它会做两件事，a)试图回到正轨或 b)创造新的节奏。在 chrome 上，它创造了一种新的节奏。

### 结论

在异步操作的情况下，`setTimeInterval`将会创建长的请求队列，这将会适得其反。
在时间密集型同步操作的情况下，`setTimeInterval`可能会打破节奏。
同样，如果 setInterval 代码块出现错误，它不会停止执行，而是继续运行错误代码。更不用说他们需要一个`clearInterval`函数来阻止它。
或者，在时间敏感操作的情况下，您可以递归使用`setTimeout`。