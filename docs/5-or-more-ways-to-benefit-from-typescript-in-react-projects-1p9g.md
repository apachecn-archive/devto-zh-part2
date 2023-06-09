# React 项目中受益于 TypeScript 的 5 种(或更多)方式

> 原文：<https://dev.to/brightdevs/5-or-more-ways-to-benefit-from-typescript-in-react-projects-1p9g>

在[最近的帖子](https://brightinventions.pl/blog/modern-way-getting-started-react-typescript/)中，我们讨论了如何使用 TypeScript 启动一个新的 React 项目，以从类型检查的所有好处中受益，并从我们的代码库中消除整个类别的错误和打字错误。现在让我们看看如何将基于 TypeScript 的类型定义应用到 React 项目中，以充分利用它。

作为开始，对于我们希望拥有 TypeScript 支持的每个库，我们需要首先获取类型定义。通常，这样做的方法是使用[明确类型化的](https://github.com/DefinitelyTyped/DefinitelyTyped)项目并运行:

```
npm install --save-dev @types/react @types/react-dom 
```

Enter fullscreen mode Exit fullscreen mode

## 基础知识-组件

React 中的组件在大多数情况下是扩展 React 提供的`Component`类的 ES6 类。TypeScript 向这个基类定义添加了两个泛型参数——第一个定义了我们的 props 是什么，第二个用于本地组件的状态。

```
interface HelloProps {
    greeting: string
}

interface HelloState {
    wasDisplayed: boolean
}

class HelloWorldComponent extends React.Component<HelloProps, HelloState> {} 
```

Enter fullscreen mode Exit fullscreen mode

通过添加这两个定义，我们不仅为组件添加了代码完成功能。在这里，我表示我期望一个`greeting`属性，并将其标记为非可选的(冒号前没有`?`符号，也没有允许`undefined`通过的联合类型)。编译器现在成功地阻止我们在没有传入正确的`greeting`属性的情况下使用这个组件。

[![Props completeness is enforced](img/71bcbb40efd8d2bf922c606d97779aa0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jKstA2yw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z8fy7o8x27fwxy3ldkss.png)

### 道具&状态(im)易变性

但是还有更多。在 React 中，属性和状态都是不可变的。Props 仅用于将数据放入组件，状态将通过专门的`setState`方法进行修改。多亏了定义为类型`Readonly<>`的道具和状态，我们才不会意外地改变它:

[![Props immutability is enforced](img/1aa3647da85c69b347e554bf0808bef3.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--HgYs-q5_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2x2dcpi1pj3ytgpooypn.png) ￼

我们也不允许对其进行猴子修补，即在那里添加新的属性:

[![Props are frozen](img/92b6a4ad0c2d4381035d90698c9dd153.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--69TTTmub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ewjuoraomk4hg1ichsey.png) ￼

话虽如此，我们必须意识到`Readonly<>`不是深度的(递归的)——它只保护我们不去改变对象的根级别，没有什么能保护它的子对象:

```
interface State {
    inner: {stuff: string}
}

// this is still possible
this.state.inner.stuff = "inner’s properties are still mutable" 
```

Enter fullscreen mode Exit fullscreen mode

直到 React 的类型实现了对深度只读的正确支持([，从 TypeScript 2.8](https://github.com/Microsoft/TypeScript/pull/21316) 开始这是可能的)，我们能够(并且应该)做的最好的事情就是自己确保所有的属性和组件的状态也被标记为`Readonly<>`(以及它的内部属性，无限地):

```
interface State {
    inner: Readonly<{stuff: string}>
}

// now we’re safe again
this.state.inner.stuff = "this does not compile anymore" 
```

Enter fullscreen mode Exit fullscreen mode

### `setState`正确性

我们自动避免的另一类错误是当我们用无效对象调用`setState`时。这个函数的第一个参数是用一个相当隐晦的类型声明定义的:

```
state: ((prevState: Readonly<S>, props: P) => (Pick<S, K> | S | null)) | (Pick<S, K> | S | null), 
```

Enter fullscreen mode Exit fullscreen mode

但是当逐段阅读时，它告诉我们要么需要传递一个返回`Pick<S, K> | S | null`的函数，要么直接返回它。而那个`Pick<S, K> | S | null`的东西是——倒着读——要么是`null`，全状态对象本身(`S`，要么是一个带有状态键子集的对象( [`Pick<S, K>`](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types) )。长话短说，我们无法传递与我们的状态定义不匹配的新状态值对象。下面是 TypeScript 编译器给出的错误:

[![State correctness is enforced](img/a4226288921144ac39f2622d0e9ab50d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iibilAN_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k3cud8ntmv7ujiwp26be.png)

## 无状态组件

经验丰富的 React 开发人员可能熟悉可用组件的最简单形式——[无状态功能组件](https://hackernoon.com/react-stateless-functional-components-nine-wins-you-might-have-overlooked-997b0d933dbc)。这些被定义为接收道具并返回 JSX 元素的纯函数。就这么简单，从类型的角度来看，通常只需要像对待任何其他函数一样对待它——指定它的参数类型，并可选地指定返回类型:

```
interface InputProps {
    value: any
    onChanged: () => void
}

function Input(props: InputProps) {
    return <input type="text" onChange={props.onChanged} value={props.value}/> } 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们想为我们的无状态组件指定`propTypes`或`defaultProps`，我们可能会遇到一个问题。TypeScript 会抱怨普通函数没有定义那种属性:

[![PropTypes cannot be added to plain function](img/55347f51a34379f1aab8e87622b0f32b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xKBc13tl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b7oj8wls2np3i7ghes5r.png)

我们可以通过稍微不同的方式声明我们的组件来解决这个问题:

```
const Input: React.StatelessComponent<InputProps> = function (props) {
    return <input type="text" onChange={props.onChanged} value={props.value}/> }

Input.propTypes = {
    value: PropTypes.any.isRequired
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，让我们停下来想一想，我们在这里试图实现什么。TypeScript 不是已经为我们提供了类似级别的类型安全，而且更加方便了吗？我发现[根本不再需要使用 PropTypes】。](https://dbushell.com/2017/04/19/typescript-instead-of-react-proptypes/)

## 事件

我们在 React 世界中的下一站可能会使用更强的类型，那就是围绕事件系统。每当我们希望组件对用户动作作出反应时，我们都会处理事件。让我们再一次看到我们简单的`Input`组件:

```
interface InputProps {
    value: any
    onChanged: () => void
}

function Input(props: InputProps) {
    return <input type="text" onChange={props.onChanged} value={props.value}/> } 
```

Enter fullscreen mode Exit fullscreen mode

与每个事件处理程序一样，`onChange`属性将事件对象作为唯一的参数。让我们用我们的`InputProps`接口定义来表达。`Event`是一个由 HTML 规范本身定义的名称，所以让我们首先在这里尝试使用它:

```
onChanged: (event: Event) => void 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这似乎不是我们应该关心的事件:

[![React does not use native HTML events](img/b2f0b012f848f8722e26ad631a09c91d.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s---G9jPODz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dpbpsyndvfl4o1n069rw.png) ￼￼
￼
这个相当冗长的错误给出了事件的预期类型，这是最重要的——请看它的最后一行。React 传递的事件对象实际上被类型化为`ChangeEvent<HTMLInputElement>`并且这个类型似乎没有扩展 HTML 内置的`Event`类型。这是有意的，因为 React 不直接使用 HTML 事件——而是使用[合成事件](https://reactjs.org/docs/events.html)包装器。

当我们将我们的`Event`类型定义更改为由事件类型和元素类型共同确定的合成事件类型时，我们就可以:

```
onChanged: (event: React.ChangeEvent<HTMLInputElement>) => void 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们最好的可能的置信度，我们可以期望得到一个论点。然而，这极大地降低了灵活性。对于在多种类型的 HTML 元素(例如，`<input>`和`<select>`)上触发的事件，我们不能再使用相同的变更处理程序:

[![Events on different HTML elements are not compatible](img/0e415303326cfc88f012dfd1377a6013.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XxAVKwwK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9wmqnje2nmg2t3k9atrt.png)

我们得到一个错误，指出在接近结尾时`HTMLSelectElement`不可分配给`HTMLInputElement`。事实上，它不是，我们的处理程序被定义为只接受前者，我们不能直接重用那个处理程序。如果我们希望将同一个处理程序附加到多种类型的事件(例如，更改、点击、鼠标交互等),也会出现类似的问题。)–`ChangeEvent<T>`和`MouseEvent<T>`不兼容。

幸运的是，TypeScript 提供了一组不错的类型系统特性，可能会对我们有所帮助。首先，我们可以使用共同的祖先事件类型，而不是特定的`ChangeEvent`–`SyntheticEvent`是一个很好的选择。描述我们处理的元素类型的泛型参数更麻烦。我们可以试着用一个`HTMLElement`基类，在某些情况下它就足够了。但是我通常的通用事件处理是处理多个表单元素并访问它的值属性。显然，对于所有定义了`value`属性的表单元素来说，没有一个通用的类型。我们至少有两种方法来解决这个问题。首先，[联合类型](http://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)的仁慈，在这里我们可以指定一个我们想要处理的可选类型，它的公共属性将免费提供:

```
onGenericEvent: (event: React.SyntheticEvent<HTMLSelectElement | HTMLInputElement>) => void 
```

Enter fullscreen mode Exit fullscreen mode

这很好，也很明确，尽管如果我们想一次处理多个元素，它的伸缩性不好。第二个解决方案使用了[结构类型兼容性](https://basarat.gitbooks.io/typescript/docs/types/type-compatibility.html#structural)，这是另一个非常简洁的 TypeScript 的类型系统功能，允许我们仅通过结构来定义和比较类型。如果我们的情况是只读取被处理元素的`value`属性的值，我们可以在我们的处理程序类型定义中明确地声明它:

```
onGenericEvent: (event: React.SyntheticEvent<{value: string}>) => void 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 的类型系统允许我们选择在我们的情况下哪个级别的特异性和灵活性是合适的。

### 丑案的仿制`setState`

不幸的是，不全是鲜花和玫瑰。我在 React 中处理表单的典型场景是从表单元素的变更事件值中直接设置组件的状态属性:

```
<input type="text" name="firstName" 
       onChange={event => this.setState({firstName: event.currentTarget.value})} /> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过消除输入名称的重复，假设名称与我们想要在状态中设置的键匹配，并使用来自 ES6 规范的[增强的对象文字](http://www.benmvp.com/learning-es6-enhanced-object-literals/)(方括号中指定了动态属性名称的东西):

```
<input type="text" name="firstName" 
       onChange={event => this.setState({[event.currentTarget.name]: event.currentTarget.value})} /> 
```

Enter fullscreen mode Exit fullscreen mode

正如我们之前看到的，TypeScript 确保我们传递给`setState`的对象的键与组件状态的实际属性相匹配。但是在这里，TypeScript 编译器(至少从 2.6.1 开始)并没有聪明到计算出事件的目标`name`属性的实际值是多少，即使在这种情况下它只能等于`firstName`。对于 TypeScript 来说，它是一个通用字符串，不幸的是，它太宽了，对于我们的`setState`调用来说是无效的:

[![TypeScript is unable to specify down setState parameter types](img/9e8cedaf4b612c0e9797f40b85909251.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fAyN4ism--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r9a0xi8n77lkyc4c6klh.png)

我们可以通过类型转换来解决这个问题，通知 TypeScript 编译器我们可能从`event.currentTarget.name`中得到的值的范围是什么(假设`State`描述了我们组件的状态)。`keyof State`结构通知编译器，那里的字符串可能只有那些由`State`接口结构定义的:

```
<input type="text" name="firstName" 
       onChange={e => this.setState({[e.currentTarget.name as keyof State]: e.currentTarget.value})}/> 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果我们想避免类型转换，我们可以通过确保总是传递完整状态(包括预期的修改)来满足`setState`调用。它实际上利用了比部分状态更新稍微独立的 React 特性，但是应该以相同的方式运行:

```
<input type="text" name="firstName" 
       onChange={e => this.setState({...this.state, [e.currentTarget.name]: e.currentTarget.value})}/> 
```

Enter fullscreen mode Exit fullscreen mode

注意，我在这里使用了还不标准的[对象传播操作符](https://github.com/tc39/proposal-object-rest-spread)。它创建了一个`this.state`的副本，并替换(或添加)了这个副本的一个属性——在这种情况下，它将把`firstName`属性设置为输入的`value`属性的值，反映用户在框中输入的内容。

## 更何况？

您可能已经注意到，所有的 HTML 元素都将其属性映射到`HTML*Element`类型，无论何时我们对元素进行操作，都会从中受益。类似地，CSS 属性的良好子集被映射到`CSSProperties`接口，该接口定义了特定 CSS 属性可能使用的所有预定义值。如果我们在组件中使用任何形式的[内联样式](https://reactjs.org/docs/dom-elements.html#style)，这可能会很有用。它将提供一个适当的代码完成，并在某些情况下确保我们的 CSS 定义的有效性:
￼
[![TypeScript helps with CSS validity](img/456928a0b8e498d9539b7df969c7f528.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--eOeqDuhu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5e3j3s63fr34ehbbjk44.png) ￼￼￼

我希望您已经感觉到 TypeScript 可以为您的 React 代码库提供很多好处，尽管我们只是触及了冰山一角。在下一篇文章中，我们将添加 Redux，看看 TypeScript 如何帮助我们。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl) [Github](https://github.com/NOtherDev)