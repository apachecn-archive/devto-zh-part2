# ES6 基本功能

> 原文：<https://dev.to/k_penguin_sato/es6-basic-features-40hl>

# 简介

ES6 中引入了一些新功能。我将在这篇文章中介绍它们的一些基本特征。

# 目录

1.  [用 const 和 let 进行变量声明](#variable-declaration-with-const-and-let)
2.  [let 和 const 的范围](#the-scope-of-let-and-const)
3.  [字符串插值](#string-interpolation)
4.  [箭头功能](#arrow-function)
5.  [默认参数](#default-parameters)
6.  [类表达式](#class-expression)
7.  [类继承](#class-inheritance)
8.  [传播算子](#spread-operator)

# 用 const 和 let 进行变量声明

你可以使用`var`、`let`或者`const`来声明一个变量。

### 让

用`let`声明的变量不能重新声明。但是你可以重新分配一个新的值。

```
let name = 'John'
console.log(name) //=> John

name = 'Mike'
console.log(name) //=> Mike

let name = 'Nick' //=> SyntaxError: redeclaration of let name 
```

Enter fullscreen mode Exit fullscreen mode

### 常数

用`const`声明的变量不能重新声明。而且你不能重新分配一个新的值。

```
const name = 'John'
console.log(name) //=> John

name = 'Mike' //=> TypeError: invalid assignment to const `name'

const name = 'Nick' //=> SyntaxError: redeclaration of let name 
```

Enter fullscreen mode Exit fullscreen mode

# let 和 const 的范围

与`var`不同，`let`和`const`语句声明块范围局部变量。

**var**的例子

```
var x = 10
console.log(x) //=>10

{
    var x = 5
    console.log(x) //=>5
}

console.log(x) //=> 5 
```

Enter fullscreen mode Exit fullscreen mode

**让**的例子

```
let x = 10
console.log(x) //=>10

{
    let x = 5
    console.log(x) //=>5
}

console.log(x) //=> 10 
```

Enter fullscreen mode Exit fullscreen mode

**const**的例子

```
const x = 10
console.log(x)//=> 10

{
    const x = 5
    console.log(x)//=> 5
}

console.log(x)//=> 10 
```

Enter fullscreen mode Exit fullscreen mode

# 字符串插值

你可以使用`template literals`来读取字符串中变量的值。

```
let name = 'Mike'

console.log(`I am ${name}`)//=> I am Mike 
```

Enter fullscreen mode Exit fullscreen mode

# 箭头功能

**ES5 风格功能**

```
function greeting(name){
    console.log('Hello' + '  ' + name)
}

greeting('Mike') //=>Hello Mike 
```

Enter fullscreen mode Exit fullscreen mode

**ES6 风格功能**

```
const greeting=(name)=> {
    console.log(`Hello ${name}`)
}

greeting('Mike') //=> Hello Mike 
```

Enter fullscreen mode Exit fullscreen mode

# 默认参数

您可以将默认值赋给参数。

```
const add = (x, y = 10) => {
    console.log(x + y)
}

add(5) //=> 15 
```

Enter fullscreen mode Exit fullscreen mode

# 类表达式

类表达式是 JavaScript 现有的基于原型的继承的语法糖。

```
class Car{
    constructor(name){
        this.name = name
    }

    displayinfo(name){
        console.log(this.name)
    }
}

const car1 = new Car('Honda')

car1.displayinfo()//=> Honda 
```

Enter fullscreen mode Exit fullscreen mode

# 类继承

类可以使用`extends`互相扩展。

```
class Car{
    constructor(name){
        this.name = name
    }

    displayinfo(name){
        console.log(this.name)
    }
}

class Track extends Car {
    constructor(name){
        super()
        this.name = name
    }
}

track1 = new Track('TL')
track1.displayinfo() //=> TL 
```

Enter fullscreen mode Exit fullscreen mode

# 传播算子

下面是`spread operator`的一些用法。

### 布阵

```
const arr = [2,3]

console.log(...arr)//=> 2, 3 
```

Enter fullscreen mode Exit fullscreen mode

### 组合数组

```
const arr = [2,3]
const arr2 = [1, ...arr, 4, 5]

console.log(arr2)//=> Array(5) [ 1, 2, 3, 4, 5 ] 
```

Enter fullscreen mode Exit fullscreen mode

### 获取多个参数作为数组

```
const arr=(arg1, ...args)=> {
    console.log(arg1, args)
}

arr(1, 2, 3, 4, 5)//=> 1 Array(4) [ 2, 3, 4, 5 ] 
```

Enter fullscreen mode Exit fullscreen mode