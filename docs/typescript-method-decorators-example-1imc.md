# TypeScript 方法装饰器示例

> 原文：<https://dev.to/scleriot/typescript-method-decorators-example-1imc>

装饰器是 JavaScript 的第二阶段提案，已经作为 TypeScript 中的一个实验性特性。

装饰器是一个应用于类、方法、属性或参数的函数，并为后者添加一些逻辑。换句话说，使用装饰器与创建一个新类是一样的(但简单得多),这个新类扩展了目标类，并有一个指向它的字段:

[![Wikipedia schema](../Images/6b4287f81ad394e894de2cb6d252dfc6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7qZ-RmkX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/e/e9/Decorator_UML_class_diagram.svg/800px-Decorator_UML_class_diagram.svg.png) 
来源:【https://en.wikipedia.org/wiki/Decorator_pattern】T4

您甚至可以让*装饰工厂*定制如何将装饰应用于声明(使其在不同的上下文中更容易重用)。

在这个例子中，我们将使用一个方法装饰工厂去抖一个函数。

## 去抖概念

去抖动的完美例子是实时搜索文本输入:为了进行搜索，你向服务器发送一个请求。简单的实现会在每次文本输入改变时发送一个请求，但是这会使服务器过载，视图必须等待每个请求完成后才能显示最终结果。

去抖将方法的执行延迟一段固定的时间。如果在暂停时再次调用该方法，则第一次方法调用将被取消。这样，当用户停止输入时，搜索组件将只向服务器发送一个请求。

[![Debounce concept](../Images/bf5a260a7a094a00925961095a8e4abe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RTB4Avoj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oppthnv7npyr0mtrd4js.png)

最终看起来是这样的:

[![Debounce example](../Images/fd1a96ab40371ee95669576e6a1812b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5GUhtYOe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2i4vie4kb6i0t75xbeha.gif)

## 打字稿配置

装饰器仍然是 TypeScript 中的一个实验性特性，所以您需要显式启用`experimentalDecorators`编译器选项。根据您构建项目的方式，有两种可能性:

命令行:

```
tsc --target ES5 --experimentalDecorators 
```

Enter fullscreen mode Exit fullscreen mode

tsconfig.json:

```
{  "compilerOptions":  {  "target":  "ES5",  "experimentalDecorators":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 创建装饰者

装饰工厂被声明为普通的类型脚本函数:

```
function Debounce(wait:number, immediate:boolean=false) { 
```

Enter fullscreen mode Exit fullscreen mode

`wait`是毫秒延时。
`immediate`设置我们是要“调用函数，然后停顿 x 毫秒，再允许它被调用”，还是“停顿 x 毫秒，然后实际调用函数”。

`Debounce`方法将返回装饰函数(这就是它被称为工厂的原因):

```
return function(target: any, propertyKey: string | symbol, descriptor: PropertyDescriptor) { 
```

Enter fullscreen mode Exit fullscreen mode

这个函数从它所应用的元素中获取它的三个参数(在我们的例子中，它将是我们想要去抖的方法):

*   `target`引用元素的类。它将是静态方法的构造函数或实例成员的类的原型
*   `propertyKey`是元素的名称
*   `descriptor`是方法的属性描述符，所以我们可以改变它

装饰函数的主体看起来像这样:

```
var timeout:any;
var originalMethod = descriptor.value;
descriptor.value = function() {
    var context = this
    var args = arguments;
    var later = function() {
        timeout = null;
        if (!immediate) originalMethod.apply(context, args);
    };
    var callNow = immediate && !timeout;
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
    if (callNow) originalMethod.apply(context, args);
};
return descriptor; 
```

Enter fullscreen mode Exit fullscreen mode

我们存储方法的原始体:

```
var originalMethod = descriptor.value; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们通过将`descriptor.value`设置为一个新函数来改变它:

```
descriptor.value = function() { 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们使用超时来延迟方法的执行。

> 我们的方法装饰器返回的值将覆盖原始类方法的描述符。

最后我们有了下面的去抖动装饰工厂:

```
function Debounce(wait:number, immediate:boolean=false) {
    return function(target: any, propertyKey: string | symbol, descriptor: PropertyDescriptor) {
        var timeout:any;
        var originalMethod = descriptor.value;
        descriptor.value = function() {
            var context = this
            var args = arguments;
            var later = function() {
                timeout = null;
                if (!immediate) originalMethod.apply(context, args);
            };
            var callNow = immediate && !timeout;
            clearTimeout(timeout);
            timeout = setTimeout(later, wait);
            if (callNow) originalMethod.apply(context, args);
        };
        return descriptor;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 使用它

我们想去抖下面的搜索方法(来自 Vue.js 组件的类):

```
search(query: string) {
    axios.get(`users`, {
        params: {
            name: query
        }
    })
    .then(response => {
        // ...
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们简单地应用我们之前定义的装饰器，并使用适当的时间:

```
@Debounce(500)
search(query: string) {
    axios.get(`users`, {
        params: {
            name: query
        }
    })
    .then(response => {
        // ...
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，只有在 500 毫秒内没有发送其他的`search`调用，才会调用`search`方法。

> 你绝对应该考虑将 [Vue.js](https://vuejs.org/) 与 TypeScript 一起使用，它们是很好的搭配！https://fr.vuejs.org/v2/guide/typescript.html
> T3

再深入挖掘 TypeScript decorators:
[https://medium . com/Google-developers/exploring-es7-decorators-76 ECB 65 FB 841](https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841)
[http://www.typescriptlang.org/docs/handbook/decorators.html](http://www.typescriptlang.org/docs/handbook/decorators.html)
[http://www.sparkbit.pl/typescript-decorators/](http://www.sparkbit.pl/typescript-decorators/)
[https://cabbageapps.com/fell-love-js-decorators/](https://cabbageapps.com/fell-love-js-decorators/)

**最初发表于[我的个人博客](http://cleriotsimon.com/posts/typescript-decorators/)。**