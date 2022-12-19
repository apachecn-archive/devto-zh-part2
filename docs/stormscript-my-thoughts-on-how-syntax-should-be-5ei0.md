# StormScript:我对语法应该如何的想法

> 原文：<https://dev.to/stormprograms/stormscript-my-thoughts-on-how-syntax-should-be-5ei0>

# 简要介绍

用大多数人的话来说，我是编程新手。我是一名高中生，有大约 4 年的编程经验，其中一年从事软件开发。我觉得对大多数人来说，奇怪的是我已经把自己(没有双关语的意思)投入到这类项目中了。我没有写出最整洁的代码。我写更多的函数代码。也就是说，我正在寻找人们为我的知识库贡献想法、代码和发布报告。

[风暴脚本库](https://github.com/stormprograms/stormscript)
[风暴脚本文档](https://stormprograms.com/stormscript)

# 我为什么要做这个？

很长一段时间，我对编程怎么教，怎么读有非常强烈的感受。尽管我热爱 C++和 JavaScript，但我相信语法可以做得更好。我的意思是，我们需要更多读起来像 Python，但仍然非常强大的语言。我知道这里的大多数人，如果不是所有人，都知道许多编程语言，并且习惯于这些笨拙、难写和难读的语言。让我们面对现实吧，他们可以做得更好。

# 这怎么好点了？

我有很多理由相信 StormScript 解决了这些问题。为了向你展示，我将会用 StormScript 比较许多语言，以及为什么我认为它更好。

## 功能

### 没有论据

C++:

```
#include <iostream> 
std::string givehello() {
    return "Hello, World";    
}

int main() {
    std::cout << givehello() << '\n';
    return 0;
} 
```

在 C++中，名称空间是不受控制的，几乎所有不是从 C 继承的标准 C++函数和类型库都要求在它前面有`std::`。

Python:

```
def givehello():
    return "Hello, World"

print(givehello()) 
```

JavaScript:

```
function givehello() {
    return "Hello, World"
}

console.log(givehello()) 
```

Python 和 JavaScript 分别具有很强的可读性和一点点可读性，但是它们都有相同的问题:它们都缺少一个主作用域，并且只是在其他语言中通常是全局作用域中运行代码。这降低了可读性，因为很难分清应该把东西放在哪里，什么需要立即运行，什么不需要。

StormScript:

```
func givehello {
    return "Hello, World!";
}end;

do{
    printl givehello;
}end; 
```

如果你仔细观察，你可能会注意到在参数应该去的地方没有括号，原因很简单:*如果你没有参数，你就不应该为它们保留一个空间。*

### 同论据

几乎在每种语言中，运行一个函数都是一样的:

```
functionname("arg1", &arg2, arg3); 
```

StormScript 参数的工作方式完全不同。StormScript 函数使用`=>`符号，它通常用于声明一个内联函数。
例如在 JavaScript 中:

```
return new Promise(resolve, reject) => { 
    resolve(); 
}) 
```

在 StormScript 中，`=>`实际上是为运行带参数的函数而保留的。`@`符号用于声明带有参数的函数。

```
@args: int x, str y;
func printxy {
    printl x, '\n', y;
}end;

do {
    printxy => x: 3, y: "hi";
}end; 
```

## 类

### 背景

在这方面我可能是孤独的，但是我花了**很长时间**才明白编程中的类是什么。从开发 StormScript 开始，我就一直想把声明类的关键字从`class`改成`type`。

### 例子

C++:

```
#include <iostream> 
class human {
public:
    int age;
    std::string name;
    std::string gender;
};

int main() {
    human h;
    h.gender = "male";
    h.name = "Bob";
    h.age = 22;

    std::cout << h.name << " is a " << h.age << " year old " << h.gender << '\n';

    return 0; 
} 
```

StormScript:

```
type human{
    int age;
    str name;
    str gender;
}end;

do{
    human h;

    h|gender: "male";
    h|name: "Bob";
    h|age: 22;

    printl h|name, " is a ", h|age, " year old ", h|gender;
}end; 
```

StormScript 在类的工作方式上与 C++没有太大区别，但主要区别在于它使用`type`关键字而不是`class`关键字进行声明。

## If 语句

StormScript 与许多其他语言的 if 语句相似，但有一个关键区别，它使用了`is`而不是`==`，使用了`not`而不是`!=`。我做这个改动是因为我犯了一个错误，输入了`=`而不是`==`，因为这样更易读。

示例:

```
do{
    str x: "hi";

    if x is "hi"{
        printl x;
    }end;
}end; 
```

请务必查看存储库和文档。
[风暴脚本库](https://github.com/stormprograms/stormscript)
[风暴脚本文档](https://stormprograms.com/stormscript)