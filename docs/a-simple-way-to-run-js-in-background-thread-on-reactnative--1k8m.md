# React Native 上在后台线程中运行 JS 的简单方法

> 原文：<https://dev.to/craftzdog/a-simple-way-to-run-js-in-background-thread-on-reactnative--1k8m>

CPU 密集型任务阻塞 UI，如索引。因为，在 React Native 中，JavaScript 是在 JavaScriptCore 上执行的，这意味着你只有一个线程。所以你必须使用像 [react-native-workers](https://github.com/devfd/react-native-workers) 这样的本地模块，它提供了与 [web workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 类似的 API。但是如果你只是想在后台运行一个简单的任务，这是一种超高速的方法。我不喜欢在我的应用程序中安装很多本机模块，因为它们会使应用程序更加复杂和脆弱。如果你有 expo app，它就不能使用原生模块。

我发现我们已经有现成的后台线程了。也就是 [WebView](https://facebook.github.io/react-native/docs/webview.html) 。你可以通过调用`injectJavaScript`方法在里面运行 JavaScript。在 webview 内部，它是 Safari(iOS)/Chrome(Android)的另一个实例，所以在其中运行的 JS 根本不会屏蔽 app UI。我通过运行下面的代码在两个平台上检查了这一点:

```
for (;;) { Math.random() * 9999 / 7 } 
```

这是有用的。不用安装原生模块就可以在后台线程中运行代码！这里有一个例子:

```
import React, { Component } from 'react'
import { WebView } from 'react-native'

export default class BackgroundTaskRunner extends Component {
  render() {
    return (
      <WebView
        ref={el => this.webView = el}
        source={{html: '<html><body></body></html>'}}
        onMessage={this.handleMessage}
      />
    )
  }
  runJSInBackground (code) {
    this.webView.injectJavaScript(code)
  }
  handleMessage = (e) => {
    const message = e.nativeEvent.data
    console.log('message from webview:', message)
  }
} 
```

要获得代码的结果，您可以为您的 webview 指定`onMessage` prop。
webview 调用`window.postMessage`时调用的功能。设置该属性会将一个`postMessage`全局变量注入到您的 webview 中，但仍会调用预先存在的`postMessage`值。
`window.postMessage`接受一个参数`data`，该参数可用于`event`对象`event.nativeEvent.data`。`data`必须是字符串。

只需在 webview 上调用:

```
const message = { ok: 1 }
window.postMessage(message) 
```

然后你在应用程序上得到消息:

```
message from webview:, { ok:1 } 
```

就是这样！😄