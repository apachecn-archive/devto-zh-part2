# 学习反应-第 2 部分-了解 JSX

> 原文：<https://dev.to/felipegalvao/learn-react---part-2---getting-to-know-jsx-2gfl>

[最初发表在我的博客上](http://felipegalvao.com.br/blog/2018/09/06/learn-react-getting-to-know-jsx/)

在我们上一篇关于 React 的文章中，我们用`Webpack`创建了一个 React 项目，有一些不错的特性。现在，我们将探索一下 JSX。JSX 是 Javascript 的语法扩展，广泛用于 React 应用程序。

这篇文章是我介绍 React 基础知识的系列文章的一部分:

1.  **[使用 Webpack 4 (+ CSS / SASS)简单设置 React 应用程序](https://dev.to/felipegalvao/simple-setup-for-a-react-application-with-webpack-4--css--sass-bef)**
2.  **了解 JSX**

有了 JSX，你可以编写与 HTML 非常相似的 Javascript 代码，这使得在你的 web 应用程序上开发前端变得很容易。让我们看看你能为 JSX 做些什么。

让我们从上一篇文章开始吧。[点击这里](https://dev.to/felipegalvao/simple-setup-for-a-react-application-with-webpack-4--css--sass-bef)看一看，你可以找到 Github 库的链接，你可以克隆并跟随。

## 基本特征

为了让我们都在同一个页面上，我们从这个非常基本的 React 代码开始:

```
import React from "react";
import ReactDOM from "react-dom";

const Index = () => {
  return <div>Hello React!</div>; };

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，当我们在函数内部返回`div`元素时，我们已经在使用 JSX 了。

我们要做的第一件事是在返回的元素中包含一个变量值。最基本的方法是在花括号`({ })`中包含变量名。我们来看看:

```
import React from "react";
import ReactDOM from "react-dom";

const name = "Felipe";

const Index = () => {
  return <div>Hello { name }!</div>; };

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们定义了`name`变量，然后将它包含在元素中。当您访问您的应用程序时，您可以看到变量值在`div`元素中。

我们也可以调用一个预定义的函数:

```
import React from "react";
import ReactDOM from "react-dom";

const name = "Felipe";

function yellName(nameToYell) {
  return nameToYell.toUpperCase();
}

const Index = () => {
  return <div>Hello { yellName(name) }!</div>; };

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

当你刷新你的浏览器时，你会注意到这个函数被调用了，并且`name`变量中的所有字符现在都是大写的。

## 也有一些限制

现在，为了改变，让我们看看 JSX 的限制。尝试在当前的`div`之后添加一个新段落。您的代码将是这样的:

```
import React from "react";
import ReactDOM from "react-dom";

const name = "Felipe";

function yellName(nameToYell) {
  return nameToYell.toUpperCase();
}

const Index = () => {
  return <div>Hello { yellName(name) }!</div> <p>Here, a paragraph!</p>;
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

嗯，如果你刷新你的浏览器窗口，你会注意到没有任何东西被渲染。如果您检查浏览器控制台或运行 Webpack 服务器的终端，您可以看到以下错误消息:`SyntaxError: Adjacent JSX elements must be wrapped in an enclosing tag`。

这个信息非常清楚。当您想要返回两个 JSX 元素时，您需要将它们包含在一个标签中。定义什么将被渲染的函数必须总是返回一个外部元素。在它的内部，您可以拥有任意多的元素，但是应该只有一个外部元素。

在 React 16 发布之前，处理这个问题的方法是将所有元素放在一个`div`中。您的代码应该是这样的:

```
import React from "react";
import ReactDOM from "react-dom";

const name = "Felipe";

function yellName(nameToYell) {
  return nameToYell.toUpperCase();
}

const Index = () => {
  return (
    <div>
      <div>Hello { yellName(name) }!</div>
      <p>Here, a paragraph!</p>
    </div>
  );
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

现在，`div`包含了 2 个元素，您的应用程序将被渲染，这 2 个元素将被正确显示。请注意，您也不需要在一行中包含所有内容。为了更好的组织和可读性，可以将元素分成几行。

使用 React 16(如果您关注了我们上一篇 React 文章，您应该会使用它)，您可以返回一个元素列表。然后，你的代码会像这样:

```
import React from "react";
import ReactDOM from "react-dom";

const name = "Felipe";

function yellName(nameToYell) {
  return nameToYell.toUpperCase();
}

const Index = () => {
  return [
    <div>Hello { yellName(name) }!</div>,
    <p>Here, a paragraph!</p>
  ];
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您刷新浏览器时，您会注意到您的应用程序将继续以同样的方式工作。在我看来，列表更好看，你不必创建如果不是为了满足 React 的规则就不会存在的 HTML 元素。但是请随意使用您喜欢的方式。

## 控制和重复结构

继续 HTML 中的 Javascript，可以使用控制和重复结构。例如，条件渲染可以使用一个简单的`if`块来实现，就是您在标准 Javascript 中习惯使用的那个:

```
import React from "react";
import ReactDOM from "react-dom";

const name = "Felipe";

function sayHello(name) {
  if (name) {
    return <p>Hello { name }!</p>;
  } else {
    return <p>Hello, nobody</p>;
  }
}

const Index = () => {
  return sayHello(name);
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

刷新您的浏览器选项卡，您将看到呈现的段落是第一个，它包括我们定义的变量。现在，注释我们定义`name`变量的那一行，并刷新您的浏览器选项卡。现在正在渲染的段落是第二个，带有`Hello, nobody`消息。

进行条件渲染的另一种方式是使用三元运算符。它是这样工作的:`{ condition ? returns this if condition is true : returns this if condition is false }`。让我们看看上面使用的同一个例子，但是使用了三元运算符。

```
import React from "react";
import ReactDOM from "react-dom";

const name = "Felipe";

const Index = () => {
  return (
    <div>
      { name ? <p>Hello { name }!</p> : <p>Hello, nobody</p> }
    </div>
  )
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这种方式比使用一个`if`块和两块模板更简洁。您可以像我们之前一样进行测试。刷新浏览器选项卡，检查呈现的段落是否是带有`name`变量的段落。然后，注释我们定义它的行，并再次刷新浏览器。

还要注意，如果条件为真，也可以只呈现某些内容，如果条件不为真，则不呈现任何内容。错误横幅就是一个很好的例子。为此，我们可以使用不带 else 部分的 if 块，或者使用三元运算符并在最后部分返回 null。让我们看一个三元运算符的例子:

```
import React from "react";
import ReactDOM from "react-dom";

const name = "Felipe";

const Index = () => {
  return (
    <div>
      { name ? <p>Hello { name }!</p> : null }
      <p>How are you?</p>
    </div>
  )
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的代码，两个段落都将被呈现。但是，如果您注释定义`name`变量的那一行，您将会看到带有 Hello 的段落不会显示。

要遍历一个项目集合，我们可以使用`map`，而不是使用`for`循环，在这里我们需要追加要呈现的元素，它已经以您需要的方式返回了列表。让我们看看它是如何工作的:

```
import React from "react";
import ReactDOM from "react-dom";

const names = ["Felipe", "Jose", "Alfredo"];

const Index = () => {
  return <div>
    {
      names.map((name, count) => {
        return <p key={ count }>Hello, { name }!</p>
      })
    }
  </div>; };

ReactDOM.render(<Index />, document.getElementById("index")); 
```

Enter fullscreen mode Exit fullscreen mode

在一个`map`中，你遍历一个集合，无论你返回什么都将是新列表的一个元素。

注意，我们为每个元素上的`key`定义了一个值。当我们处理元素组时，这个值是必需的，以便 React 可以正确地呈现所有内容。如果你不定义它，你的应用程序会渲染，但你会得到一个警告，你可能会遇到一些奇怪的行为时使用它。

## 元素属性

开发 React 应用程序时，另一件重要的事情是 JSX 元素的属性。最常见的是`class`属性，在这里我们为 HTML 元素定义类，这样它们就可以使用 CSS 去风格化。当使用 JSX 元素时，你应该使用`className`来代替。另一个很常见但在 JSX 不同的属性是`for`，在处理表单时广泛用于标签。在 JSX，你必须用`htmlFor`来代替。关于 JSX 元素中所有属性差异的详细视图，可以查看此链接:[https://react js . org/docs/DOM-elements . html #属性差异](https://reactjs.org/docs/dom-elements.html#differences-in-attributes)

这是理解 JSX 和 React 的良好开端。在下一篇文章中，我们将讨论组件以及如何更好地组织你的应用程序。

欢迎随时评论，提出建议。