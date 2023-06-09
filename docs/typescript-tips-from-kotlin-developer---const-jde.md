# Kotlin developer - const 提供的打字技巧

> 原文：<https://dev.to/brightdevs/typescript-tips-from-kotlin-developer---const-jde>

如今，我大多用 Kotlin 编程语言编写代码。当我开始研究 Java 7 代码库时，我对它产生了兴趣，并很快厌倦了这种语言。在 [Bright Inventions](https://brightinventions.pl) 我们经常在后端、前端和移动开发中使用 TypeScript，所以我想分享一些在使用 Kotlin 时学到的关于 TypeScript 的想法和技巧。特别是这篇文章是关于常量变量的。

## 尽可能使用`const`

使用不可变变量有助于对程序的流程和状态进行推理。它有助于编译器提供更智能的提示，尤其是在处理可空类型时。

在 Kotlin 中，`val`关键字表示初始化后值不变的变量，例如:

```
val x: Int
val y = 3
x = 2
x = 20 // Error: Val cannot be reassigned
y = 30 // Error: Val cannot be reassigned 
```

Enter fullscreen mode Exit fullscreen mode

在[打字稿](https://www.typescriptlang.org/)中，这种情况用`const` :
处理

```
const x: number // Error: 'const' declarations must be initialized
const y: number = 3

y = 30 // Error: Cannot assign to 'y' because it is constant or read-only property 
```

Enter fullscreen mode Exit fullscreen mode

## 编译者的最爱`const`

当我们使用`const`时，编译器变得更聪明的第一种方式是空检查。当您启用 [`strictNullChecks`](https://www.typescriptlang.org/docs/handbook/compiler-options.html) 时，Kotlin 和 TypeScript 编译器都知道某些内容是否可以为空。

```
const firstName: string | null = getFirstName()
let lastName: string | null = getLastName()

if (firstName !== null && lastName !== null) {
    setTimeout(() => {
        console.log(firstName.length)
        console.log(lastName.length) // Error: Object is possibly 'null'
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

在前两行中，我们声明`firstName`和`lastName`持有`string`或`null`。用辅助函数`getFirstName`和`getLastName`初始化变量。在我们检查了`firstName`和`lastName`绝对不为空之后，我们触发一些异步函数。我们可以放心使用`firstName.length`。然而，当我们使用`lastName.length`时，编译器报错`Object is possibly 'null'`。这是因为在空检查和`console.log`语句*之间，有可能*会改变`lastName`值。通过查看代码，我们可能知道这不是真的。然而，编译器不能在所有情况下都 100%确定。谢天谢地，我们有`const`，我们可以与编译器分享我们的知识。

## 编译器用`const`捕捉 bug

因为`const`和`val`只能赋值一次，编译器可以防止另一类 bug。看一下代码示例 blow。有一个 bug🐛如果我们用`const`而不是`let`，这很容易避免。

```
let firstName: string = person.firstName
let lastName: string = person.lastName

const parsed = parseFormData((data: {name: string }) => {
    let first: string | null, last: string | null
    let parts = data.name.split('  ')
    lastName = parts[0]
    firstName = parts[1]
    return { firstName: first, lastName: last }
})

if (parsed.firstName !== firstName || parsed.lastName !== lastName) { 
    // submit changes
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经发现了这个漏洞。然而，尤其是如果你像我一样，在长时间血液中的咖啡水平大大低于所需水平后，可能需要很长时间才能找出原因。不过有一个非常简单的补救方法。通过将`firstName`和`lastName`声明为常量变量，编译器为我们捕捉到了错误:

```
lastName = parts[0] // Error: Cannot assign 'lastName' because it is a constant or a read-only property
firstName = parts[1] // Error: Cannot assign 'firstName' because it is a constant or a read-only property 
```

Enter fullscreen mode Exit fullscreen mode

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)