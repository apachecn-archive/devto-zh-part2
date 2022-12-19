# 初学者指南:记忆

> 原文：<https://dev.to/milkstarz/a-beginners-guide-memoization-22f0>

这篇文章最初发表在[malikbrowne.com](https://www.malikbrowne.com/blog)上。

上周，我在浏览不同的文章，寻找 React v16.3 中新生命周期方法的指南。我看到了[这篇文章](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)，它讨论了许多开发人员可能如何错误地使用`getDerivedStateFromProps`。

如果您不熟悉 React，该方法只是允许组件更新其内部状态，作为其属性变化的结果。然而，这篇文章建议**而不是**做一些我在代码中会一直做的事情:

使用`getDerivedStateFromProps`或`componentWillReceiveProps`确保当输入改变时，组件只为重新渲染执行昂贵的计算。

然而，一种更简单、更简洁的方法是使用一种叫做[记忆化](https://en.wikipedia.org/wiki/Memoization)的函数式编程技术。

作为一个对性能感兴趣的成长中的程序员，我喜欢接触新的函数式编程技术，它们有助于加速我每天编写的代码。记忆化是我在不同的算法问题中听其他工程师谈论过的东西。然而，我从来没有花时间去看所有的宣传到底是怎么回事——主要是因为这听起来真的很复杂。

在这篇文章中，我将解释什么是纯函数，记忆化是如何工作的，以及如何在 React 组件中组合它们来提高代码的性能。

让我们从谈论**纯函数**开始。

[![let's get started](img/d504edf6bf8c432e193bea23c572ead3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZqJx-BlL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.gifer.com/KQ73.gif)

### 什么是纯函数？

根据定义，纯函数是满足以下标准的函数:

1.  这是一个函数，如果传入相同的参数，**总是**返回相同的结果。
2.  该功能不会对您的应用产生任何**可观察到的副作用**，包括:
    *   网络请求
    *   数据突变
    *   记录到文件
    *   更改应用程序状态
3.  这是一个只访问您传递给它的数据的函数，使得依赖关系很容易定义。

这篇文章中的一个类比可能有助于这个想法的实现，这篇文章将纯函数比作咖啡研磨机。

> 一个纯粹的函数就像一个咖啡研磨机:豆子进去，粉末出来，故事结束。

#### 好处

纯函数有几个好处，其中两个是:

1.  它们可以产生更多的说明性程序，描述不同的输入和输出之间的关系。
2.  它们可以增加代码的可测试性，让调试代码不再是一场噩梦。

然而，值得注意的是**副作用，一般来说，并不坏** -这意味着我们不必使每一个函数都是纯的。

#### 纯函数的例子

假设我们有一个递归函数，它返回一个数字 :
的[阶乘](https://www.mathsisfun.com/numbers/factorial.html)

```
const factorial = n => {
     if (n === 1) {
          return n;
      }
    return n * factorial(n - 1)
}

// factorial(4)
// 4! === 4 * 3 * 2 * 1 === 24 
```

Enter fullscreen mode Exit fullscreen mode

如果我们传入`factorial(4)`，我们将进行计算并返回结果，每次都是 24，**。**

 **既然我们现在知道一个纯函数每次都会返回相同的值，如果我们的函数能够记住(或缓存)我们的结果，岂不是很方便？这样，下次有人想计算`factorial(100)`，我们可以节省时间和资源，只需给他们已经存储的答案。

我的朋友们，这就是记忆。

### 什么是记忆化，真的？

根据定义，

> 记忆化是一种优化技术，主要用于通过**存储昂贵的函数调用**的结果并在相同的输入再次出现时返回缓存的结果来加速程序。

通俗地说，这意味着如果你给它同样的问题，这个函数会记住问题的答案。为了实现一个简单的内存化解决方案，我们可以以映射的形式实现某种类型的缓存，这样我们的函数就可以引用它。

下面是我们的阶乘解在记忆函数下的样子:

```
// our original factorial function
const factorial = n => {
    if (n === 1) {
         return n;
     }
   return n * factorial(n - 1)
}
// a memoized function used to calculate our factorial
const scopedMemoizedFactorial = () => {
  const fakeCache = {};
  return (value) => {
    if (value in fakeCache) {
      // return the value from our fake cache
      return fakeCache[value];
    }
    else {
      // calculate our factorial
      const result = factorial(value);
      fakeCache[value] = result;
      return result;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 注意事项

*   `scopedMemoizedFactorial`返回稍后调用的函数。我们可以在 JavaScript 中这样做，因为函数是第一类对象，这意味着我们可以将它们用作高阶函数并返回另一个函数。
*   `fakeCache`可以记住这些值，因为它是在闭包中实现的
*   这是因为我们使用的函数是纯函数，就像我们之前讨论的那样。如果它没有返回相同的值，我们的缓存就不会为输出返回正确的值！

如果你想看一个通用记忆化函数的例子，看看斯托扬·斯特凡诺夫的`JavaScript Patterns`中的[这篇文章](https://gist.github.com/dannycallaghan/6897874)展示了一个记忆化模式。

### 在 React 中使用记忆

对于我们的例子，假设我们有一个第三方 API，它返回一些关于我们应用程序中所有用户的 JSON。数据结构看起来像这样:

```
[
    {
        name: "Malik",
        age: 24,
        company: "Meetup",
        // ...and a bunch of other fields like this
    },
    // ...and 996 other entries just like this
] 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看看整个数据集是什么样子，请点击[这个链接](http://www.json-generator.com/api/json/get/bVHdtPdtmG?indent=2)。(为此感谢 [JSON 生成器](https://www.json-generator.com)！)

我们的应用程序的要求是创建一个搜索框，它将过滤我们的用户列表，并返回名称与查询匹配的所有用户的排序列表。

没有记忆的代码应该是这样的:

```
class App extends React.PureComponent{
  state = {
    searchValue: ""
  };

  filterList = (list, searchValue) =>
    list.filter(member => member.name.toLowerCase().startsWith(searchValue));

  sortList = list =>
    list.sort((a, b) => {
      if (a.name < b.name) return -1;
      if (a.name > b.name) return 1;
      return 0;
    });

  handleInputChange = searchValue => {
    this.setState({ searchValue, inputChanged: true });
  };

  render() {
    const { searchValue, inputChanged } = this.state;
    const filteredMembers = this.filterList(data, searchValue);
    const members = this.sortList(filteredMembers);

    return (
      <div className="App">
        <h1>No Memoization Example</h1>
        <Search
          searchValue={searchValue}
          onInputChange={e => this.handleInputChange(e.target.value)}
          placeholder="Search for a member"
        />
        <div className="members">
          {members.map(member => {
            return <Member member={member} key={member._id} />;
          })}
        </div>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

点击查看运行中的代码[。](https://codesandbox.io/embed/ymlk2mmq39?module=%2Fsrc%2Fno-memoize.js&moduleview=1)

这种解决方案在大多数情况下都能很好地工作，但是对于大量的数据，应用程序会变慢很多。

发生这种情况有两个原因:

*   过滤大量数据是一项昂贵的操作
*   应用程序的其他重新呈现将导致该函数再次调用昂贵的操作。

使用助手`memoize-one`，我们可以很容易地将记忆添加到这个例子中:

```
import memoize from 'memoize-one';

class App extends React.PureComponent {
  state = {
    searchValue: ""
  };

  filterList = memoize((list, searchValue) =>
    list.filter(member => member.name.toLowerCase().startsWith(searchValue))
  );

  sortList = memoize(list =>
    list.sort((a, b) => {
      if (a.name < b.name) return -1;
      if (a.name > b.name) return 1;
      return 0;
    })
  );

  handleInputChange = searchValue => {
    this.setState({ searchValue });
  };

  render() {
    const { searchValue } = this.state;
    const filteredMembers = this.filterList(data.slice(0, 50), searchValue);
    const members = this.sortList(filteredMembers);

    return (
      <div className="App">
        <h1>With Memoization Example</h1>
        <Search
          searchValue={searchValue}
          onInputChange={e => this.handleInputChange(e.target.value)}
          placeholder="Search for a member"
        />
        <div className="members">
          {members.map(member => {
            return <Member member={member} key={member._id} />;
          })}
        </div>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`memoize-one`很棒，因为它只存储了*最后一次*函数调用的结果，所以你不必担心[缓存破坏问题](https://www.keycdn.com/support/what-is-cache-busting/)。

### 性能重要注意事项

记忆化的想法很棒，但是记住记忆化的主要好处:存储昂贵的函数调用的结果。

我采用我们的阶乘解决方案，并使用[性能时间线 API](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) 来计时我们的函数调用花费的时间(精确到微秒):

```
// we use performance.now() to keep track of how long each call takes
const tick = () => performance.now();
const t0 = tick()

optimizedFactorial(5000); // calculated
const t1 = tick();
console.log(`The first call took ${t1 - t0}ms.`);
// The first call took 0.3999999971711077ms.

optimizedFactorial(5000); // cached
const t2 = tick();
console.log(`Our memoized call took ${t2 - t1}ms.`);
// Our memoized call took 2.2000000026309863ms.

optimizedFactorial(4999); // calculated again with different param
const t3 = tick();
console.log(`A call that wasn't stored in our cache took ${t3 - t2}ms.`);
// A call that wasn't stored in our cache took 0.3999999971711077ms 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，在我的电脑上，记忆调用花费了**五倍于**的时间来得到相同的结果。这是因为，为了让我们的记忆技术工作，计算机需要为一个新变量分配内存并实例化它，这分别需要一大块时间才能执行计算。

因此，我们可以看到，在这个解决方案中使用 memoize 技术是一种不成熟的优化——并且会对我们应用程序的性能产生负面影响。

另一点需要注意的是，这种解决方案不能解决与“破坏”缓存相关的许多问题，包括:

*   设置最大年龄或尺寸
*   我们缓存的排除

这两种痛苦都会导致应用程序中的内存泄漏，这对于调试来说是一场噩梦。正因为如此，许多工程师倾向于使用已经实现了解决这些常见问题的方法的记忆助手。其中包括:

*   [memoize-one](https://github.com/alexreardon/memoize-one)
*   [Lodash 的记忆功能](https://github.com/lodash/lodash/blob/master/memoize.js)

关于 React 中的记忆化，这篇 [React 博文](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)涵盖了一些主要的限制。由于他们用了类似的例子，我在下面分享一下:

> 1.  In most cases, you will want **to attach a memorized function to a component instance** . This can prevent multiple instances of a component from resetting each other's memory keys.
> 2.  To prevent memory leakage over time, you may also want to use a memory assistant, which has **a limited cache size** .
> 3.  If `props.members` is re-created every time the parent component is rendered, all the implementations shown in this section will not work. But in most cases, this setting is appropriate.

### 结论

记忆化是一项令人敬畏的技术，如果使用得当，可以增强您的应用程序。使用更多的函数式编程技术可以产生更容易、更可预测的代码，并且具有很高的可测试性。

我强烈推荐您通过一个名为 [memoize-one](https://github.com/alexreardon/memoize-one) 的包在您的应用程序中尝试记忆化。

如果你对本文中的任何概念有任何疑问，欢迎在评论中提问！

我一直欢迎来自开发社区的人的意见，所以也可以随时在 Twitter 上联系我。说说你对用记忆化来表现的看法！

下一集见。**