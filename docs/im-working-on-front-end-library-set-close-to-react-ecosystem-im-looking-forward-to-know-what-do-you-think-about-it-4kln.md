# 我在做前端库集(接近 react 生态系统)。我很期待知道你对它的看法！

> 原文：<https://dev.to/int0h/im-working-on-front-end-library-set-close-to-react-ecosystem-im-looking-forward-to-know-what-do-you-think-about-it-4kln>

### 简介

一段时间以来，我一直致力于我的宠物项目，名为**超价值**。它是一组旨在帮助你开发网络应用程序的库。

### 项目的要旨

主要思想是提供一个简单的包装器来包装 javascript 中的任何值。包装器可用于在组件之间共享数据、更新数据并对这些更新做出反应。

### 代码示例

这里是用**超值** :
编写的一个最简单的应用程序的完整代码

```
import {HyperValue} from 'hyper-value';
import {jsx, Component} from 'hv-jsx';
import {renderIn} from 'hv-dom';

class App extends Component<{}> {
    count = new HyperValue(0);
    render() {
        return <div>
            <span>Click amount: {this.count}</span>
            <button onClick={() => this.count.$++}>Click me!</button>
        </div>;
    }
}

renderIn(document.body, {}, <App />); 
```

Enter fullscreen mode Exit fullscreen mode

### 状态

在这一点上，我想得到来自社区的反馈。有人(除了我)认为这是一个合理的想法吗？

### 链接

可以在这里找到详细教程:[https://medium . com/@ int0h/hyper-value-living-data-in-your-application-a 54 aab 68d 8 b 1](https://medium.com/@int0h/hyper-value-living-data-in-your-application-a54aab68d8b1)

库:
超值:[https://github.com/int0h/hyper-value](https://github.com/int0h/hyper-value)T3】HV-jsx:[https://github.com/int0h/hv-jsx](https://github.com/int0h/hv-jsx)T6】HV-DOM:[https://github.com/int0h/hv-dom](https://github.com/int0h/hv-dom)

### 演示

柜台申请:[https://github.com/int0h/hv-counter-app](https://github.com/int0h/hv-counter-app)
待办事宜申请:[https://github.com/int0h/hv-todo-list](https://github.com/int0h/hv-todo-list)T5】异步演示:[https://github.com/int0h/hv-async-app](https://github.com/int0h/hv-async-app)

### P.S

我不确定这里是否适合发表这样的文章。如果你能告诉我其他获得反馈的方法，我会很高兴的！