# 对象？不...请列阵！

> 原文：<https://dev.to/genta/objects-no-array-please-2am>

# 我不喜欢物体...那么多！

这是一个*物体* :

```
const obj = {breed:"labrador",age:9} 
```

Enter fullscreen mode Exit fullscreen mode

但有时我更喜欢使用数组。

为什么？因为在我看来它们真的更好看...而且真的有很多方法或循环对【数组】起作用*只是*！

这是一些用来将对象转换成数组的工具。

```
 //Object.values() will give you an array of all the object "values"

const obj = {breed:"labrador",age:9}

const values = Object.values(obj)

console.log(values)

//-> ["labrador", 9]

//Object.keys() will give you an array of all the object "keys"

const obj = {breed:"labrador",age:9}

const keys = Object.keys(obj)

console.log(keys)

//-> ["breed", "age"]

//Object.entries()  will give you an arraysh version of the object. 
//Where the key and the value will be paired into an array... 
//and all of those arrays will be "pushed" into another array.

const obj = {breed:"labrador",age:9}

const entries = Object.entries(obj)

console.log(entries)

//->[["breed", "labrador"], ["age", 9]] 
```

Enter fullscreen mode Exit fullscreen mode

这是很容易的事情，但是很多时候，在我开始在 *JS* 的旅程时，对象对我来说经常是个问题。

要是他们以前告诉过我就好了...

<center>![img](img/15d1a2fcbddec29eeacbcb9b602a7db3.png)</center>

附注:这些工具还可以...如果可以用数组代替对象的话。有时你不得不使用物体...因为性能或长期维护。