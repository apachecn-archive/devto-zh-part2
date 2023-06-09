# 原型设计模式

> 原文：<https://dev.to/arnas/prototype-design-pattern--2lg2>

## 什么是原型设计模式？

它是一种软件模式，允许使用原型(样本、模型)来复制、克隆或创建遵循相同结构的对象。

类型:创意

### 主要用例

克隆现有对象而不是创建新对象，假设对象创建是一项开销很大的操作。代价高昂的操作示例包括:发送多个请求、查询数据库、生成复杂的对象属性等。

### 它解决了什么问题？

它解决了性能问题。通过减少资源饥渴的对象创建，它允许应用程序更有性能。

此外，模式可以抽象一些对象创建逻辑。

### 相关创建模式:

*   允许动态配置的抽象工厂。

### 它是如何工作的？

有几种方法可以使用原型模式:

*   面向对象语言中使用的标准
*   基于原生原型继承

OOP，主要用于面向对象的语言。类获取一个人工智能的初始原型，并将其作为一个寺庙来创建多个人工智能。

```
class AIPrototype {
    constructor(prototype){
        this.name = prototype.name;
        this.type = prototype.type;
        this.brains = prototype.brains || this.generateBrains();
    }

    clone(){
        // NOTE: if any of these properties are not a primitive type you need to create a shallow or deep copy.
        // For example if `this.brains` would be an object all AI objects that are generated 
        // from this `clone` function would use brain.
        // Though that may be good as they would think as one.
        return new AI(this.name, this.type, this.brains)
    }

    generateBrains() {
        // super expensive operation
        const randomStringThatSimulatesBrainFunctionality = Math.random().toString(36).substring(7);
        return randomStringThatSimulatesBrainFunctionality
    }
}

class AI {
    constructor(name, type, brains) {
        this.name = name;
        this.type = type;
        this.brains = brains
    }
}

const prototype = new AI("GLaDOS", "rampant");
const rampantAIPrototype = new AIPrototype(prototype);

const rampantAIV1 = rampantAIPrototype.clone();
const rampantAIV2 = rampantAIPrototype.clone();

console.log(rampantAIV1.brains === rampantAIV2.brains) // true 
```

Enter fullscreen mode Exit fullscreen mode

Native one 使用 Javascript 原型继承在多个人工智能之间共享相同的大脑。

```
class AI {
    constructor(name, type, brains){
        this.name = name;
        this.type = type;

        AI.prototype.brains = brains || this.generateBrains();
    }

    generateBrains() {
        // super expensive operation
        const randomStringThatSimulatesBrainFunctionality = Math.random().toString(36).substring(7);
        return randomStringThatSimulatesBrainFunctionality
    }
}

const rampantAI = new AI("GLaDOS", "rampant");

const friendlyAI = new AI("GLaDOS", "friendly");

console.log(rampantAI.brains === friendlyAI.brains) // true 
```

Enter fullscreen mode Exit fullscreen mode

受益于 Javascript 原型继承的其他例子可能是 Calculator。原型中可能定义了`add`、`div`、`sub`函数。

## 反馈

嘿，如果你到了这个地方，请告诉我我做得怎么样。我正在尝试和学习如何写一篇文章。
所以我非常感谢你的反馈，怎么样，是太短了还是太长了:)

### 来源

*   [https://en.wikipedia.org/wiki/Prototype_pattern](https://en.wikipedia.org/wiki/Prototype_pattern)
*   [https://medium . com/front-end-hacking/JavaScript-design-patterns-ed 9d 4c 144 c 81](https://medium.com/front-end-hacking/javascript-design-patterns-ed9d4c144c81)
*   [https://github.com/fbeline/Design-Patterns-JS/](https://github.com/fbeline/Design-Patterns-JS/)
*   [https://weblogs . ASP . net/dwahlin/techniques-strategies-and-patterns-for-structuring-JavaScript-code-the-prototype-pattern](https://weblogs.asp.net/dwahlin/techniques-strategies-and-patterns-for-structuring-javascript-code-the-prototype-pattern)
*   [https://www . do factory . com/JavaScript/prototype-design-pattern](https://www.dofactory.com/javascript/prototype-design-pattern)
*   [https://www.youtube.com/watch?v=xizFJHKHdHw](https://www.youtube.com/watch?v=xizFJHKHdHw)