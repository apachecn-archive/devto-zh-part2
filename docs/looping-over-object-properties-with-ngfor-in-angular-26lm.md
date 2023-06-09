# 使用 ngforangular 循环对象属性

> 原文：<https://dev.to/papaponmx/looping-over-object-properties-with-ngfor-in-angular-26lm>

这篇文章最初发表在[媒体](https://medium.com/@papaponmx/looping-over-object-properties-with-ngfor-in-angular-869cd7b2ddcc)上。

在 Angular 中用 ngFor 循环对象属性
你好，这是我在工作中发现的一个有趣的问题。

如果你还不知道。我说的是棱角分明的 2+。仅供参考:这里有一个帖子是关于为什么它只是有角的。

问题是

所以你想显示一个列表的标记，这个列表的值来自后端，由于某种原因，你收到的不是一个很好的对象数组，而是类似这样的东西。

```
{ 
  "car" : 
    { 
       "color" : "red",
       "model" : "2013"
    },
   "motorcycle": 
    { 
       "color" : "red",
       "model" : "2016"
    },
   "bicycle": 
    { 
       "color" : "red",
       "model" : "2011"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您尝试使用`*ngFor`,但是出现了一条错误消息:

找不到类型为“object”的不同支持对象“[object Object]”。NgFor 仅支持绑定到可重复项，如数组。你可以在后端修复它，这样你就可以得到一个对象数组，但是没有人有时间这么做。别担心孩子，我有我们。

解决方案

在一个完美的世界中，你会得到一个对象数组，因为世界并不总是完美的。你要做的是把所有这些对象存储在一个数组中。这是一个用普通的旧 JavaScript 编写的过于简化的解决方案。

第一步。获取所有对象键。这个方法返回一个给定对象自己的可枚举属性的数组。

第二步。创建一个空数组。这是一个所有属性都将存在的地方，因为你的新的`ngFor`循环将指向这个数组，我们必须捕获它们。

第三步。迭代抛出所有的键，并将每个键放入你创建的数组中。

这是它在代码中的样子。

```
// Evil response in a variable. Here are all my vehicles.
let evilResponse = { 
  "car" : 
    { 
       "color" : "red",
       "model" : "2013"
    },
   "motorcycle": 
    { 
       "color" : "red",
       "model" : "2016"
    },
   "bicycle": 
    { 
       "color" : "red",
       "model" : "2011"
    }
}
// Step 1\. Get all the object keys.
let evilResponseProps = Object.keys(evilResponse);
// Step 2\. Create an empty array.
let goodResponse = [];
// Step 3\. Iterate throw all keys.
for (prop of evilResponseProps) { 
    goodResponse.push(evilResponseProps[prop]);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以将`goodResponse`分配给您最初尝试遍历的类属性。

这是所有的乡亲。