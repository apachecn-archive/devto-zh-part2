# 优化 JavaScript 中的搜索

> 原文：<https://dev.to/dev3l/optimizing-search-in-javascript-2lca>

*[原载于 Skiplist 博客](https://www.skiplist.com/blog/optimizing-search-in-javascript)*

[Skiplist](https://www.skiplist.com/) 一路向下都是 JavaScript。

它就像一种僵尸病毒。语言已经接管了一切。我的手被咬了一口。我是拥抱我内心的 JavaScript kitty，变成我一直害怕的样子，还是应该趁有机会把它剁掉？

本周，我优化了内存缓存查找。客户数据集比预期的要大几个数量级。因此，我们必须重构内存缓存中的数据结构。

高速缓存的最初版本是通过 TDD 成对编程和驱动的。当我可以的时候，我喜欢拥抱我内心的敏捷技术教练。这可能只是巧合，但是很容易重构实现细节，这样查找现在可以在固定时间内发生。**优化技巧的一些技术细节描述如下。**

* * *

下面的例子可以在 GitHub 的[中找到。](https://github.com/DEV3L/node-kata/tree/master/search-optimization/src)

# 声明式编程

祈使句告诉我们如何做。声明性代码告诉我们什么。

我们来看一个例子，集合三个人的年龄:

```
const people = [
  {id: 1, name: "Jason", age: 38},
  {id: 2, name: "Justin", age: 34},
  {id: 3, name: "Josh", age: 33}
]

// imperative
const ages = []
for(let person of people) {
    ages.push(person.age);
}

// declarative 
const ages = people.map(person => person.age) 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 提供了一些内置的声明性帮助函数:

*   **[地图()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)**
*   **[减少()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)**
*   **[【过滤()】](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)**

*   **[找到()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)**

被灌输的声明性代码是有表现力的、优雅的、功能性的...[清洁](https://www.pluralsight.com/blog/software-development/7-reasons-clean-code-matters)。我同意，不关心香肠是如何制作的会让你更好地享受它！然而，有时候方法很重要。

# 使用查找来搜索一个值

如果您在一百万个条目的列表中通过 id 查找一个人，那么类似的场景会怎么样呢？

```
const idToFind = 1000000
person = people.find(person => person.id === idToFind); 
```

Enter fullscreen mode Exit fullscreen mode

上面的语句很干净，找到 id 为 1000000 的第一个人。相比之下，同样的线性搜索的强制性方法大约多了六行代码。简单就是牛逼。简单就是干净。但是，大(O)符号(“[大 O 符号](https://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/)”)告诉我们[线性搜索](http://www.bradoncode.com/blog/2012/04/big-o-algorithm-examples-in-javascript.html)是字面上的差。**我们牺牲性能来换取整洁，这是我个人在 99.8%的时候会选择的一种折衷。**#移情编程

如果键是惟一的，并且我们的数据集具有可管理的大小，我们可以通过将人员列表转换成按 id 排列的人员图来提高性能，然后对 id 执行散列查找 O(1 )!更糟糕的是，我们有一次 O(n)排列步骤，然后在每个记录上进行 O(1)查找。

# 代码示例

作为[软件工艺](http://manifesto.softwarecraftsmanship.org/)的好例子，让我们从一个失败的 [JavaScript 单元测试](https://codeburst.io/javascript-unit-testing-using-mocha-and-chai-1d97d9f18e71)开始，来断言预期的行为。

```
const assert = require('assert');
const Search = require("./search");

describe('Search', function () {
  const people = [];

  before(() => {
    people.push({id: 1, name: "Jason", age: 38});
    people.push({id: 2, name: "Justin", age: 34});
    people.push({id: 3, name: "Josh", age: 33});
  });

  it('should return the correct element', function () {
    const expectedName = "Justin";
    const search = new Search(people);

    const person = search.find(2);

    assert.equal(expectedName, person.name);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 命令式

此时，我们有一个红色测试。让我们实现第一种方法，使用 for 循环的传统命令式搜索。

```
class Search {
  constructor(people) {
    this.people = people;
  }

  find(id) {
    for(let person of this.people) {
      if(person.id === id) {
        return person;
      }
    }
  }
}

module.exports = Search; 
```

Enter fullscreen mode Exit fullscreen mode

我设置了一个测试工具来评估性能。

```
// performance output:
// >>> Average time for find for 3 records: 0.001 ms
// >>> Total time for find for 3 records: 2 ms
// >>> Average time for find for 1000000 records: 2.617 ms
// >>> Total time for find for 1000000 records: 2906 ms 
```

Enter fullscreen mode Exit fullscreen mode

### 陈述性的

我们有一个断言行为的绿色测试和一个性能测试工具，我们可以自由地在机舱中移动(*重构`find`方法*的内部)！**从祈使句转移到陈述句看起来像:**T5】

```
// ...

  find(id) {
    return this.people.find(person => person.id === id);
  }

// ... 
```

Enter fullscreen mode Exit fullscreen mode

```
// performance output:
// >>> Average time for find for 3 records: 0.001 ms
// >>> Total time for find for 3 records: 2 ms
// >>> Average time for find for 1000000 records: 2.356 ms
// >>> Total time for find for 1000000 records: 2690 ms 
```

Enter fullscreen mode Exit fullscreen mode

我们在一百万条记录上的搜索时间相对没有变化。随着从命令式到声明式的转变，我们获得了代码的整洁。代码现在以这样一种方式“告诉什么”,即换入不同的数据结构，如地图，更容易概念化。我们的认知负荷减少了。

### 优化

最后，如果我们在一个大型集合的嵌套循环中执行这个搜索会怎么样呢(这个*永远不会发生*！)?在数百条记录的搜索中，每条记录的搜索时间只有两个半毫秒，这很容易降低客户体验。那么，让我们来看看使用地图的例子。JavaScript 中的[数组是一个关联数组，所以我们可以很容易地将 id`map`作为对象的键。](https://www.w3schools.com/js/js_arrays.asp) 

```
class Search {
  constructor(people) {
    const peopleMap = [];
    people.forEach(person => peopleMap[person.id] = person);
    this.people = peopleMap
  }

  find(id) {
    return this.people[id]
  }
}

module.exports = Search;

// performance output:
// Average time for find for 3 records: 0.001 ms
// Total time for find for 3 records: 2 ms
// Average time for find for 1000000 records: 0 ms
// Total time for find for 1000000 records: 302 ms 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

我觉得我对 JavaScript 的问题不是不喜欢。我讨厌我喜欢它。我对浏览器标准化(IE6 ~2005 w/ ActiveX)之前的 JavaScript web 开发记忆犹新。我尊重它目前在开发社区中的地位，并期待在解决方案的每一层找到一个通用的平台选项。