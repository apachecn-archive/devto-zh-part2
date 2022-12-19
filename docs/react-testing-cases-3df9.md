# 反应测试案例

> 原文：<https://dev.to/askeroff/react-testing-cases-3df9>

**更新**:我写这篇文章的时候，我用的是 3.1.3 版本的库，我想，现在我已经更新了，有些东西已经改变了。我已经修改了这篇文章，如果我遗漏了什么，请在评论中告诉我。
如果你使用的是 5 以上的实际版本，那么下面是对这些代码片段的一些修改:

1.  没有 **renderIntoDocument** 。使用**渲染**的方法
2.  不需要首先更改值，然后使用 fireEvent。像这样使用它:

```
 fireEvent(component.getByLabelText("Some label"), {target: { value: "20"}}); 
```

Enter fullscreen mode Exit fullscreen mode

1.  你不用勾选复选框。在 fireEvent 上更改，使用. click。

我想我都有了。

* * *

**免责声明**:我正在描述一些用例，我会认为自己是有用的。我希望这篇文章能帮助到一些人，如果没有，也许它能在我忘记一些事情的时候帮助到我自己。

我用来测试 react 的是:Jest 和 react-testing-library 进行单元测试，cypress 进行集成(或者端到端，我不确定应该怎么称呼这些)。不过，我不会在这里谈论赛普拉斯。

## 未定义 localStorage

我偶然发现的第一个问题是，当您运行测试时，没有定义 localstorage。那是我开始了解模仿的时候。基本上，如果你真的不需要测试，你可以替换(模仿)一些代码，至少在这个测试用例中不需要。
在这种情况下，像 localstorage 这样的浏览器 API 没有在您的测试环境中定义，您可以通过以下方式模仿它:

```
 //browserMocks.js
    var localStorageMock = (function() {
        var store = {};

        return {
            getItem: function(key) {
                return store[key] || null;
            },
            setItem: function(key, value) {
                store[key] = value.toString();
            },
            clear: function() {
                store = {};
            }
        };

    })();
    Object.defineProperty(window, 'localStorage', {
        value: localStorageMock
    }); 
```

Enter fullscreen mode Exit fullscreen mode

之后，您需要告诉 jest 在您的测试环境中使用这个文件。为此，打开 package.json 文件，根据下面的代码片段编辑它:

```
"jest": {
"setupFiles": [
      "./some_path/browserMocks.js",
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用戏言嘲弄

就像上面的例子一样，有时你必须模仿你的一些代码。它要么会降低测试速度，因为它会调用 API，要么太难设置，会出错，等等。您可以隔离您的测试，并模仿所有不重要的东西。可以这样做:

```
 jest.mock("../path/module", () => {
        return jest.fn();
    }); 
```

Enter fullscreen mode Exit fullscreen mode

## 多嘲讽

有一次，我只需要模拟一个类中的一个方法。我不想用上面提到的模拟来代替整个班级。我本来可以为它编写一个模拟类，并在那里定义方法，然后告诉 jest 使用它，但这似乎太麻烦了。

我所做的如下:

```
ClassName.prototype.method= jest.fn() 
```

Enter fullscreen mode Exit fullscreen mode

感谢 stackoverflow 昵称为 **WickyNilliams** 的用户。你可以查看他更详细的回答[这里](https://stackoverflow.com/questions/49667231/jest-how-to-mock-a-method-within-a-function)

## 测试某事是否在 DOM 中

React-testing-library 为你提供了两种访问渲染元素的方法，一种是从 **queryBy** 开始，另一种是从 **getBy** 开始。有一些这样的方法，比如 **getByTestId** 或者 **queryByTestId** 。

你可以在库的 readme 中读到更多关于它的内容，你特别需要哪些方法。但是用以 queryBy 开头的方法获取元素的区别在于，它可以是 null，getBy 应该总是返回一个元素，否则就会抛出一个错误。

因此，如果您想测试 DOM 中没有的东西，您可以执行以下操作:

```
 const component = renderIntoDocument(<Component />);
  expect(component.queryByTestId("testid-of-element")).toBeNull(); 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您想测试 DOM 中的某个东西**是**该怎么办呢？为此你需要安装 **jest-dom/extend-expect** ，然后你可以这样做:

```
 const component = renderIntoDocument(<Component />);
  expect(component.queryByTestId("row-34")).toBeInTheDOM(); 
```

Enter fullscreen mode Exit fullscreen mode

## 测试某个元素有某个类

```
 const component = renderIntoDocument(<Component />);
    expect(component.getByTestId("testid-element")
            .classList.contains("class-name")).toBe(true); 
```

Enter fullscreen mode Exit fullscreen mode

当然你可以通过 false 来测试它没有类。

## 你没有 testids

假设，您在呈现的组件中没有这些元素的 testids，并且您想要测试一些东西。

```
 const component = renderIntoDocument(<Component />);
    const elements = component.container.getElementsByClassName("class-name"); 
```

Enter fullscreen mode Exit fullscreen mode

还有其他访问元素的方法，由 react-testing-library 公开。像 getByLabelText 和其他几个，你可以在这里看到。但是有时它们都不适用，所以我使用我上面提到的类。但是，这可能不是一个好主意，或者至少不是最佳实践。因为有人可以重命名或删除该类，您的测试将会失败。我只是想，我应该提到还有其他方法。也可以使用 getElementsByTagName。

## 事件

从 react-testing-library 中，您可以导入 fireEvent 并触发一些事件供 react 处理。这真的很有用，因为很多时候我需要在事情发生后测试组件的状态。触发点击很容易，但是其他的就有点棘手了。嗯，至少对我来说，我花了一些时间试图找出在某些情况下如何触发变化。

```
 const component = renderIntoDocument(<Component />);
    fireEvent.click(component.getByTestId('testid-element')); 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，如果你想触发 onchange 处理程序，你必须首先在你的测试代码中进行修改，然后触发 onChange。比如，假设您有一个输入，并且想要测试它的 onChange 处理程序:

```
 const component = renderIntoDocument(<Component />); 
    component.getByTestId("input").value = "20";
    fireEvent.change(component.getByTestId("input"), {}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想测试一个复选框，我偶然发现了一个东西。你可以这样触发 onchange:

```
 const component = renderIntoDocument(<Component />);
        component.getByLabelText("Label Text").setAttribute("checked", "");
        fireEvent.change(component.getByLabelText("Label Text")); 
```

Enter fullscreen mode Exit fullscreen mode

但是，这种方法对我不起作用，因为我的输入有 **defaultChecked** 属性。
在那种情况下对我有用的是:

```
 const component = renderIntoDocument(<Component />);
        component.getByLabelText("Label Text").checked = true;
        fireEvent.change(component.getByLabelText("Label Text")); 
```

Enter fullscreen mode Exit fullscreen mode

我不知道为什么会这样，为了安全起见，我可能每次都会选择最后一个。

## 计时器

如果你的代码在几秒钟后发生了什么，例如，你正在使用 setTimeout 方法，你可以使用假的定时器，然后直接看到结果。为此，使用 jest 的方法:

```
 jest.useFakeTimers(); 
```

Enter fullscreen mode Exit fullscreen mode

在您执行了这个方法之后，您可以在您的测试中使用它:

```
 jest.runAllTimers(); 
```

Enter fullscreen mode Exit fullscreen mode

## 卸载测试中的组件

我在例子中使用了 renderIntoDocument，如果你打算使用这个方法，不要忘记用 **unmount** 方法来清理。

```
 const component = renderIntoDocument(<Component />);
        /// testing
        component.unmount(); 
```

Enter fullscreen mode Exit fullscreen mode