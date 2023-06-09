# 打字稿和反应:禁止 Lambdas？

> 原文：<https://dev.to/jonhilt/typescript-and-react-lambdas-are-forbidden-3eah>

在[学习 React(作为. net 开发者)](https://dev.to/jonhilt/diary-of-a-net-developer---learning-react-4o5f)中，我建议一起使用 React 和 Typescript。

我通常是 Typescript 的粉丝，并欣赏它如何捕捉可能会漏网(并进入生产)的打字错误/错误。

但是重要的是要认识到使用 Typescript 学习 React 可能比使用 Javascript 学习 React 更难。

尤其是因为大多数 React 文档、教程、博客帖子都是用 Javascript 编写的。

这意味着您必须弄清楚 Typescript 部分是如何工作的，并相应地调整您看到的示例。

到目前为止，创建 React Typescript 项目通常涉及使用[这个 Typescript Create React App 模板](https://github.com/wmonk/create-react-app-typescript)。

如果你用这个模板开发一个应用程序，然后试着按照官方的 React javascript 教程去做，你很可能会遇到让你挠头的打字错误。

特别是一个提前到来的，如果你刚刚开始，会有点困惑。

**停止按压！先看看这个...**

本文的其余部分提到了一个问题，如果您使用这里的中的[模板创建一个新的 React Typescript 项目，您可能会遇到这个问题。](https://github.com/wmonk/create-react-app-typescript)

但是，从“Create React App”2.1 开始，Typescript 就被“烘焙”了；因此，您可以使用此命令创建一个新的 Typescript React 应用程序...

```
npx  create-react-app  app-name  —-typescript 
```

这种方法避免了本文其余部分提到的问题！

你可以在这里查看我制作的展示新 CRA 模板的简短视频...T3】

但是如果您对 TSLint 及其工作原理感兴趣(或者您仍然在使用 wmonk 模板),请继续阅读...

## JSX 没有λ

当你开始学习教程时，比如在 reactjs.org，你总是会看到这样的东西...

```
<button className="square" onClick={() => alert('click')}>
    {this.props.value}
</button> 
```

注意`onClick`事件处理程序使用 ES2015 箭头语法(如果你是 C#开发人员，你会习惯于把它称为 lambda)。

现在，如果你试图用你的 React Typescript 项目(用 wmonk 模板创建的)声明一个这样的内联函数，你会遇到一个错误。

> JSX 属性中禁止使用 Lambdas，因为它们会影响渲染性能

这是怎么回事？

wmonk React Typescript 模板使用 TSLint 来实施某些规则。

您可以将“linters”视为在编译和运行代码之前检查潜在问题的工具。

对于特定的编程语言来说，它们强制执行许多被认为是“良好实践”的规则。

其中一个规则叫做 **jsx-no-lambda** 。以下是解释(摘自 [GitHub 回购](https://github.com/palantir/tslint-react))。

> 在渲染调用堆栈内创建新的匿名函数(使用函数语法或 ES2015 arrow 语法)会影响纯组件
> 渲染。当在两个 lambdas 之间进行相等检查时，React 将总是认为它们是不相等的值，并强制组件比必要的更频繁地重新渲染
> 。

现在有很多解决方法。您可以创建一个命名函数并引用它...

```
public render() {
    return (
    <button className="square" onClick={buttonClicked}>
        {this.props.value}
    </button>) }

private buttonClicked = () => {
    alert('click');
} 
```

但是如果您只是想跟着教程走，并且希望 TSLint 在您这样做的时候不要碍事，您可以在`tslint.json`配置文件中禁用规则。

```
{  "extends":  [  "tslint:recommended",  "tslint-react",  "tslint-config-prettier"  ],  "rules":  {  "jsx-no-lambda":  false  }  } 
```

在这里，您可以随意关闭规则。

自然，这需要一句谨慎的话！

如果您关闭所有的规则，那么 TSLint 就没有存在的意义了！所以，不管你愿不愿意关掉它们，三思而后行是值得的！

也就是说，似乎有一个普遍的共识，即`jsx-no-lambda`规则通常是不必要的，如果你刚刚开始，消除任何可能妨碍你的摩擦是值得的。

因此，关闭规则(或者使用官方的 Create React Typescript 模板)，然后[跟随教程](https://reactjs.org/tutorial/tutorial.html)！

**记住，你可以在这里查看我制作的展示新 CRA 模板的简短视频...T3】**