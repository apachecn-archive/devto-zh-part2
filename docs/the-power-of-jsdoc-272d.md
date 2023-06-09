# jsDoc 的力量

> 原文：<https://dev.to/gmartigny/the-power-of-jsdoc-272d>

# jsDoc 的力量

许多开发人员抱怨 Javascript 的弱类型化(理由很充分)。这就是为什么我们看到了 Typescript 的兴起。但是尽管它很简洁，Typescript 也有一些注意事项。

*   硬打字
*   您的代码被解析和更改
*   要构建的额外步骤
*   要学习的新语法

很多时候，这些都很容易处理或者干脆忽略。jsDoc 真正好的地方，在于它减少了弱型的痛苦而没有任何弊端，甚至还增加了牌桌。

## 这是什么

首先，让我们来看一个复杂的例子，我们将根据本教程重建它。

```
/**
 * Nice little equine animal
 * @class
 * @extends Animal
 */
class Pony extends Animal {
    /**
     * Pony constructor
     * @param {String} name - This pony's name
     * @param {String} [color=Pony.colors.white] - This pony's color
     */
    constructor (name, color = Pony.colors.white) {
        super(name);
        this.color = color;
        /**
         * This pony's age
         * @type {Number}
         */
        this.age = 0;
    }

    /**
     * Make it run
     * @param {...String} through - Any number of field to run through
     * @example instance.run("field", "land");
     * @return {Pony} Itself
     */
    run (...through) {
        console.log("Run through ", ...through);
        return this;
    }

    /**
     * @typedef {Object} PonyColors
     * @prop {String} white - Pure light color
     * @prop {String} black - Dark color
     * @prop {String} foxy - Reddish color
     */
    /**
     * Get possible pony's colors
     * @static
     * @return {PonyColors}
     */
    static get colors () {
        return {
            white: "#fff",
            black: "#333",
            foxy: "#c57432",
        };
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个优势演示(使用 webstorm)。仔细查看自动完成工具提示。

[![animated demo gif](img/f071d05ae7caea0356d66a7890e73c36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q8mCRJEy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a09p6tsc38jjtleqxfhw.gif)

现在，让我们一步步来。

## 描述

jsDoc 最简单的用途是描述一个函数或一个类。

```
/**
 * Nice little equine animal
 */
class Pony extends Animal {
    /**
     * Pony constructor
     */
    constructor (name, color = Pony.colors.white) {
        // ...
    }

    /**
     * Make it run
     */
    run (...through) {
        // ...
    }

    /**
     * Get possible pony's colors
     */
    static get colors () {
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何使用您编写的代码的人都会有一些关于每个函数或类的用途的信息。根据你的 IDE 或编辑器，每当自动完成开始时，它会显示在工具提示中。

## 参数

在介绍中，我谈到了 JS 中的变量类型。这是我们解决问题的地方。JsDoc 允许你指定一个函数需要什么类型参数。然后，如果您给出的参数类型不兼容，您的开发环境应该会警告您。

```
/**
 * Pony constructor
 * @param {String} name - A string
 * @param {String|Number} color - A string or a number
 */
constructor (name, color = Pony.colors.white) {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

同时，你可以快速描述变量的用法。如果参数是可选的，只要用括号将它括起来，并指定相关的默认值。

```
/**
 * Pony constructor
 * @param {String} name - This pony's name
 * @param {String} [color=Pony.colors.white] - This pony's color
 */
constructor (name, color = Pony.colors.white) {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以再次看到自动完成的效果。

[![params type demo](img/4549f06606ea9c1418c8b128433b3df6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r6cWJISu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rszzw6tiuq7z42juz9kk.png)

## 类型/回调定义

有时，您可能需要定义自定义类型来描述一些您不想包装在类中的数据。

```
/**
 * @typedef {Object} PonyColors
 * @prop {String} white - Pure light color
 * @prop {String} black - Dark color
 * @prop {String} foxy - Reddish color
 */ 
```

Enter fullscreen mode Exit fullscreen mode

这样，您可以将类型和描述附加到对象的每个项目。

[![type definition demo](img/786420f59e6988ee1dc12ee8619682a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qx67VAvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xxpljf4yy9tch978obhn.png)

对于预期参数也是如此，考虑以下:

```
/**
 * @typedef {Object} CustomData
 * @prop {String} name - Cute name for you pony
 * @prop {String} [color=Pony.colors.white] - Color of its fur
 * @prop {Number} [age=0] - Years since its birth
 */
/**
 * Create a pony
 * @param {CustomData} data
 * @return Pony
 */
function ponyFactory (data) {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在@typedef 块中解释了`CustomData`类型。类型定义可以用@extends 标记事件扩展其他类型。这真的很酷

如果一个函数期望一个回调(例如典型数组遍历)，您可以显示什么参数将被传递给这个回调。

```
/**
 * @callback PonyCallback
 * @param {Pony} pony - A pony
 * @param {Number} index - Index of the pony
 * @param {Array<Pony>} list - List of all the ponies
 */
/**
 * Execute a function on each created pony
 * @param {PonyCallback} callback - Function called on each pony
 */
function herd (callback) {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更远更远

jsDoc 文档有很多标签供您使用。每一个都允许你更好地告知用户你的代码。

对@return(定义返回类型)、@abstract(该类不应被实例化)、@type(为任何变量指定类型)、@example(展示使用示例)的荣誉奖...

请记住，大多数时候，您将是维护自己代码的人。所以实际上，你通过记录你写的代码来为你自己服务。

最后但同样重要的是，有许多方法可以解析所有的文档并输出完全格式化的 markdown 来记录你的 API。