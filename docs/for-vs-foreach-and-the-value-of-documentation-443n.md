# “for”与“forEach”以及文档的价值

> 原文：<https://dev.to/aritdeveloper/for-vs-foreach-and-the-value-of-documentation-443n>

这篇文章是为了强调开发人员熟悉文档的重要性。请注意，当我在编码时遇到问题时，我是 StackOverflow 上的第一个人，我很喜欢开发教程培训轮的便利性(更多信息请点击[这里](https://dev.to/msarit/web-dev-tutorials-taking-the-training-wheelsoff-2nb8))。但是我爱上了那些写得好的医生所能提供的详细的、例子丰富的帮助。

例子:我最近想对数组的每一项进行一些计算。所以我编码如下:

```
array1 = [2,3,4,5];
function greaterThan(numbr){
    array1.forEach(function(item){
        if (item >= numbr){
            return true;
        };
    });
    return false;
};

greaterThan(4); // expect "true"
greaterThan(9); // expect "false"
greaterThan(2); // expect "true"
greaterThan(1); // expect "true" 
```

Enter fullscreen mode Exit fullscreen mode

但是，我上面所有的函数调用都返回了`false`。我忍住谷歌“forEach 不工作”的冲动，直接去了 [MDN](https://developer.mozilla.org/en-US/) 。果然，在“forEach”的文档中，我读到了以下内容:

> 除了抛出异常之外，没有其他方法可以停止或中断`forEach()`循环。如果您需要这样的行为，那么`forEach()`方法是错误的工具。

如此简单。这么直白。我的`return true`线没有打破`forEach`循环；它的执行一直持续到最后。我通过运行`greaterThan(5)`验证了这一点；它返回了`true`，因为`5`是我的数组中的最后一个数字，因此也是最后一个被处理的数字。我用一个简单的 for 循环重写了我的代码，一切正常。

相对于其他信息来源，你采用文档的方式有哪些？