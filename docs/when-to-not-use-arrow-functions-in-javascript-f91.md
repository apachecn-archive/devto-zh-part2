# JavaScript 中何时不使用箭头函数

> 原文：<https://dev.to/kevinlangleyjr/when-to-not-use-arrow-functions-in-javascript-f91>

箭头函数是传统函数表达式的一种更简洁的替代方式，但是它们并不是在任何地方都适用。

让我们来看看一些你*不想*使用箭头函数的例子。

#### 场景一。需要动态上下文的回调函数。

需要动态上下文的回调函数，比如被点击的元素，不是箭头函数的好选择，因为您不能改变`this`的预定义上下文。

```
const button = document.querySelectorAll( '.cta-button' );

button.addEventListener( 'click', () => {

this.classList.toggle( 'selected' ); // `this`refers to the Window. 😔

} ); 
```

Enter fullscreen mode Exit fullscreen mode

使用常规函数允许`this`的值基于目标元素的上下文而改变。

```
const button = document.querySelectorAll( '.cta-button' );

button.addEventListener( 'click', function() {

this.classList.toggle( 'selected' ); // `this`refers to the button! 🎉🎉🎉

} ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 场景二。使用`arguments`对象的函数。

箭头函数不绑定全局`arguments`对象。

```
const logUser = () => {

console.log(arguments[0]); // Uncaught ReferenceError: arguments is not defined 😕

}

logUser( 'Kevin' ); 
```

Enter fullscreen mode Exit fullscreen mode

但是使用常规函数，您可以访问全局`arguments`对象。

```
function logUser() {

console.log(arguments[0]); // Kevin

console.log(arguments[1]); // Langley

}

logUser( 'Kevin', 'Langley' ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 场景三。原型方法

```
function User(firstName, lastName) {

this.name = `${firstName}  ${lastName}`;

}

User.prototype.sayHello = () => {

return `Hello, ${this.name}`;

}

const me = new User( 'Kevin', 'Langley' );

me.sayHello(); // Hello, 
```

Enter fullscreen mode Exit fullscreen mode

`sayHello`方法中的`this`值引用了全局对象，而不是它试图访问的`User`对象。

相反，用老式的方法编写原型方法。

```
function User(firstName, lastName) {

this.name = `${firstName}  ${lastName}`;

}

User.prototype.sayHello = function() {

return `Hello, ${this.name}`;

}

const me = new User( 'Kevin', 'Langley' );

me.sayHello(); // Hello, Kevin Langley 
```

Enter fullscreen mode Exit fullscreen mode

#### 场景四。对象方法

```
const user = {

 name: 'Kevin Langley',

sayHello: () => `Hello, ${this.name}`;

}

user.sayHello(); // Hello, 
```

Enter fullscreen mode Exit fullscreen mode

`sayHello`方法中的`this`值引用了全局对象，而不是它试图访问的`User`对象。

相反，用老式的方法编写原型方法。

```
const user = {

 name: 'Kevin Langley',

sayHello() {

return `Hello, ${this.name}`;

}

}

user.sayHello(); // Hello, Kevin Langley 
```

Enter fullscreen mode Exit fullscreen mode