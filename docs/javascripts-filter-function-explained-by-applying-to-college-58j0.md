# 通过申请大学解释 JavaScript 的过滤功能

> 原文：<https://dev.to/kbk0125/javascripts-filter-function-explained-by-applying-to-college-58j0>

如果你熟悉大学申请流程，那么你就能理解 JavaScript 的过滤功能。

与 JavaScript 中的 map()和 reduce()方法相比，filter()方法可能有一个最简单的名字。

你输入一个数组，然后过滤掉满足特定条件的元素，放入一个新的数组。

这似乎很简单，但我似乎总是发现自己又回到了 for()循环。因此，我决定寻找一种更好的方法来理解 filter()函数是如何工作的。

我意识到过滤功能有点像大学招生官。他们使用一组参数来决定哪些学生应该被他们特定的大学录取。是的，我们都希望大学能更灵活一点，从整体上评判我们的成绩，但在现实中，大多数大学仍然有关于 SAT、ACT 和 GPA 分数的硬性数字来决定谁会被考虑。

让我们开始吧！

[![](img/0e7b77afb3e672983ab7fa25e5588076.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XUSiuE1---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.codeanalogies.com/wp-content/uploads/2018/05/filterOverviewDiagram.png)

### 使用 For 循环代替过滤功能

好吧，假设我们有 4 个学生的名字和平均绩点。这所特殊的大学只想录取平均绩点 3.2 或更高的学生。下面是你可以如何做。

```
let students = [
  { 
    name: "david", 
    GPA: 3.3 
  }, 
  { 
    name: "sheila", 
    GPA: 3.1 
  }, 
  { 
    name: "Alonzo", 
    GPA: 3.65 
  }, 
  { 
    name: "Mary", 
    GPA: 3.8 
  }
] 

let admitted =[]; 

for (let i=0; i < students.length; i++){ 
  if(students[i].gpa > 3.2) 
    admitted.push(students[i]); 
} 

/*admitted = [
  { 
    name: "david", 
    GPA: 3.3 
  }, 
  { 
    name: "Alonzo", 
    GPA: 3.65 
  }, 
  { 
    name: "Mary", 
    GPA: 3.8 
  }
];*/ 
```

Enter fullscreen mode Exit fullscreen mode

哇！这比需要的要复杂得多。如果有人阅读您的代码，他们需要跟踪多个数组，以了解您只是将一个数组过滤到另一个数组中。而且，你需要小心地跟踪 *i* 以避免任何错误。让我们学习如何使用 filter 方法来完成同样的事情。

### 使用 Filter()方法

让我们学习如何用 filter()方法实现同样的目标。

1.  Filter 是一个数组方法，所以我们将从学生数组开始。
2.  它使用在数组中的每个元素上运行的回调函数。
3.  它使用一个 return 语句来显示哪些元素实际上会出现在最终的数组中，在本例中是被录取的学生。

```
let students = [
  { 
    name: "david", 
    GPA: 3.3 
  }, 
  { 
    name: "sheila", 
    GPA: 3.1 
  }, 
  { 
    name: "Alonzo", 
    GPA: 3.65 
  }, 
  { 
    name: "Mary", 
    GPA: 3.8 
  }
] 

let admitted = students.filter(function(student){
   return student.gpa > 3.2;
})

/*admitted = [
  { 
    name: "david", 
    GPA: 3.3 
  }, 
  { 
    name: "Alonzo", 
    GPA: 3.65 
  }, 
  { 
    name: "Mary", 
    GPA: 3.8 
  }
];*/ 
```

Enter fullscreen mode Exit fullscreen mode

输入和输出是相同的，所以我们做了不同的事情:

1.  我们不需要声明被接受的数组，然后再填充它。我们声明它，然后用相同代码块中的元素填充它
2.  我们实际上在 return 语句中使用了一个条件！这意味着我们只返回通过特定条件的元素。
3.  我们现在可以对数组中的每个元素使用 *student* ，而不是像在循环的*中那样使用 students[i]。*

[![filterDiagramV1.jpg](img/01df13c5facfbc103a0138d4203afdd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x8ep4Mol--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/IDqqk2Y7RvC5mkYGwom6)

[![filterDiagramV2.jpg](img/1029db9f0d7453b8e4da761290f8afd8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GAVSts20--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/Bb3Z8tQiRpal46iYJJqT)

你可能会注意到有一点是违反直觉的——考上大学是最后一步，但是在我们的代码中，变量*录取*是语句的第一部分！您可能通常期望找到最后一个数组作为函数中的最后一条语句。相反，我们使用 return 来表示哪些元素将在*中被承认*结束。

[![filterCodeBlock1.jpg](img/e15352ce73852692ec1b49420b256da3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u6ch9m2B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/CJFC1S8VShCCAjcNCLqK)

### 例 2-在过滤器内使用两个条件

到目前为止，我们在过滤方法中只使用了一个条件。但那根本不代表大学录取过程！通常，招生官员会考虑 10 个以上的因素。

我们来看两个因素——GPA 和 SAT 成绩。学生必须 GPA 在 3.2 以上，SAT 成绩在 1900 以上。下面是相同函数的样子。

```
let students = [
  {
    name: "david",
    GPA: 3.3,
    SAT: 2000
  },
  {
    name: "sheila",
    GPA: 3.1,
    SAT: 1600
  },
  {
    name: "Alonzo",
    GPA: 3.65,
    SAT: 1700
  },
  {
    name: "Mary",
    GPA: 3.8,
    SAT: 2100
  }
]

let admitted = students.filter(function(student){
   return student.gpa > 3.2 && student.SAT > 1900;
})

/*admitted = [
  {
    name: "david",
    GPA: 3.3,
    SAT: 2000
  },
  {
    name: "Mary",
    GPA: 3.8,
    SAT: 2100
  }
];*/ 
```

Enter fullscreen mode Exit fullscreen mode

看起来很相似，对吧？现在我们在 *return* 语句中有两个条件。但是让我们进一步分析一下代码。

```
let admitted = students.filter(function(student){
   let goodStudent = student.gpa > 3.2 && student.SAT > 1900
   return goodStudent;
}) 
```

Enter fullscreen mode Exit fullscreen mode

啊哈！因此，与循环的*相比，这是另一个重要的区别。如果你检查 goodStudent 变量，你可以看到它只会评估为*真*或*假*。然后，该布尔值被输入到 return 语句中。*

所以， *true* 或 false 真的只是决定了原始数组的每个成员是否会被包含在结果数组中，*承认了*。

[![twopartfilter1.jpg](img/6ab99929d4ddc817cf25c68428cbc23e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TBoz5YY---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/ly7otC19QNasdN29wBQF)

[![twopartfilter2.jpg](img/84b92e80cf0e50d7f55f1a858488f9df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rvZJIxEy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/zYB4yi8ZSmKSu1loFM92)

### 获取更多可视化教程

你喜欢这个教程吗？你可能也会喜欢我在 [CodeAnalogies 博客](https://codeanalogies.com)上对 web 开发主题的其他可视化解释。