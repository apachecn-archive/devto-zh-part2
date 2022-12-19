# 更好地理解反应和渲染

> 原文：<https://dev.to/itwillwork/understand-react-better-4g50>

来回答这个问题。**为什么会出现渲染？**

[![example](../Images/ec26d032cec47d1dea6000cd75dbbb55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I9eQDnp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/15855766/47255109-5e687900-d474-11e8-86b1-38d732483959.png)

## 优点

*   直接用于一个组件，因此工作速度非常快

*   适用于 React 从 0.13.0 到 16.0.0 的所有版本

*   零依赖性

## 安装

```
npm install clear-render --save-dev 
```

您可以全局设置，以便在他们的所有项目中使用，而不必安装每个项目。

## 用法

包装要观察的组件。然而。

```
import clearRender from 'clear-render';

@clearRender
class SimpleComponent extends React.Component {

    ...

} 
```

或者

```
import clearRender from 'clear-render';
import OriginalSimpleComponent from './SimpleComponent';

const SimpleComponent = clearRender(OriginalSimpleComponent);

...

<SimpleComponent {...props} /> 
```

## 使用 CodePen 和 JSBin 作为 UMD 模块

在 html 区域

```
<script src="https://unpkg.com/clear-render@latest/build/umd/index.js"></script> 
```

在 js 区域使用来自全局范围
的`clearRender`

```
@clearRender
class SimpleComponent extends React.Component {

    ...

} 
```