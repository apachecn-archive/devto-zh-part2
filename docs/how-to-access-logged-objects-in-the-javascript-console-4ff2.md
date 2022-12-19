# 如何在 Javascript 控制台中访问记录的对象

> 原文：<https://dev.to/nicolasmesa/how-to-access-logged-objects-in-the-javascript-console-4ff2>

交叉发布自我的个人博客[https://blog.nicolasmesa.co](https://blog.nicolasmesa.co)。

在这篇短文中，我们介绍了在 Chrome 的 javascript 控制台中创建一个 *console.logged* 对象的过程。

## TLDR

右键点击想要检查的对象，点击**保存为全局变量**。这会创建一个名为`temp<n>`的变量，其中`<n>`是一个数字(从`1`开始)。使用变量来访问对象。

## 解释

### 黑客版

有时当我在调试时，我需要从 Javascript 控制台访问一个对象及其方法。要做到这一点，我必须启动调试器并进入函数，直到我可以访问我想要的对象，或者我必须想出一个类似下面的方法:

```
function superNestedFunction() {
    const obj = getObj();
    console.log(obj);

    // hack
    window.myObj = obj;

    // Do something interesting with obj
} 
```

对于这个讨论，让我们假设`getMyObj()`函数像这样返回一个对象:

```
function getObj() {
    return {
        prop: 'Hello World',
        fn: function() {
            console.log('Inside myObj.fn');
        }
    };
} 
```

然后，从控制台，我可以做:

```
superNestedFunction(); // Logs: {prop: "Hello World", fn: ƒ}
window.myObj.prop; // Logs: "Hello World"
window.myObj.fn(); // Logs: Inside myObj.fn 
```

注意，这是一个可怕的黑客！尽管我不喜欢在日志中有一堆对象，但我认为污染全局名称空间更糟糕(尤其是当你忘记删除那个 hack 的时候)。让我们来看看更好的方法。

### 更好的版本

我们将使用与之前相同的示例(不包括黑客)。整个代码看起来像这样:

```
function superNestedFunction() {
    const obj = getObj();
    console.log(obj); // We're still logging the object

    // Do something interesting with obj
}

function getObj() {
    return {
        prop: 'Hello World',
        fn: function() {
            console.log('Inside myObj.fn');
        }
    };
}

superNestedFunction(); // Logs: {prop: "Hello World", fn: ƒ} 
```

现在右击记录的对象并选择**存储为全局变量**选项。

[![Right click on the Object an select "Store as global variable"](../Images/5c8a0e556e35e0482be73e4934d8db11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XNvAAsSt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.nicolasmesa.co/posts/2018/09/store_as_global_variable.png)

Chrome 会自动创建一个新的全局临时变量(本例中为`temp1`),并为其分配您右键单击的对象:

[![Global variable "temp1" created by Chrome](../Images/7bd97ad67ba410f7c9883aedff484468.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hizKb8eK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.nicolasmesa.co/posts/2018/09/global_variable_created.png)

现在您可以使用`temp1`来访问该对象的任何字段！让我们看看整个过程:

[https://www.youtube.com/embed/xR292-EaUZ0](https://www.youtube.com/embed/xR292-EaUZ0)

## 结论

我们完成了将一个日志对象分配给一个全局变量的步骤，并且能够访问它的属性和函数。请注意，这不是调试器的替代品，它只是探索对象的一种便捷方式。尤其是当`console.log`已经存在的时候。

调试愉快！