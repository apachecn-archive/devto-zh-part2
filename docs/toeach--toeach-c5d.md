# 给每个||！每个人

> 原文：<https://dev.to/mcarpenter/toeach--toeach-c5d>

你好，如果我用吸引人的标题吸引了你，那是有原因的！我想告诉你关于下划线。下划线是一个充满有用的函数式编程助手的库。我将通过`.forEach`作为例子。如果你没有听说过下划线，我想现在就向你介绍它！

“下划线有助于您编写清楚表达您意图的代码”

## 什么意思？

让我们以`arr.forEach`为例，它执行一次提供的函数`.forEach`数组元素。

*举个例子*

```
const arr = [1,2,3];
arr.forEach(e => console.log(e));
//1, 2, 3 
```

Enter fullscreen mode Exit fullscreen mode

如果我们能像说的那样写出来岂不是很棒？我们来看看下面的`_.each`例子。对数组中的每个元素执行 logger 函数。

```
//_.each(element, index, list)
const arr = [1,2,3];

function logger(ele){
  console.log(ele)
}
_.each(arr,logger)
//1,2,3 [1,2,3] 
```

Enter fullscreen mode Exit fullscreen mode

## 对象呢？

很棒的问题！在下面的例子中(剧透警告)，下划线处理对象的方式和数组一样。WOAH！

```
//.each(element, index, list)

const objList = { pie1: 'Apple', pie2: 'Blueberry', pie3: 'Peach'}

function logger(ele){
  console.log(ele)
}
_.each(objList, logger)
//Apple, Blueberry, Peach { pie1: 'Apple', pie2: 'Blueberry', pie3: 'Peach'} 
```

Enter fullscreen mode Exit fullscreen mode

## 怎么晃？

让我们深入了解这是如何实现的。正常情况下，我们必须使用 for 循环，并获取对象中每个键的值(见下文)。

```
const objList = { pie1: 'Apple', pie2: 'Blueberry', pie3: 'Peach'}

for(let key in objList){
  console.log(objList[key]);
}
//Apple, Blueberry, Peach 
```

Enter fullscreen mode Exit fullscreen mode

因此...当我们打开引擎盖看一看引擎时，我们只发现一个 if 语句，它检查你传入的是数组还是对象。后面是适当的 for 循环和一个`callback`。请参见下面的示例。_ object 的工作方式与 jQuery 中的＄object 完全一样。

```
const _ = {  
  each(list, callback){
    if(Array.isArray(list)){
      for(let i = 0; i < list.length; i++){
        callback(list[i], i, list);
      }
    } else {
      for(let key in list){
        callback(list[key], key, list)
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

这是一个非常庞大/有用的库中的一小部分。事实上，这对我来说还是很新鲜的，因为我不是 JS 开发专家。我喜欢写代码，我喜欢学习新的库，但更重要的是理解它下面发生了什么。我希望我向你介绍下划线的尝试有所帮助，即使只有一个人。此外，您可以通过从[下划线打开 chrome dev tools 来测试 google chrome dev tools 的下划线。JS](https://underscorejs.org) 网站。

留下评论，让我知道你对下划线的看法。
*也让我知道，如果你发现更正感谢*