# 用事件处理程序扩展 Javascript 数组

> 原文：<https://dev.to/gate3/extending-javascript-array-with-an-event-handler-1ce2>

我最近不得不编写一个分类器算法，根据某些属性对一组项目进行分类。这是一个非常复杂的数据集，有一组更加复杂的约束，但是为了这篇文章，我将保持它的简单。

假设我们有以下数据集

```
 [
        {id:1, age:32},
        {id:2, age:4},
        {id:3, age:20},
        {id:4, age:30}
    ] 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们希望找到最大年龄和所有年龄的总和。使用一个循环和一些变量可以很容易地做到这一点，但是对于本文来说，我将使用一个附加到传统 javascript 数组的事件监听器来实现这一点。

# 首先让我们扩展数组对象

```
 const MyArray = function (){
        // this events object will be explained shortly
        this.events = {}
    } 
```

Enter fullscreen mode Exit fullscreen mode

所以，我简单地创建了一个函数，称它为我的数组，然后给它一个 events 对象(或者 hashtable ),它将保存我们所有的事件和回调函数，格式如下:

```
 {eventName:callback_function} 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续创建扩展数组

```
 MyArray.prototype = []

    MyArray.prototype.on = function (event, callback){
        this.events[event] = callback
    }    

    MyArray.prototype.push = function(args){
        Array.prototype.push.call(this, args)
        const eventName = 'push'
        if(this.events[eventName]){
            this.events[eventName](args)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们让函数使用原型继承来继承数组对象的属性。

第二个块实现了我们的事件监听器函数。我们调用 on 函数，所以我们可以做 on('filter ')等事情。这个函数所做的就是接收事件(或事件名)和事件发生时执行的回调。该函数将事件名称存储为键，将回调存储为 events 哈希表中的值。

最后，我们创建自己的 push 方法，将新的项目放入我们的对象中。下一行使用父数组对象的 push 方法，但是通过使用 call，我们在当前对象的上下文中调用 push。这是因为我们的对象继承了数组。仅此而已。

## 测试我们的新对象

因此，让我们测试一下，看看它将如何使用上述示例数据工作。

```
const Arr = new MyArray()
let sum = 0;
let oldest = 0;
Arr.on('push', function (e){
    sum += e.age
    oldest = (e.age > oldest ? e.age:oldest)
})

for (let data of dataSet){
    Arr.push(data)
}

console.log(sum, oldest) 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。