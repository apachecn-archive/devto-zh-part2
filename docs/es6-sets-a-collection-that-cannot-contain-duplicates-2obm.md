# ES6 设置一个不能包含重复项的集合

> 原文：<https://dev.to/jrohatiner/es6-sets-a-collection-that-cannot-contain-duplicates-2obm>

通过向构造函数
传递一个数组，在 ES6 中创建一个集合

```
let set = new Set([1, 2, 3, 3, 4, 5, 5, 5, 6]);

console.log(set.size); // 6 
```

Enter fullscreen mode Exit fullscreen mode

我传入的数组包含重复项。但是这个集合基本上去掉了它们，留下了一个 6 个唯一项的集合

```
let set = new Set();

set.add(1);
set.add('two');

console.log(set.size); // 2 
```

Enter fullscreen mode Exit fullscreen mode

最后，还有 has()方法，非常有用。该方法允许您检查一个项目是否存在

```
console.log(set.has(1)); // true
console.log(set.has('two')); // true
console.log(set.has(3)); // false 
```

Enter fullscreen mode Exit fullscreen mode

[在 jsbin 上试试](https://jsbin.com/poqoyoh/edit?html,js,console,output)