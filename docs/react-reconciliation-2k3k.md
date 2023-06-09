# 反应和解

> 原文：<https://dev.to/susickypavel/react-reconciliation-2k3k>

# 简介

React 是一个非常棒的库，用于创建用户界面。谈到 react 应用的性能，我首先想到的是虚拟 DOM，或者更好的协调过程。这个概念将告诉我们 react 如何更新 [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) 。

## **和解**

> 协调是 React 更新 DOM 的过程。

作为开发人员，我们正在创建组件树，react 然后获取这棵树，处理它，我们得到一个虚拟 DOM，它保存在内存中。当我们的应用程序中有更新时(例如状态或道具的改变)，react 将获取更新的虚拟 DOM 并将其与旧的虚拟 DOM 进行比较，然后决定应该改变什么以及如何改变。这一过程又重复了一遍。

虚拟 DOM 和“真实”DOM 之间的同步版本也由诸如 **ReactDOM** 这样的库提供服务。

React 在比较这些树时需要非常快，因此它使用复杂度为 O(n)的启发式算法，因此这意味着对于 1000 个节点，我们需要 1000 次比较。

> *使用这种方法代替[最先进的算法](https://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf)，其复杂性为 O(n^3) = >对于 1000 个节点，我们需要 10 亿次比较。*

### 然而这种方法有两个重要的假设:

*   *两种不同类型的元素会产生不同的树。* **(称为成对差分)**

所以用这个规则，区别于:

```
render() {
    return(
        <div>
            <h1>Hello World!</h1>
            <p>Welcome.</p>
        </div>
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

致:

```
render() {
    return(
        <span>
            <h1>Hello World!</h1>
            <p>Welcome.</p>
        </span>
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

将销毁 **div** 元素及其所有子元素，并用 h1 和 paragraph 创建一个新的 **span** 。

*   开发者可以用一个关键道具来提示哪些子元素在不同的渲染中是稳定的。 **(称为列表式差异)**

使用 react 时，我们都会遇到这种情况。这在迭代数组时很常见，我们返回某种 JSX 作为响应。

```
renderFlags() {
    return ["Private", "Property"].map((flag) => {
        return <p key={flag}>{flag}</p>;
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

key 属性对于 react 来说非常重要，它可以指示树中的哪些子元素发生了变化，哪些子元素保持不变。然而，有一点很重要。

*   key 属性不应该填充索引参数(或任何不一致的源)，因为这会导致性能问题。

```
renderFlags() {
    // First render.
    ["Private", "Property"].map((flag, index) => {
        return <p key={index}>{flag}</p>;
    });

    // Second Render
    ["Public", "Private", "Property"].map((flag, index) => {
        return <p key={index}>{flag}</p>;
    });

    /*
        First render returns:
        <p key={0}>Private</p>
        <p key={1}>Property</p>

        Second render returns:
        <p key={0}>Public</p>
        <p key={1}>Private</p> ! Key changed from 0 to 1
        <p key={2}>Property</p> ! Key changed from 1 to 2
    */
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果我们在数组的开头添加一个元素，它会移动其余元素的索引。所以 react 会让所有这些孩子变异。

> 这个我真的不好解释，但是官方[文档](https://reactjs.org/docs/reconciliation.html)里有一个很好的解释。

## **反应纤维**

随着 react 16 出现了新的协调引擎。它提高了 react 应用程序的性能，并有许多精彩的功能，但概念有点复杂，所以如果你想自己了解它，有一个由安德鲁·克拉克写的很好的[帖子](https://github.com/acdlite/react-fiber-architecture)。

## 来源

另一个值得一读的好资料来源:

*   [https://reactjs.org/docs/faq-internals.html](https://reactjs.org/docs/faq-internals.html)
*   [https://reactjs.org/docs/reconciliation.html](https://reactjs.org/docs/reconciliation.html)
*   [https://hackernoon.com/virtual-dom-in-reactjs-43a3fdb1d130](https://hackernoon.com/virtual-dom-in-reactjs-43a3fdb1d130)
*   [https://css-tricks.com/how-react-reconciliation-works/](https://css-tricks.com/how-react-reconciliation-works/)