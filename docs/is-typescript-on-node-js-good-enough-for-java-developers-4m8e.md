# Node.js 上的 Typescript 对 Java 开发者来说够好了吗？

> 原文：<https://dev.to/bnevilleoneill/is-typescript-on-node-js-good-enough-for-java-developers-4m8e>

[![](../Images/93d5cd2a5bb63c6d237fd04b571d5a99.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PeWK46uh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/672/1%2A0ei2MOQxAzF7krm-v60wnQ.jpeg)

你时不时会遇到讨厌 Javascript 的古怪程序员。他们认为 JavaScript 很糟糕，因为它缺乏类型检查或任何严格的东西。他们会坚持认为企业级系统需要一定程度的严格性，这只能在严格类型的语言中找到(比如 Java、C#或 C++)。

争论还在继续。在“小型”应用程序中，具有松散类型字段的匿名对象是可以的。但是，一个拥有数百万行代码的系统只有在编译器和运行时系统帮助程序员找到错误时才能真正工作。只有具有强类型和其他特性的语言才能防止某些类型的编程错误。

假设你永远无法让最古怪、最迟钝的程序员相信 JavaScript 不是最差的，那么是否有可能至少提出一个折中的解决方案呢？

也许吧。输入 Typescript。

在本文中，我将从一个经验丰富的 Java 程序员的角度评估 Typescript，他已经掌握了 JavaScript、Node.js 和 Vue.js 等。在很大程度上。我很好奇 Typescript 能在多大程度上提高我用 JavaScript 编码的能力。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 工装和设置

[类型脚本](https://www.typescriptlang.org/)工具链是用 Node.js 写的，当然你的第一步是安装 [Node.js](https://nodejs.org/en/download/package-manager/) 和 npm。在本文中，我们将使用 node . js version 10 . x(10.12 是撰写本文时的最新版本)，因为它支持 ES6 模块。

你会从 [Typescript 快速入门指南](https://www.typescriptlang.org/samples/index.html)中了解到，一个人是这样安装 Typescript 的:

```
$ npm install -g typescript 
```

Enter fullscreen mode Exit fullscreen mode

建议全局安装 Typescript 包(-g 选项)。它安装一个命令 tsc，即 Typescript 编译器。编译器的目的是从类型脚本文件生成 JavaScript 源代码。JavaScript 是将要执行的内容，也是您应该部署到浏览器或作为 Node.js 模块的内容。

现在，你可以输入这个来查看用法说明:

```
$ tsc — help 
```

Enter fullscreen mode Exit fullscreen mode

另一个非常有用的工具是 [ts-node](https://www.npmjs.com/package/ts-node) ，它是直接执行 typescript 源代码的 node 命令的变体。

它是这样安装的:

```
$ npm install ts-node -g 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装完毕，ts-node 命令就可用了。

下一步是建立一个 Node.js 项目，以便遵循本文中的示例。首先，创建一个空白目录，然后运行 npm init 来设置一个空白的 npm/Node.js 项目。

在同一个目录中创建一个类型脚本配置文件 tsconfig.json，其中可以包含:

```
 {
 “compilerOptions”: {
 “lib”: [ “es5”, “es6”, “es7”,
 “es2015”, “es2016”, “es2017”, “es2018”, “esnext” ],
 “target”: “es6”,
 “moduleResolution”: “node”
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着要根据 ES5/ES6/etc 规范进行编译，这就是 Node.js 10.x 所实现的。它使用 ES6 规范输出代码，这也是 Node.js 10.x 中提供的。

你可以在 [Typescript 文档](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#types-typeroots-and-types)中找到更多相关信息。

最后要设置的是 Typescript 中对 Node.js 的特定支持。

我们将添加 Typescript 的 DefinitelyTyped 集合 JavaScript 生态系统中特定库或平台的巨大类型集合。

Typescript 包括实现声明文件的功能。这就是 DefinitelyTyped 项目所做的，创建一个明确指定的声明文件。查看[库](https://github.com/DefinitelyTyped/DefinitelyTyped)以获得更多信息，但是准备好因为缺少有用的文档而失望吧。

为 Node.js 添加 DefinitelyTyped 定义带来了对某些 Node.js 特性的支持。我们这样做是为了避免一个特定的问题，否则我们会与*进程*对象发生冲突。

Node.js 对其传统模块(基于 CommonJS 模块规范)和 ES6 模块所做的是不同的。在传统的 Node.js 模块中，注入了几个对象，像*模块*和*进程*。这些对象不是 ES6 模块规范的一部分，因此在 ES6 模块中不可用。

因为 Typescript 使用 ES6 模块，所以那些对象丢失了，阻止我们使用它们。在我们稍后运行的脚本中，我们需要从命令行获取参数，这当然使用了*进程*对象。

解决方案是安装包@types/node。这个包是 DefinitivelyTyped 集合的一部分，它提供了 Node.js 的定义。所需要的就是将这个包作为开发依赖项安装:

```
$ npm install — save-dev @types/node 
```

Enter fullscreen mode Exit fullscreen mode

### 快速举例

让我们开始快速入门教程的一个变种。创建一个文件，将其命名为 greeter.ts(注意“.ts "扩展名)包含:

```
function greeter(person: string) {
 return “Hello, “ + person;
}

let user = “Jane User”;
// let user = [0, 1, 2];

console.log(greeter(user)); 
```

Enter fullscreen mode Exit fullscreen mode

然后这样执行:

```
$ ts-node greeter.ts
Hello, Jane User 
```

Enter fullscreen mode Exit fullscreen mode

使用 ts-node 命令，我们不需要设置任何东西，它只是运行代码。当然，这不适用于生产，为此我们必须将类型脚本编译成 JavaScript。

编译是这样完成的:

```
$ tsc greeter.ts 
$ cat greeter.js 
function greeter(person) {
 return “Hello, “ + person;
}
var user = “Jane User”;
// let user = [0, 1, 2];
console.log(greeter(user)); 
```

Enter fullscreen mode Exit fullscreen mode

除了 greeter 函数的参数列表之外，Typescript 源代码是简单的 JavaScript。

```
function greeter(person: string) { … } 
```

Enter fullscreen mode Exit fullscreen mode

这就是 Typescript 开始帮助我们的地方。参数 *person* 是用 string 类型声明的。在常规 JavaScript 中，我们没有编译器的帮助来避免传递给这个函数的参数出现问题。调用者可以传递任何东西，在 JavaScript 中，这无关紧要。但是如果我们的函数只使用一个字符串就能正确执行呢？

在传统的 JavaScript 中，我们会像这样手动检查类型:

```
if (typeof greeter !== “string”) throw new Error(“bad type for person”); 
```

Enter fullscreen mode Exit fullscreen mode

像这样编写我们的代码会更健壮，但是我们大多数人都不愿意这么做。在 Kernighan 和 Plauger 的经典著作《编程风格的要素》中，作者强烈推荐使用防御性编码。也就是说，在假设它们是什么之前检查函数参数，因为否则，程序可能会崩溃。

这就是具有强类型检查的语言的用武之地。编译器(和运行时)介入做我们大多数人懒得做的事情。

在示例代码中，您将看到用户对象的两种变体，其中一种是数组。将源代码改为:

```
// let user = “Jane User”;
let user = [0, 1, 2]; 
```

Enter fullscreen mode Exit fullscreen mode

由此，我们引入了一个问题。用户对象是一个数组，与函数参数列表不匹配:

```
$ ts-node greeter.ts 
/Volumes/Extra/logrocket/typescript/start/node\_modules/ts-node/src/index.ts:261
 return new TSError(diagnosticText, diagnosticCodes)
 ^
TSError: ⨯ Unable to compile TypeScript:
greeter.ts(8,21): error TS2345: Argument of type ‘number[]’ is not assignable to parameter of type ‘string’. 
```

Enter fullscreen mode Exit fullscreen mode

这太棒了。JavaScript 中的编译时错误检查，警告我们有问题。因为，是的，我们确实有一个错误。更好的是，错误被清楚地描述了，我们可以理解该做什么。

由此，我们开始看到一场“胜利”正在形成。使用严格的类型检查语言，看起来我们背后有一个编译器来双重检查我们没有犯编码问题。

### Typescript 接口和一个更大的例子

Typescript 有一整套类似于 Java 或 C#语言的有趣特性。例如，它有一个类概念，是 ES-2015/6 中定义的超集，当然还增加了类型。但是仔细阅读文档，一个突出的特点是它们对接口的处理。

在 Java 和其他语言中，接口对象是灵活性的关键。接口不是一个完整的类。相反，它是一个可以应用于类的属性。例如，在 Java 中，java.util.List 接口由几个具体的类实现，如 ArrayList、LinkedList、Stack 和 Vector。您可以将这些列表实现中的任何一个传递给声明为接受列表的任何方法，并且该方法不关心具体的实现。

在老式的 JavaScript 中，我们有鸭子打字的想法来实现相同的概念。这个想法是，如果它像鸭子一样嘎嘎叫，那它一定是一只鸭子。在一个匿名 JavaScript 对象中，如果该对象有一个名为“嘎嘎”的字段，则该对象应该描述鸭子，不同种类的鸭子有不同的属性。

Typescript 接口功能是由 Typescript 语言语法支持的鸭式键入。一个声明接口 InterfaceName {..fields }，然后 InterfaceName 可以用作方法参数中的类型或对象中的字段。在编译期间，Typescript 编译器在进行静态代码分析时，将检查对象是否符合每个参数或字段上声明的任何接口。

尝试一个简单的例子，创建一个文件并将其命名为 interface1.ts，其中包含:

```
enum Gender {
 male = “male”, female = “female”
}

interface Student {
 id: number;
 name: string;
 entered: number;
 grade: number;
 gender: Gender
};

for (let student of [
 {
 id: 1, name: “John Brown”, 
 entered: 1997, grade: 4,
 gender: Gender.male
 },
 /\* {
 id: “1”, name: “John Brown”, 
 entered: 1997, grade: 4,
 gender: Gender.male
 }, 
 {
 id: 1, name: “John Brown”, 
 entered: 1997, grade: 4,
 gender: “male”
 } \*/
]) {
 printStudent(student);
}

function printStudent(student: Student) {
 console.log(`${student.id} ${student.name} entered: ${student.entered} grade: ${student.grade} gender: ${student.gender}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们所做的是定义一个接口和一些匿名对象。匿名对象还没有被声明来实现学生接口，它们只是对象。但是，这些匿名对象在一个将对象传递给 printStudent 调用的循环中。使用静态代码分析，Typescript 编译器看到每个对象必须符合学生接口。

当 Typescript 将一个对象与一个接口进行匹配时，它会通过接口定义逐个字段地与所提供的对象中的字段进行匹配。对于被视为实现接口的对象，它必须具有所有匹配的字段，并且类型必须匹配。您可以在[文档](https://www.typescriptlang.org/docs/handbook/type-compatibility.html)中找到更多信息。

运行上面显示的例子，您会得到这个:

```
$ ts-node interface1.ts
1 John Brown entered: 1997 grade: 4 gender: male 
```

Enter fullscreen mode Exit fullscreen mode

考虑一个结构不正确的对象与学生界面不匹配的可能性。此数组中被注释掉的条目旨在演示这种可能性。

取消对数组中这两个条目的注释，您将得到这样的结果:

```
$ ts-node interface1.ts
/Volumes/Extra/logrocket/typescript/start/node\_modules/ts-node/src/index.ts:261
return new TSError(diagnosticText, diagnosticCodes)
^
TSError: ⨯ Unable to compile TypeScript:
interface1.ts(31,18): error TS2345: Argument of type ‘{ id: string; name: string; entered: number; grade: number; gender: Gender; } | { id: number; name: string; entered: number; grade: number; gender: string; }’ is not assignable to parameter of type ‘Student’.
Type ‘{ id: string; name: string; entered: number; grade: number; gender: Gender; }’ is not assignable to type ‘Student’.
Types of property ‘id’ are incompatible.
Type ‘string’ is not assignable to type ‘number’. 
```

Enter fullscreen mode Exit fullscreen mode

我们再次成功地检测到了一个常见的问题——将结构不正确的对象传递给函数。数组的第二个元素——id 字段——使用了一个字符串而不是一个数值，导致了这里的错误。在数组的第三个元素中，性别字段使用一个简单的字符串，而不是性别.男性或性别.女性。

又一次胜利。但是在下一节，我们将看看 Typescript 是如何让我们失望的。

### 从外部存储器检索—执行时类型检查

我们的例子很简单，但是包含了一个重要的问题。数据存储在数组中，而它应该在外部存储中。显然，学生注册系统必须将数据存储在可靠的位置，而不是静态地列在源代码中。让我们解决这个问题。

当我们解决这个问题时，我们打开了一个蠕虫的罐子。由于 Typescript 只做编译时类型检查，它不能帮助我们在执行过程中发现问题。与 Java 或 C#等在运行时执行类型检查的语言相比，这是一个缺点。在这个过程中，我们将学习足够多的关于 Typescript 的知识，以便在结论中做出判断。

在前面的例子的基础上，我们将使用一个 YAML 文件作为外部数据存储。创建一个新文件 interface2.ts，包含以下内容:

```
import \* as yaml from ‘js-yaml’;
import { promises as fs } from ‘fs’;
import \* as util from ‘util’;

class Registry {
 private \_yaml: string;
 private \_parsed: any;

 async load(fn: string): Promise<void> {
   this.\_yaml = await fs.readFile(fn, ‘utf8’);
   this.\_parsed = yaml.safeLoad(this.\_yaml);
 }

 get students(): Student[] {
   if (this.\_parsed) {
     let ret: Student[] = [];
     for (let student of this.\_parsed.students) {
       try {
         ret.push({
           id: student.id,
           name: student.name,
           entered: student.entered,
           grade: student.grade,
           gender: student.gender
         });
       } catch (e) {
         console.error(`Could not convert student ${util.inspect(student)} because ${e}`);
       }
    }
    return ret;
  }
 }
}

let registry: Registry = new Registry();

let fn = process.argv[2];
registry.load(fn)
.then(res => { listStudents(); })
.catch(err => { console.error(err); });

async function listStudents(): Promise<void> {
 for (let student of registry.students) {
   printStudent(student);
 }
}

function printStudent(student: Student): void {
 console.log(`${student.id} ${student.name} entered: ${student.entered} grade: ${student.grade} gender: ${student.gender}`);
}

enum Gender {
 male = “male”, female = “female”
}

interface Student {
 id: number;
 name: string;
 entered: number;
 grade: number;
 gender: Gender
}; 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们添加了一个注册表类，用于处理从 YAML 文件中检索学生数据。目前，它支持的唯一数据是一组学生记录。显然，为了更完整的应用程序，其他数据项可以存储在 YAML 文件中。名为 students 的 getter 将访问学生信息记录的数组。

接下来，创建一个包含这些数据的文件 students . YAML:

```
students:
 — id: 1
   name: John Brown
   entered: 1977
   grade: 4
   gender: male
 — id: “2”
   name: Juanette Brown
   entered: 1977
   grade: “4”
   gender: female
 — id: 3
   name: Nobody
   entered: yesterday
   grade: lines
   gender: None-of-your-Business 
```

Enter fullscreen mode Exit fullscreen mode

在 YAML 语中，这是一个名为 students 的数组，它包含恰好与 student 接口匹配的字段。除了我们将看到的，没有一个数据与学生界面精确匹配。第三个值明显与学生界面有很大差异。

在 registry 类中，我们有一个函数 load，它读取 YAML 文本，然后将其解析为一个对象。数据存储在类的私有成员中。

Typescript 类定义是 ES6 引入的类对象的超集。其中一个新增功能是 private 和 protected 关键字，它们创建了一种信息隐藏措施。我们可以将这些本地数据存储在对象实例中，并确保其他代码不会访问这些数据。

在脚本的中间，您会看到我们实例化了一个注册表，然后调用 registry.load，后面跟着 listStudents，它逐步执行并打印学生列表。

在 registry.load 中，我们对类型声明非常明确。 *fn* 参数(文件名)声明为字符串，函数声明不返回任何内容。因为 load 是一个异步函数，Typescript 迫使我们将其声明为 promise，因为异步函数总是返回一个 Promise。这个语法表示一个*承诺，它决定使*无效。这种语法看起来像其他语言的泛型特性(这就是目的)。

在 Typescript 中，Foo 对象的*数组的语法是 Foo[]。因此，*学生*访问器方法被声明为返回一个学生对象数组。*

为了填充学生对象数组，我们从 YAML 文件中的数据创建简单的对象。碰巧我们的 YAML 文件中的字段与学生界面中定义的相匹配，所以这应该可以正常工作。

为了争取 YAML 的支持:

```
$ npm install js-yaml — save 
```

Enter fullscreen mode Exit fullscreen mode

程序是这样执行的:

```
$ ts-node interface2.ts students.yaml 
(node:9798) ExperimentalWarning: The fs.promises API is experimental
1 John Brown entered: 1977 grade: 4 gender: male
2 Juanette Brown entered: 1977 grade: 4 gender: female
3 Nobody entered: yesterday grade: lines gender: None-of-your-Business 
```

Enter fullscreen mode Exit fullscreen mode

第一行，关于 fs.promises，是使用 *fs Promises API* 的副产品。不要担心，因为我们用它来简化编码。

数据文件有三个条目，我们看到三个没有错误的输出。酷，它的工作，没有更多的事情要做，对不对？

不对。问题是所有这些项目都应该失败，因为数据类型与学生界面不匹配。对于第二个和第三个条目，有几个字段是字符串，而它们应该是数字，因此与学生界面中的类型不匹配。在任何情况下，性别字段都不包含性别枚举，相反，它总是包含一个字符串。

问题是 printStudent 函数中的类型检查只发生在**编译**时，而不是在**执行**时。这很容易看清自己。简单地运行这个来编译代码:

```
$ tsc 
```

Enter fullscreen mode Exit fullscreen mode

使用已经显示的配置，这将使用 tsconfig.json 中配置的目标将 Typescript 文件编译成 JavaScript。编译的 JavaScript 是实际执行的内容，因此在尝试理解您的程序为什么不按预期运行时，查看这些代码是有帮助的。

在编译后的代码 interface2.js 中，你会看到这是 printStudent 函数:

```
function printStudent(student) {
  console.log(`${student.id} ${student.name} entered: ${student.entered} grade: ${student.grade} gender: ${student.gender}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单明了的函数，但是你看到类型检查了吗？没有。在其余的编译代码中也看不到任何错误。同样，Typescript 出色的类型检查只发生在编译期间，而不是执行期间。

我们愚蠢地认为我们可以读取一个数组并直接把它作为学生对象使用。students getter 应该以防御性的方式编写，并检查我们收到的对象，以验证它们是否与 student 接口声明相匹配，并将它们映射到相应的对象实例中。让我们看看如何在 Typescript 中做到这一点。

如果你在记分，我们在前面两个部分经历的胜利现在已经失去光泽。为了获得完整的类型检查，我们必须自己实现执行时验证。

### Typescript 中的执行时类型检查

现在确定的主要问题是在执行过程中缺少类型检查。数据文件中的 students 数组可以包含任何内容，当它不正确时，我们的代码会像正确的一样传递它。防御性编程说我们应该清理，也就是说在使用数据之前对其进行规范化。

为了规范化数据，我们的代码必须处理这些情况:

*   所有字段都存在并且格式正确
*   必须检查*性别*字段中所有正确的性别值
*   数字字段必须容纳数字或字符串值，并将字段存储为数字
*   它必须检测具有完全疯狂值的字段
*   它必须检测丢失的字段

将 interface2.ts 复制为 interface3.ts，并准备进行更改。

让我们首先创建一个 StudentImpl 类来实现学生接口。这样命名一个类 *StudentImpl* 是不是散发着“前 Java 程序员”的气息？多么难听的类名，但在 Java 中却是家常便饭。

如果我们简单地用这个:

```
class StudentImpl implements Student {
 id: number;
 name: string;
 entered: number;
 grade: number;
 gender: Gender;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们不会得到任何东西，因为没有任何东西的运行时强制。

在[类型脚本文档](https://www.typescriptlang.org/docs/handbook/classes.html)中，建议将数据存储在私有字段中，并使用 get/set 访问函数。

现在，类的定义应该是:

```
class StudentImpl implements Student {
 private \_id: number;
 private \_name: string;
 private \_entered: number;
 private \_grade: number;
 private \_gender: Gender;

 get id(): number { return this.\_id; }
 set id(id: number) { this.\_id = id; }
 .. similar pattern for the other fields
}; 
```

Enter fullscreen mode Exit fullscreen mode

但这并没有说明以下情况:

*   YAML 使用字符串而不是数字的情况
*   格式错误的数字
*   缺失的字段

经过大量的实验，我们开发了这个类定义:

```
class StudentImpl implements Student {
 constructor(id: number | string, 
             name: string, 
             entered: number | string,
             grade: number | string, 
             gender: string) {
   this.setID(id);
   this.setName(name);
   this.setEntered(entered);
   this.setGrade(grade);
   this.setGender(gender);
 }
 private \_id: number;
 private \_name: string;
 private \_entered: number;
 private \_grade: number;
 private \_gender: Gender;

 get id(): number { return this.\_id; }
 set id(id: number) { this.setID(id); }
 setID(id: number | string) {
   this.\_id = normalizeNumber(id, ‘Bad ID’);
 }
 get name() { return this.\_name; }
 set name(name: string) { this.setName(name); }
 setName(name: string) {
   if (typeof name !== ‘string’) {
     throw new Error(`Bad name: ${util.inspect(name)}`);
   }
   this.\_name = name; 
 }

 get entered(): number { return this.\_entered; }
 set entered(entered: number) { this.setEntered(entered); }
 setEntered(entered: number | string) {
   this.\_entered = normalizeNumber(entered, ‘Bad year entered’); 
 }

 get grade(): number { return this.\_grade; }
 set grade(grade: number) { this.setGrade(grade); }
 setGrade(grade: number | string) {
   this.\_grade = normalizeNumber(grade, ‘Bad grade’);
 }

 get gender(): Gender { return this.\_gender; }
 set gender(gender: Gender) { this.setGender(gender); }
 setGender(gender: string | Gender) {
   this.\_gender = parseGender(gender);
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，每个字段的模式是:

*   在对象定义中将数据存储声明为私有字段
*   声明一个简单的 getter 函数来访问该字段
*   声明一个简单的 setter 函数，调用 *setFieldName*
*   声明一个名为 *setFieldName* 的函数，该函数在将数据存储到字段中之前验证数据

您应该注意到 *setFieldName* 方法中的参数类型有一点奇怪。等等，我们会说到的。

我们还有一个*构造器*，它将帮助创建对象实例。要使用*构造函数*，在注册表类中把*学生*的 getter 改成这个:

```
get students(): Student[] {
 if (this.\_parsed) {
   let ret: Student[] = [];
   for (let student of this.\_parsed.students) {
     try {
**ret.push(new StudentImpl(  
         student.id, student.name,   
         student.entered, student.grade,   
         student.gender));**  
     } catch (e) {
       console.error(`Could not convert student ${util.inspect(student)} because ${e}`);
     }
   }
   return ret;
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，我们不是将一个匿名对象推入数组，而是推入一个 StudentImpl。

现在让我们来讨论一下 *setFieldName* 方法的参数:

```
_setFieldName_(grade: number | string) { .. } 
```

Enter fullscreen mode Exit fullscreen mode

这是一个名为*联合类型*的类型脚本特性。当代码显示“等级:数字|字符串”时，您应该理解为“*”参数等级可以有类型数字或类型字符串*。

在绝大多数语言中，每个变量都用一种类型声明，而 Typescript 允许变量有两种或多种类型。这乍一看似乎很奇怪，但却极其有用。为了使它更有趣和不同，另一个 Typescript 特性，*交集类型，允许您声明一个变量类型为列出的每种类型的组合。*

 *在这个应用程序中，我们有一个数据文件，其中的字段可以是数字或字符串。在前面列出的问题中，我们说过数字字段必须可以从数字或字符串值初始化，并存储为数字。参数类型定义(联合类型)是实现该目标的第一步。第二步是 normalizeNumber 函数，我们一会儿就会看到，它也必须使用联合类型，并在进行类型检查以确保正确性的同时处理从联合类型到数字的转换。

理想情况下,“set”访问器就足够了，我们不需要第三个函数。但是 Typescript 编译器不允许这样，因此我们必须引入第三个函数。但是我们必须记住总是调用第三个函数吗？

我们有点偷偷摸摸。每个 setter 继续调用相应的 *setFieldName* 函数。检查编译后的代码，我们看到因为没有编译时类型检查，setter 最终会做正确的事情:

```
get entered() { return this.\_entered; }
set entered(entered) { this.setEntered(entered); }
setEntered(entered) {
 this.\_entered = normalizeNumber(entered, ‘Bad year entered’);
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们已经知道的，在执行时，JavaScript 代码并不强制(正如我们在这里看到的)在 Typescript 代码中编写的类型。因此，无论我们向 setter 提供什么类型，它都将被传递给相应的 *setFieldName* 函数，执行时类型检查将执行，提供我们所寻求的安全性。

我们忽略了执行时类型检查和转换所需的函数。

```
function normalizeNumber(num: number | string,
                         errorIfNotNumber: string): number {
  if (typeof num === ‘undefined’) {
    throw new Error(`${errorIfNotNumber} — ${num}`);
  }
  if (typeof num === ‘number’) return num;
  let ret = parseInt(num);
  if (isNaN(ret)) {
    throw new Error(`${errorIfNotNumber} ${ret} — ${num}`);
  }
  return ret;
}

function isGender(gender: any): gender is Gender {
 return typeof gender === ‘string’
    && (gender === ‘male’ || gender === ‘female’);
}

function parseGender(gender: string): Gender {
 if (!isGender(gender)) throw new Error(`Bad gender: ${gender}`);
 return (gender === ‘male’) ? Gender.male : Gender.female;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 normalizeNumber 中，我们进行各种检查，要么返回一个数字，要么抛出一个错误。它依赖于 parseInt 函数的行为，如果在输入中找不到可解析的数字，它就返回 NaN。通过检查 NaN，我们已经自动检测到一系列可能的错误情况。

同样，在 parseGender 中，我们进行各种检查，要么返回性别，要么抛出错误。

这里的关键技术是 Typescript 所谓的类型保护。这些是运行时表达式，保证变量的类型是预期的。性别枚举的类型保护是代码中显示的 isGender 函数。isGender 中的返回类型，“ *foo 是类型*”，是一个布尔值，true 或 false，表示命名参数是否匹配那个类型。

在 interpolateNumber 函数中，我们有一个内嵌类型保护:

```
if (typeof num === ‘number’) return num; 
```

Enter fullscreen mode Exit fullscreen mode

在 Typescript 文档的其他地方，据说编译器会将这种模式识别为类型保护。为此，它将识别的*类型和*表达式的*实例。*

类型保护与 Typescript 编译器执行的*类型推断*密切相关。编译器在编译期间执行大量的静态代码分析。当它识别出这种模式时，它可以推导出每个变量的允许类型。

但这并不是 Typescript 独有的。去掉这些类型，你就有了用于防御性编程的普通 JavaScript 代码。类型保护只是实现运行时类型检查的一种方式，无论如何我们都应该这样做。正如我们前面提到的，我们大多数人不会像这样防御性地编写代码。似乎我们将从用类型脚本的方式编写代码、类型保护和所有这些中获得足够的价值，这将激励我们实际实现类型保护。

我们现在得到这个:

```
$ ts-node interface3.ts students.yaml 
(node:10048) ExperimentalWarning: The fs.promises API is experimental
Could not convert student { id: 3,
 name: ‘Nobody’,
 entered: ‘yesterday’,
 grade: ‘lines’,
 gender: ‘None-of-your-Business’ } because Error: Bad year entered NaN — yesterday
1 John Brown entered: 1977 grade: 4 gender: male
2 Juanette Brown entered: 1977 grade: 4 gender: female 
```

Enter fullscreen mode Exit fullscreen mode

我们有运行时类型检查。代码的每个角落都有类型定义，这不仅有助于 Typescript 编译器，也有助于将来代码的维护者了解什么是什么。

目前的结果是，我们的代码能够正常化两个学生记录(可以正常化)，并且在第三个记录中发现一个错误，因为它不能正常化。

为了得到这个结果，我们必须实现我们自己的执行时类型检查。Typescript 在这方面没有帮助我们，但是我们应该使用防御性的编码实践。

### 结论

虽然我们只是触及了 Typescript 的表面，但我们已经看到了足够多的东西来评估它的有用性。一个 Java 或 C#程序员会舒服到足以编写一个大型系统吗？

只要编码者明白它的局限性，Typescript 就是一种非常有用的编程语言。

在每一个方面，Typescript 都提供了其他语言中通常没有的特性。类型声明是其他语言的超集，类和接口声明也是如此。Typescript 的类定义是 ES-2015 中添加到 JavaScript 的超集。我们没有触及*模块*和*名称空间*特性，这两个特性都是常规 JavaScript 中可用特性的超集。

换句话说，Typescript 的特性集超出了人们在其他语言或 JavaScript 中所习惯的范围。

Java 或 C#程序员会觉得使用 Typescript 类来描述用来组织代码的类层次结构很舒服。

Typescript 的主要缺点是它的类型检查只在编译时执行。正如我们看到的，没有运行时类型检查，相反，我们有自己编码的开销。而使用 Java、C#或者其他语言的程序员就没有这个开销了。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * **