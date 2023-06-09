# JavaScript 对象简介

> 原文：<https://dev.to/frugencefidel/introcuding-javascript-objects-54m3>

这篇文章最初发表在我的博客上。

对象是以键值对的形式存储一组数据的方式。对象由符号{ }表示。

假设我们必须列出某人的名、姓和他/她的国籍。

= >通过使用数组

```
const person = ['Frugence', 'Fidel', 'Tanzanian']; 
```

Enter fullscreen mode Exit fullscreen mode

这里很难理解哪个值是名和姓，也许是通过猜测。如果数组值互换了怎么办

```
const person = ['Fidel', 'Tanzanian', 'Frugence'];

// access person's first name
person[2]; // This is not meaningful 
```

Enter fullscreen mode Exit fullscreen mode

这是一个对象的完美用途。

= >通过使用对象

```
const person = {
  firstName: 'Frugence',
  lastName: 'Fidel',
  nationality: 'Tanzanian'
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，即使位置互换，读取数据也很简单。

```
const person = {
  lastName: 'Fidel',
  nationality: 'Tanzanian',
  firstName: 'Frugence'
}; 
```

Enter fullscreen mode Exit fullscreen mode

**创建对象**

创建对象有两种常见方法。您可以创建一个空对象并在以后添加数据，也可以用它的数据创建。

I .通过空对象，稍后添加数据

```
// create empty object
const person = {};

// add data to object
person['firstName'] = 'Frugence';
person['lastName'] = 'Fidel';
person['nationality'] = 'Tanzanian';

console.log(person); // output {firstName: "Frugence", lastName: "Fidel", nationality: "Tanzanian"} 
```

Enter fullscreen mode Exit fullscreen mode

二。用它的数据

```
const person = {
  firstName: 'Frugence',
  lastName: 'Fidel',
  nationality: 'Tanzanian'
};

console.log(person); // output {firstName: "Frugence", lastName: "Fidel", nationality: "Tanzanian"} 
```

Enter fullscreen mode Exit fullscreen mode

**访问对象的数据**

从对象中访问数据有两种常见的方法，即括号和点符号。

```
const person = {
  firstName: 'Frugence',
  lastName: 'Fidel',
  nationality: 'Tanzanian'
};

// bracket notation
const first = person['firstName'];
console.log(first); // output 'Frugence'

// dot notation
const last = person.lastName;
console.log(last); // output 'Fidel' 
```

Enter fullscreen mode Exit fullscreen mode

**更新对象的数据**

您可以使用点符号或括号符号
来更新对象

```
const person = {
  firstName: 'Frugence',
  lastName: 'Fidel'
};

// bracket notation
person['firstName'] = 'John';

// dot notation
person.lastName = 'Doe';

console.log(person); // output {firstName: "John", lastName: "Doe"} 
```

Enter fullscreen mode Exit fullscreen mode

**向对象添加方法**

方法是对象内部的函数。

```
const person = {
  firstName: 'Frugence',
  lastName: 'Fidel',
  nationality: 'Tanzanian',
  sayHi() {
    return `Hi!! My name is ${person.firstName}  ${person.lastName}, and I'm ${person.nationality}`;
  }
};

// sayHi is the method
console.log(person.sayHi()); // output "Hi!! My name is Frugence Fidel, and I'm Tanzanian" 
```

Enter fullscreen mode Exit fullscreen mode

**对象中的数据类型**

对象可以保存任何 javascript 数据类型。

```
const person = {
  fullName: 'Frugence Fidel',
  isTanzanian: true,
  luckyNumber: 1
}; 
```

Enter fullscreen mode Exit fullscreen mode

**嵌套对象和数组**

有时你可能需要嵌套对象和数组来满足你的需要。

```
const person = {
  fullName: 'Frugence Fidel',
  hobbies: ['Watch TV', 'Football', 'Reading', 'Coding']
}; 
```

Enter fullscreen mode Exit fullscreen mode