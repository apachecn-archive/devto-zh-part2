# 并行迭代

> 原文：<https://dev.to/hoodwink73/concurrent-iteration-3c57>

迭代的概念直观上似乎是同步的——当迭代完成时，我们将准备好我们的结果。

有许多原生数组方法可以帮助我们迭代数组。

假设我们有一组学生的详细信息。每个学生都有一个字段说明他们的出生日期，现在我们要计算他们的年龄。

```
const ageOfStudents = studentDetails.map(({dob}) => calculateAgeFromDOB); 
```

Enter fullscreen mode Exit fullscreen mode

那辆车将马上为我们准备好。

`calculateAgeFromDOB`是同步操作。所以，我们会一个接一个地严格计算每个学生*的年龄。*

 ***但是如果要应用到每个学生的操作没有同步解决怎么办**。

比方说，我们需要每个学生的年度表现记录。并且学生的每个年度表现记录是一个网络请求。

```
const studentPerformanceRecordsPromises = studentDetails  
  .map(({id}) => getPerformanceRecordOfStudent(id)); 
```

Enter fullscreen mode Exit fullscreen mode

每次迭代都会产生一个并发的任务。并且这些任务将按照它们自己的任意顺序解决。

我们必须等待性能记录*，甚至在迭代完成*之后。这是普通迭代和并发迭代的关键区别。

如果`getPerformanceRecordOfStudent`返回一个在成功的网络请求后解决的承诺，`studentPerformanceRecordsPromises`将是一个承诺数组。

我们可以使用`Promise.all`来等待一系列的承诺。

```
Promise.all(studentPerformanceRecordsPromises)  
   .then(doSomethingWithPerformanceRecordsOfAllStudents) 
```

Enter fullscreen mode Exit fullscreen mode

因为我们正在对比同步和异步迭代，所以最好有一个我们的`Array.map`的异步副本。

我们会像这样使用它

```
Promise  
.map(studentDetails, getPerformanceRecordOfStudent)  
.then(doSomethingWithPerformanceRecordsOfAllStudents) 
```

Enter fullscreen mode Exit fullscreen mode

下面是`Promise.map`的一个简单定义，看起来像

```
if (!Promise.map) {  
 Promise.map = function(vals,cb) {  
  return Promise.all(  
   vals.map( function(val){  
      // we are expecting \`cb\` to return a promise 
      // even if it does not we are converting it in to 
      // a promise using \`Promise.resolve\` 
      return Promise.resolve(cb(val))  
   } )  
  );  
 };  
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这个想法是在我阅读 [@getify](https://twitter.com/getify) 的 [YDKJS](https://github.com/getify/You-Dont-Know-JS) 时*衍生出来的*。特别是这个部分，被恰当地命名为 [*并发迭代*](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md#concurrent-iterations) 。

我挠了一会儿头。所以想到分享我的*解析*理解。也许这能帮助一些人。

感谢阅读。

我是 YDKJS 的超级粉丝。强烈推荐！

解决你和 JavaScript 的关系。*对不起*！*