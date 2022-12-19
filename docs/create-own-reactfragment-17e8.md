# 🗄️做出自己的反应。碎片

> 原文：<https://dev.to/iamandrewluca/create-own-reactfragment-17e8>

你想在你的应用程序中使用 [`React.Fragment`](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html) 吗？
你正在使用比`16.2`更低的不支持片段的 React 版本吗？
由于某种原因，您无法更新 React 来支持它？

我可以告诉你，你可以创造自己的`Fragment`！

```
function Fragment(props) {
  return props.children;
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，就是这么简单。

```
import React from 'react';
import ReactDOM from 'react-dom';

function Fragment(props) {
  return props.children;
}

function App() {
  return (
    <Fragment>
      <div>We</div>
      <div>have</div>
      <div>own</div>
      <div>Fragments</div>
      <div>!!!</div>
    </Fragment>  
  )
}

ReactDOM.render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想玩的话，这里有一个演示。

注意，使用了不支持`Fragment`的 React `16.1`

同样，如果你看一下`Preact` `Fragment`的实现，你会看到我们上面写的函数。

[https://github . com/pre actjs/pre act/blob/da 382 和 13d 9377 a 53056 e 4 CB 0 FD 741 F6 e 0 aadf 1c 1/src/create-element . js # l92-l94](https://github.com/preactjs/preact/blob/da382e13d9377a53056e4cb0fd741f6e0aadf1c1/src/create-element.js#L92-L94)

封面照片由[张秀坤大镰刀](https://unsplash.com/@drscythe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/fragment?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄