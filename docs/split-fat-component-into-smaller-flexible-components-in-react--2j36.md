# 在反应中将脂肪成分分解成更小柔性成分

> 原文：<https://dev.to/malcolmkee/split-fat-component-into-smaller-flexible-components-in-react--2j36>

当编写 React 组件时，经常发生的一件事是，您编写的组件变成了一大块肥肉，导致它更难阅读和理解。

当这种情况发生时，建议你把大的部分分成几个小的部分，这样更容易理解。此外，较小的组件可以在其他地方重用。

有时，通过将您的代码移动到另一个组件中，如何做到这一点是非常明显的。但是有时候，分割代码的最佳方式并不那么明显。

例如，一个可能使拆分组件变得更复杂的场景是，您的子组件的`onClick`处理程序需要知道父组件的 id，当您有一些嵌套的数据结构时，这是一个常见的需求。

在这种情况下，奉承可以帮助你以一种干净的方式分割组件。

# 什么是阿谀奉承

Currying 是一种函数式编程技术，用于将接受多个参数的函数转换成一系列函数。

例如，典型的 add 函数和用法如下:

```
const add = (x, y) => {
    return x + y;
}
add(2, 3); // 5 
```

Enter fullscreen mode Exit fullscreen mode

使用 currying，add 函数可以重写如下:

```
const add = x => y => {
    return x + y;
}
add(2)(3); // 5 
```

Enter fullscreen mode Exit fullscreen mode

现在你明白了什么是 currying，让我们看看它如何帮助我们拆分组件。

# 示例介绍

为了帮助您理解 currying 可能会有所帮助，假设您有下面的数据结构。

```
data = [
    {
        id: 1,
        name: "Parent 1",
        sublist: [
            {
                id: 11,
                name: "Child 1",

            },
            {
                id: 12,
                name: "Child 2",

            }
        ]
    },
    {
        id: 2,
        name: "Parent 2",
        sublist: [
            {
                id: 21,
                name: "Child 3",

            },
            {
                id: 22,
                name: "Child 24",

            }
        ]
    }
]; 
```

Enter fullscreen mode Exit fullscreen mode

而最初的组件看起来是这样的:(我知道在这个例子中它没有那么大，只是想象一下数据结构更长，你需要在组件中显示更多的数据)

```
const FatComponent = ({ data }) => {
  const updateItem = (parentId, childId) => {
    someFunction(parentId, childId);
  };

  return data.map(parent => (
      <div>
        <span>name: {parent.name}</span>
        <div>
          {parent.sublist.map(item => (
            <div>
              <span>{item.name}</span>
              <button onClick={() => this.updateItem(parent.id, item.id)}>remove</button>
            </div>
          ))}
        </div>
      </div>
    ));
} 
```

Enter fullscreen mode Exit fullscreen mode

# 试图拆分组件

我们可以将组件拆分如下:

```
const FatComponent = ({ data }) => {
  const updateItem = (parentId, itemId) => {
    someFunction(parentId, childId);
  };

  return data.map(parent => <Parent updateItem={updateItem} {...parent} />);
};

const Parent = ({ id, name, sublist, updateItem }) => (
  <div>
    <span>{name}</span>
    <div>
      {sublist.map(item => <Item updateItem={updateItem} parentId={id} {...item} />)}
    </div>
  </div>
);

const Item = ({ name, id, updateItem, parentId }) => (
  <div>
    <span>{name}</span>
    <button onClick={() => updateItem(parentId, id)}>remove</button>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

然而，这种解决方案并不干净，因为它使`Item`与父组件紧密耦合，因为父组件必须将`updateItem`和`parentId`传递给`Item`组件。

理想情况下，`Item`应该接受`buttonOnClick`道具并将其附加到按钮`onClick`处理程序上，如下:

```
const Item = ({ name, id, buttonOnClick }) => (
  <div>
    <span>{name}</span>
    <button onClick={buttonOnClick}>remove</button>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

这将使项目更加灵活，更有可能被其他组件重用。

# 解——利用谄媚

通过使用 currying，我们将能够实现:

```
const FatComponent = ({ data }) => {
  const updateItem = parentId => itemId => () => {
    someFunction(parentId, itemId);
  };

  return data.map(parent => <Parent updateItem={updateItem(parent.id)} {...parent} />);
};

const Parent = ({ name, sublist, updateItem }) => (
  <div>
    <span>{name}</span>
    <div>
      {sublist.map(item => <Item buttonOnClick={updateItem(item.id)} parentId={id} {...item} />)}
    </div>
  </div>
);

const Item = ({ name, id, buttonOnClick }) => (
  <div>
    <span>{name}</span>
    <button onClick={buttonOnClick}>remove</button>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

干净又甜！

# 最后的话

尽管在 React 中编写代码并不需要函数式编程，但是，学习更多的函数式编程将有助于您编写更简洁、更好的 React 代码。

编码快乐！