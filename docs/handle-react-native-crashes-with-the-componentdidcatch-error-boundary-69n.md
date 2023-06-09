# 使用 componentDidCatch 错误边界处理 React 本机崩溃

> 原文：<https://dev.to/lfkwtz/handle-react-native-crashes-with-the-componentdidcatch-error-boundary-69n>

想要保持最新状态？现在看看 React Native，双周刊 [React Native 简讯](https://reactnativenow.com/)

* * *

React 16 允许你[捕捉组件内部的 JavaScript 错误](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)，如果你意识到某个特定的组件由于这样或那样的原因很容易崩溃，这是非常好的。作为一种额外的保护措施，您还可以利用我们带有`componentDidCatch`的顶级父节点中的错误边界，以便您的用户可以在任何子节点/屏幕中出现意外错误时获得更好的体验。

在下面的例子中，我利用了一个本地警报和 [react-native-restart](https://www.npmjs.com/package/react-native-restart) 包，为用户提供了一个重启应用程序和从崩溃中恢复的简单方法。

```
componentDidCatch(error, info) {
    // to prevent this alert blocking your view of a red screen while developing
    if (__DEV__) {
        return;
    }

    // to prevent multiple alerts shown to your users
    if (this.errorShown) {
        return;
    }

    this.errorShown = true;

    Alert.alert(
        null,
        'An unexpected error has occurred. Please restart to continue.',
        [
            {
                text: buttonText,
                onPress: RNRestart.Restart,
            },
        ],
        { cancelable: false }
    );
} 
```

Enter fullscreen mode Exit fullscreen mode