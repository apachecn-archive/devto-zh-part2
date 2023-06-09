# 专业提示:如何发现意外突变

> 原文：<https://dev.to/elgoorf/pro-tip-how-to-find-the-accidental-mutation-586h>

我加入了一个没有不变性库或中间件的 React 项目，但是现在做出这样的改变已经太远了，特别是考虑到它实际上从未引起任何问题，直到今天的这个例子。

如果我需要比简单的 [lodash.cloneDeep](https://lodash.com/docs/4.17.11#cloneDeep) 更多一点的控制，我通常使用`{...spread}`方法来克隆一个对象，并且记住这仅仅创建一个浅层克隆，沿着修改过的嵌套属性的树向下传播相同的方法(幸运的是这不会很深入)，例如:

```
// there are a dozen variations on how you might do this...

const modifiedObj = {
    ...originalObj,
    list: [
        ...originalObj.list,
        newItem,
    ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

不过在这种情况下，我在某个地方错过了一个祖先**，导致原始对象发生了变异，上下扫描这个 30 行的函数，我找不到变异发生在哪里。**

灵机一动从内心深处叫了一声:“`Object.freeze`！”。不，这不是建议冻结`this.props.myProperty`来防止变异(我只是假设这是一个非常糟糕的想法)，但是我可以把它作为一个临时的调试工具。

记住`Object.freeze`也只能浅尝辄止，我从 [Mozilla 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#What_is_Shallow_Freeze) :
中取出了这个`deepFreeze()`函数

```
// NB: you may need to apply "use strict" based on your project set-up

function deepFreeze(object) {

  // Retrieve the property names defined on object
  var propNames = Object.getOwnPropertyNames(object);

  // Freeze properties before freezing self

  for (let name of propNames) {
    let value = object[name];

    object[name] = value && typeof value === "object" ?
      deepFreeze(value) : value;
  }

  return Object.freeze(object);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在将这个函数应用于**在突变发生之前您想要调试的任何克隆对象的源**，浏览器控制台将方便地在意外尝试突变的确切位置抛出一个错误:

```
const onlyShallowCloned = { ...this.props.myProp};
deepFreeze(this.props.myProp);
someDeepManipulation(onlyShallowCloned); 
```

Enter fullscreen mode Exit fullscreen mode

很简单，当你踢自己的时候，现在很明显的冒犯行扇了你一巴掌:*啊，它一直就在那里！*。