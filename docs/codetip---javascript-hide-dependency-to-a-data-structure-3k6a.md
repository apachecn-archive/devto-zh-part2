# 代码提示- Javascript:隐藏对数据结构的依赖

> 原文：<https://dev.to/avraammavridis/codetip---javascript-hide-dependency-to-a-data-structure-3k6a>

很多时候，我们会遇到这样的情况，我们的代码依赖于复杂的数据结构或具有隐式语义的数据结构。例如，假设我们创建了一个图表，我们必须使用的数据是来自服务器的响应(我们不能改变响应的结构)。来自服务器的响应如下:

```
[
 ["2018-12-02", 1000], 
 ["2018-11-02", 900], 
 ["2018-10-02", 200], 
 ["2018-09-02", 300]
] 
```

这个结构有一个隐含的含义，它是一个数组的数组，每个数组有两个值，第一个代表日期，第二个代表值。最简单的方法是在我们的代码中直接使用这种结构，比如:

```
class Chart {
  constructor(data){
    this.data = data;
  }

  createDomains(){
    x.domain(d3.extent(this.data, d => d[0])); // date
    y.domain(d3.extent(this.data, d => d[1])); // value
  }

  createLine(){
    d3.line()
      .x(d => d[0]) // date 
      .y(d => d[1]); // value
  }

  formatDate(){
    this.data.forEach(d => {
      d[0] = parseTime(d[0]); // date
    });
  }
} 
```

这种方法的问题是，如果服务器决定改变值的顺序，或者在数组的第一个或第二个位置引入一个新的值，我们破坏的代码，比如说结构改变为:

```
[
 ["Dinos", "2018-12-02", 1000], 
 ["Nikos", "2018-11-02", 900], 
 ["Petros", "2018-10-02", 200], 
 ["Giannis", "2018-09-02",  300]
] 
```

现在我们的类被破坏了，它的每一个方法都被破坏了。我们必须更新每一种方法来解决这个问题。更好的方法是不依赖于响应的结构。

```
class Chart {
  constructor(data){
    this.data = data.map(dataPoint => ({
      date: dataPoint[0],
      value: dataPoint[1],
    }));
  }

  createDomains(){
    x.domain(d3.extent(this.data, d => d.date));
    y.domain(d3.extent(this.data, d => d.value));
  }

  createLine(){
    d3.line()
      .x(d => d.date)
      .y(d => d.value)
      .values(this.data) 
  }

  formatDate(){
    this.data.forEach(d => {
      d.date = parseTime(d.date);
    });
  }
} 
```

如果服务器决定改变传递的数据结构，我们将不得不只在一个地方更新我们的代码，即构造函数，而不改变其他方法。