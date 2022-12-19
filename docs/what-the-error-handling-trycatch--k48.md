# 搞什么...错误处理！(试试...接住)

> 原文：<https://dev.to/genta/what-the-error-handling-trycatch--k48>

# 请检查您的代码！

**漏洞**存在...他们永远都会。

无论我们在编码方面有多好，或者在编码时我们的思维有多专注...会有**bug**像*编码 bug*或者*输入 bug*之类的。

## 我们有责任...尽我们所能去控制它们

一个非常基本的控制我们代码的方法是非常聪明和简单的。

有错误吗？做这个。
不是错误？做这个。无论如何...也这样做。

我们可以在如下代码中看到这种东西:

```
const breeds = ["labrador","chow-chow","samoyed"];

try
    {
        //is there an
        akita;
        //?
    }
catch(e)
    {
        console.log("Ouch, no Akita here " + e);
        }
finally
    {
        console.log("At least there's a labrador");
    }

//-> Ouch, no Akita here ReferenceError: akita is not defined
//-> At least there's a labrador 
```

Enter fullscreen mode Exit fullscreen mode

那么，这里发生了什么？

我们创建了一个简单的:
`try{}
catch{}
finally{}`

### 试试{}

这里我们*尝试*执行代码

```
const breeds = ["labrador","chow-chow","samoyed"];

try
    {
        //is there an
        akita;
        //?
    } 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这里有一个`akita`变量，但是程序中没有任何东西与此相关，所以这里有一个错误

### 赶{}

当**错误发生**时，调用`catch{}`语句。

```
catch(e)
    {
        console.log("Ouch, no Akita here " + e);
    } 
```

Enter fullscreen mode Exit fullscreen mode

所有语句中的*内容都将被执行，我们将*控制台调出*字符串和那个**奇怪的** *e* 。
那一个是*错误对象*一个内置的*对象*，它 *JS* 提供给我们**捕捉**错误发生时发生的事情。*

用它可以做很多事情。如果你使用`e.stack`，它将返回堆栈中出错的地方*。*

我们还可以定义我们的*自定义误差*，稍后我们会看到它。

### 终于{}

这种说法，总是会发生。
不管是否有错误或者代码是*好的*...总会执行`finally{}`。

## 我们的自定义错误...

有时候，创建我们的*自定义错误*真的很重要。

为什么？

也许我们想给这种错误命名为 T1。
也许我们想要一个更好的代码，更容易理解。有很多很好的理由。

这里是`throw`

```
const breeds = ["labrador","chow-chow","samoyed"];

  try
      {
          if(!breeds.includes("Akita")) 
          throw new SyntaxError("no Akita here");

      }
  catch(e)
      {
          console.log(e);
      }
//-> SyntaxError: no Akita here 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？我们刚刚创造了一个新的“语法错误”。
当 *try 遇到错误*时， *throw* 会立即调用 *catch* 并产生一个*新错误*。

### 这个我们可以更深入。

如果我们想创建一个完全*新的错误对象*，那么*错误*将被抛出**，如果它是那种*错误*的实例呢？**

我们可以这样做:

```
class noDog extends Error {}

function findDog(arr) {
  if (arr.includes("Akita")) {
    return "Here you are";
  } else {
    throw new noDog("No Akita here");
  }
}

function doggy(arr) {
    try {
      return findDog(arr);
    } catch (e) {
      if (e instanceof noDog)
        throw e.message;
    }
}

console.log(doggy(["labradors","chow-chow"]));
//-> No Akita here 
```

Enter fullscreen mode Exit fullscreen mode

百米...这真的不像我们之前的例子那么简单。所以如果你不知道这里发生了什么，不要害怕，没关系。

我们正在定义一个*类*，它由*扩展*内置的*错误*。我们只是在扩展那个类。仅此而已。这个新类没有特殊的属性，它继承了来自 *Error* 对象的所有东西。

总之...我们已经为我们的*错误*创建了一个**新实例**。

考虑到这一点，我们现在可以*捕捉*异常**，如果那些是 noDog** 的**实例，我们可以将*这种*错误从*标准错误*中分离出来。**

<center>![wow](../Images/5f8ac49a46ceba96c5ee74f4e4c6de85.png)</center>

<center>COOL</center>