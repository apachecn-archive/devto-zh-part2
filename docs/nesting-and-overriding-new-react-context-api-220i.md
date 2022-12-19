# 嵌套和覆盖新的 React 上下文 API

> 原文：<https://dev.to/iamandrewluca/nesting-and-overriding-new-react-context-api-220i>

在学习 react-router v4 的时候，我读了一些他们的源代码。正如我们所知，他们使用当前上下文来传递路由器和路由信息，覆盖先前/父路由信息

## ![GitHub logo](../Images/04febf72106d337f3956ebfaf9121dae.png) [反应训练](https://github.com/ReactTraining) / [反应路由器](https://github.com/ReactTraining/react-router)

### React 的声明性路由

<article class="markdown-body entry-content" itemprop="text">

[![react-router](../Images/08ac25ab0b926ef02fb659a00e77a870.png)T2】](https://reacttraining.com/react-router/)

### 反应路由器

[反应](https://facebook.github.io/react)的声明性路由

[![](../Images/ff5ac13061650f0974ba01104c773300.png)](https://www.npmjs.com/package/react-router)[![](../Images/efd4bb034c13c2bf4f405a93dd6ee5ba.png)](https://www.npmjs.com/package/react-router)[![](../Images/3fd72b5cb1205860d3b5cd4a92d84052.png)](https://travis-ci.com/ReactTraining/react-router)

## 文件（documents 的简写）

**[在这里查看单据](https://reacttraining.com/react-router)**

[从 2.x/3.x 迁移？](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router/docs/guides/migrating.md)

[3.x 文档](https://github.com/ReactTraining/react-router/blob/v3/docs)

[2.x 文档](https://github.com/ReactTraining/react-router/blob/v2.8.1/docs)

## 包装

这个存储库是我们使用 [Lerna](https://github.com/lerna/lerna) 管理的 monorepo。这意味着我们实际上从同一个代码库向 npm 发布了[几个包](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages)，包括:

| 包裹 | 版本 | 文件（documents 的简写） | 描述 |
| --- | --- | --- | --- |
| [T2`react-router`](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router) | [![npm](../Images/2bcdb5ce21a6054dcd6b8ab4a801bdff.png)T2】](https://www.npmjs.com/package/react-router) | [![](../Images/e2d6c6f636cade7f6831395edb457ba1.png) ](https://reacttraining.com/react-router/core/guides/quick-start) [ ![](../Images/929f0a47f5e402b516340ce6804620c8.png)](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router/docs) | React 路由器的核心 |
| [T2`react-router-dom`](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router-dom) | [![npm](../Images/9d54420ee7730a0b1dafb6f0637560e7.png)T2】](https://www.npmjs.com/package/react-router-dom) | [![](../Images/e2d6c6f636cade7f6831395edb457ba1.png) ](https://reacttraining.com/react-router/web/guides/quick-start) [ ![](../Images/929f0a47f5e402b516340ce6804620c8.png)](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router-dom/docs) | React 路由器的 DOM 绑定 |
| [T2`react-router-native`](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router-native) | [![npm](../Images/761a1a380ae38474e2b88a4221f2ec55.png)T2】](https://www.npmjs.com/package/react-router-native) | [![](../Images/e2d6c6f636cade7f6831395edb457ba1.png) ](https://reacttraining.com/react-router/native/guides/quick-start) [ ![](../Images/929f0a47f5e402b516340ce6804620c8.png)](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router-native/docs) | [React 路由器的 React 本机](https://facebook.github.io/react-native/)绑定 |
| [T2`react-router-config`](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router-config) | [![npm](../Images/96afb8b04af8f65389f8c245aa7f8394.png)T2】](https://www.npmjs.com/package/react-router-config) | [![](../Images/19cd8184ebbcc7d23ab43d3333d19a9a.png)T2】](https://raw.githubusercontent.com/ReactTraining/react-router/master//packages/react-router-config/#readme) | 静态路由配置助手 |

> **Redux 用户:**[`react-router-redux`包](https://github.com/ReactTraining/react-router/tree/5345a820818c8d43ac923558670538a479ac2234/packages/react-router-redux)现已弃用。参见 [Redux Integration](https://reacttraining.com/react-router/web/guides/redux-integration) 了解更好的方法。

## 变化

给定版本的详细发布说明可以在我们的发布页面上找到[。](https://github.com/ReactTraining/react-router/releases)

版本 4 之前的变更说明请参见[changes . MD 文件](https://github.com/ReactTraining/react-router/blob/8365d4b2e91084265a1da7de911766169fb9e96e/CHANGES.md)。

## 提供资金

您可以通过开放集体捐赠[，参加](https://opencollective.com/react-router)[我们的一个研讨会](https://reacttraining.com/workshops/)，或者购买[我们的一个在线课程](https://reacttraining.com/courses/)，为这个项目提供资金支持。谢谢你的…

</article>

[View on GitHub](https://github.com/ReactTraining/react-router)

```
getChildContext() {
  return {
    router: {
      ...this.context.router,
      route: {
        location: this.props.location || this.context.router.route.location,
        match: this.state.match
      }
    }
  };
} 
```

React 团队宣布了新的上下文 API，在 React v16.3.0 中不再被弃用，该 API 已经发布:)

[https://github.com/facebook/react/releases/tag/v16.3.0](https://github.com/facebook/react/releases/tag/v16.3.0)

现在我在想，重新训练将如何使用新的上下文 API 使这种重写成为可能。从一开始，我就用**创建-反应-上下文**聚合填充新的上下文。它的工作原理完全相同，只需更改导入即可。

```
import { render } from "react-dom";
import React, { createContext } from "react";
// import createContext from "create-react-context"; 
```

接下来，我们需要创建上下文。上下文有一个**消费者**和一个**提供者**

```
const { Provider, Consumer } = createContext(); 
```

Provider 用于将`value` prop
中的一些数据传递给他

```
render() {
    return (
        <Provider value={"React is Awesome!"}>
            nested content...
        </Provider>
    )
} 
```

并且消费者习惯于使用渲染道具
来消费那个`value`

```
render() {
    return (
        <Consumer>
            {(theValue) => {
                return theValue
            }}
        </Consumer>

        // shorthand
        <Consumer>
            {theValue => theValue}
        </Consumer>
    )
}

// output
// React is Awesome! 
```

我们可以使用**消费者**多少次。

现在回到我们的首要任务。这是我的应用程序

```
const App = () => (
  <Provider value={{ location: "/" }}>
    <NestedPath>
      <NestedPath location="haha/">
        <NestedPath>
          <NestedPath>
            <NestedPath>
              <NestedPath />
            </NestedPath>
          </NestedPath>
        </NestedPath>
      </NestedPath>
    </NestedPath>
  </Provider>
);

ReactDOM.render(<App />, document.getElementById("root")); 
```

这是输出结果

```
/
/location/
/location/haha/
/location/haha/location/
/location/haha/location/location/
/location/haha/location/location/location/ 
```

这是我的**嵌套路径**组件

```
const NestedPath = ({ location = "location/", children }) => (
    <Consumer>
        {router => (
            <React.Fragment>
                <div>{router.location}</div>
                <Provider value={{ ...router, location: router.location + location }}>
                    {children || null}
                </Provider>
            </React.Fragment>
        )}
    </Consumer>
); 
```

正如您在 **Provider** 中看到的，我们用一个新值覆盖了之前的值。所有儿童**消费者**现在将接受新的价值。

这里有一个沙盒可以玩

[https://codesandbox.io/s/lrvv8w784q](https://codesandbox.io/s/lrvv8w784q)

感谢阅读！！！这是我的媒介故事的复制品！
dev.to 是开发者的新媒介:)