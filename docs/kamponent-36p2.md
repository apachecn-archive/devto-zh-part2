# komponent

> 原文：<https://dev.to/alirezavalizade/kamponent-36p2>

如果你喜欢这个库，请点击 github 上的开始按钮。
[https://github.com/cooldrip/komponent](https://github.com/cooldrip/komponent)

# [营员](#kamponent)

用于创建 react 组件的组件🏃

### 此库尚未发布

### 安装

npm:

```
npm install kamponent --save 
```

Enter fullscreen mode Exit fullscreen mode

纱线:

```
yarn add kamponent 
```

Enter fullscreen mode Exit fullscreen mode

### 例 1

```
<Kamponent pure initialState={{ count: 0 }}>
    {({ state, setState }) => {
        return (
            <div>
                <h3>{state.count}</h3>
                <button onClick={() => setState({ count: state.count + 1 })}>+</button>
                <button onClick={() => setState({ count: state.count - 1 })}>-</button>
            </div>
        );
    }}
</Kamponent> 
```

Enter fullscreen mode Exit fullscreen mode

### 例 2

```
<Kamponent>
    {({ props }) => {
        return (
            <div>
                <h3>{props.count}</h3>
                <button onClick={() => props.increment()}>+</button>
                <button onClick={() => props.decrement()}>-</button>
            </div>
        );
    }}
</Kamponent> 
```

Enter fullscreen mode Exit fullscreen mode

### 道具

| 财产 | 类型 | 工作 |
| --- | --- | --- |
| 儿童 | 函数、数组或对象 | 是 |
| 初始状态 | 目标 | 是 |
| 纯的 | 弯曲件 | 是 |
| 下车 | 功能 | 是 |
| 抓住了 | 功能 | 是 |
| 将卸载 | 功能 | 是 |
| 应该更新 | 功能 | 是 |
| 更新了 | 功能 | 是 |
| 更新前快照 | 功能 | 是 |

### 函数中的参数

###### 注意:您可以从函数和子函数(如果是函数)中访问该参数。

| 财产 | 类型 | 工作 |
| --- | --- | --- |
| 状态 | 任何的 | 是 |
| 小道具 | 任何的 | 是 |
| setState(设置状态) | 功能 | 是 |
| 强制更新 | 功能 | 是 |