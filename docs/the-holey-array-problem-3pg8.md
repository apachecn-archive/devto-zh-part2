# 多孔阵列问题

> 原文：<https://dev.to/voodooattack/the-holey-array-problem-3pg8>

我最讨厌的 JavaScript“特性”之一是“多孔”数组。如果你不确定那是什么，考虑下面的:

```
const array = [1, 2, 3]; 
```

Enter fullscreen mode Exit fullscreen mode

这就是所谓的“打包”阵列。元素是连续的，数组由一种元素类型组成:`number`。

> 在 C++方面:当使用 V8(又名 Node.js)时，这个数组实际上存储为`PACKED_SMI_ELEMENTS`，这是一种在内存中存储小整数的方法，也是 V8 存储数组的无数方法中最有效的。

现在考虑这一行无害的代码:

```
array.push(3.14); // push a floating point number to the array. 
```

Enter fullscreen mode Exit fullscreen mode

> 在 C++方面:你的数组刚刚在内存中从`PACKED_SMI_ELEMENTS`(整数)转换为`PACKED_DOUBLE_ELEMENTS`(双精度)。它变得略有不同，但仍然紧密包装和高性能。这种转变是不可逆转的。

JavaScript 方面没有任何变化。

进入下一步:

```
array.push('Hello world!'); // push a string to the array 
```

Enter fullscreen mode Exit fullscreen mode

> 在 C++方面:你的数组已经被不可逆转地再次转换了。这次是从`PACKED_DOUBLE_ELEMENTS`到`PACKED_ELEMENTS`。一个`PACKED_ELEMENTS`数组可以保存任何 JavaScript 值；但是与 SMI 或双阵列相比，必须牺牲更多的存储空间来表示自身。

现在让我们继续下一行代码:

```
console.log(array.length); // 5
array[9] = true;
console.log(array.length); // 10 
```

Enter fullscreen mode Exit fullscreen mode

这在 JavaScript 中是允许的吧？你可以给数组中的任意一个索引赋值，你的数组将被填充。那么在 C++端会发生什么呢？

> 在 C++方面:你的数组又一次被不可逆地转换了，这次是到`HOLEY_ELEMENTS`。工作起来要慢得多。你已经使 V8 的 JIT(实时编译器)优化变得更加困难，因为它将无法在很大程度上优化你的程序。
> 
> 值得注意的是，调用`new Array(n)`或`Array(n)`总是会创建这种类型的数组并降低代码速度。

但是为什么要在这里停下来呢？我来介绍一下撒旦的特殊数据结构:

```
array[999] = 'HAIL SATAN! ♥' 
```

Enter fullscreen mode Exit fullscreen mode

> 在 C++方面:你的数组刚刚从`HOLEY_ELEMENTS`转变为`DICTIONARY_ELEMENTS`，你召唤了一个无法再被驱逐的恶魔。
> 
> 我直接引用 [V8 源代码](https://cs.chromium.org/chromium/src/v8/src/elements-kind.h?g=0&l=31)吧:
> 
> ```
>  // The "slow" kind.
>   DICTIONARY_ELEMENTS, 
> ```

从 JavaScript 的角度来看:你的数组只是变成了一个字典，或者换句话说:一个普通的对象。JavaScript 数组字面上的*最坏情况场景*。

### 为什么这是危险的:

*   这样的操作会悄无声息的成功，永远不会抛出错误。
*   任何形式的基于循环的枚举或序列化的尝试都很可能会使您的服务器崩溃。
*   数组的键将被悄悄地转换成字符串。
*   数组仍将序列化为数组，而不是对象。(`JSON.stringify`将尝试使用`null` s 填充所有空索引)
*   对于`DICTIONARY_ELEMENTS`数组，`Array.isArray(array)`将返回 true。

如果您尝试在上面的数组中调用`JSON.stringify`，您将得到:

```
[1,2,3,3.14,"Hello world!",null,null,null,null,true,null,null,null,null,null,null,null,null,null,null,null,null,null,...,null,null,null,null,"HAIL SATAN! ♥"] 
```

Enter fullscreen mode Exit fullscreen mode

### 这如何能用来对付你:

考虑下面这个 REST API 使用 express 操作 todo 列表的例子:

```
// Naïve example of holey array potential vulnerability

class Todos {

  constructor(username, items) {
    this.username = username;
    this.items = items || Todos.load(username);
  }

  // add a new todo
  add(todo) {
    this.items.push(todo);
    return this.items.length - 1;
  }

  // update an existing todo
  update(index, todo) {
    // index is expected to be an integer
    // we're making the mistake of accepting an arbitrary/unbounded index here though
    // this operation will succeed silently, and node won't throw any errors with a huge index.
    // e.g. if an attacker passes 10000000, the program won't crash or show signs of instability, the array will silently become "DICTIONARY_ELEMENTS".
    this.items[index] = todo;
    return index;
  }

  remove(index) {
    return this.items.splice(index, 1);
  }

  // another common case:
  // you're keeping a list of todos and want to give the user the ability to reorder items.
  swap(i1, i2) {
    const temp = this.items[i1];
    this.items[i1] = this.items[i2];
    this.items[i2] = temp;
  }

  // load a list of the user's previously saved todos
  // we’re not using a database for simplicity’s sake
  static load(username) {
    const userPath = path.join('data', this.username + '.json');
    if (fs.existsSync(userPath) {
      return JSON.parse(fs.readFileSync(userPath, 'utf8'));
    }
    return [];
  }

  // this saves the array back to disk as JSON when the request is ending
  // holey/dictionary arrays with absurd indices will pad empty ranges with `null`.
  // this could result a multi-gigabyte file if passed a holey/dictionary array with a big enough (sparse) index in them. Most likely we’ll run out of memory first because the resulting string will be too big.
  save() {
    fs.writeFileSync(path.join('data', this.username + '.json'), JSON.stringify(this.items));
  }

}

app.use((req, res, next) => {
  // initialise/load previous todos
  req.todos = req.todos || new Todos(req.session.username);
  next();
});

// add new todo
app.post('/todos/new', (req, res, next) => {
  if (req.body.payload)
    res.json({ index: req.todos.add(req.body.payload) });
  else
    res.status(500).json({ error: 'empty input' });
});

/// update existing todo (vulnerable to unbound indices!)
app.post('/todos/:idx/update', (req, res, next) => {
  if (req.body.payload)
    res.json(req.todos.update(parseInt(req.params.idx, 10), req.body.payload));
  else
    res.status(500).json({ error: 'empty input' });
});

…

// save current todo list after request
// a better idea is to override res.end() via a thunk though.
app.use((req, res, next) => {
  next();
  req.todos.save();
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个恶意请求的例子:`POST /todos/10000000/update payload="hi"`

您现在在内存中有一个看不见的问题(10000000 元素字典数组)，当请求结束时，它会尝试写出一个巨大的 JSON 文件，或者您的服务器会在尝试将数组序列化为字符串时耗尽内存。

### 关于 V8 内部的进一步阅读:

[https://V8 project . blogspot . com/2017/09/elements-kinds-in-V8 . html](https://v8project.blogspot.com/2017/09/elements-kinds-in-v8.html)
T3】https://v8project.blogspot.com/2017/08/fast-properties.html