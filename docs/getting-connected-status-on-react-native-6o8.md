# 在 React Native 上获取连接状态

> 原文：<https://dev.to/mutatedbread/getting-connected-status-on-react-native-6o8>

我们有时可能需要知道一个设备是否在 React Native 上连接到互联网。

# 医生说了什么

我们应该使用`NetInfo.isConnected.fetch()`来获得一个表示设备是否连接的布尔值。

# 事实上

直到目前最新的 v0.55，仍然可以观察到`NetInfo.isConnected.fetch()`只能在 Android 上正常工作。在 iOS 上倾向于返回`false`。这是一个已知的错误。

# 来自互联网的解决方案

*   使用事件监听器我们可以添加一个事件监听器，监听网络状态的变化。

```
onInitialNetConnection = (isConnected : boolean) => {
    console.log(`Is initially connected: ${isConnected}`);
    NetInfo.isConnected.removeEventListener('connectionChange', this.onInitialNetConnection);
};

constructor(props : Props) {
    super(props);
    NetInfo.isConnected.addEventListener(
        'connectionChange',
        this.onInitialNetConnection
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

*   Ping 一个网站在这种情况下，你们可以尝试 ping`google.com`

```
let req = await fetch('https://www.google.com');
let hasConnection = req.status === 200; 
```

Enter fullscreen mode Exit fullscreen mode

> 很丑，但是很管用。

*   用几次`NetInfo.isConnected.fetch()`？？？

在`< v0.55`上测试后，我发现在应用程序根组件的`componentWillMount`上多次调用`NetInfo.isConnected.fetch()`确实有效。

在`v0.55`上，这似乎停止工作。

```
¯\_(ツ)_/¯ 
```

Enter fullscreen mode Exit fullscreen mode