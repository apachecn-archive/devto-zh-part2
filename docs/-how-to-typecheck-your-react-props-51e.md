# 如何检查你的反应道具

> 原文：<https://dev.to/neshaz/-how-to-typecheck-your-react-props-51e>

之前我已经写过如何在 react 中传递道具以及如何使用道具在组件间间接通信。今天，我们将进入道具指南系列的大结局，在这里，我将谈论如何检查您是否通过了正确的道具。

## React props——确保你正在进行类型检查

随着您正在开发的应用程序的增长，您肯定希望添加某种类型的属性类型检查——您不希望在您意识到您只是发送了一个空数组而不是一个对象之前，您将花费数小时或数天来处理这些错误！

你可以更极端一点，使用类似于 [Typescript](https://www.typescriptlang.org) 的东西来检查你的整个应用程序。虽然 TypeScript 非常有用，但是没有必要这样做——您不需要对每个函数和变量进行类型检查。

*仅仅定义属性类型是不够的！确保你也用测试覆盖你的代码！*

React 有内置的[属性类型](https://www.npmjs.com/package/prop-types)，所以你可以很容易地进行类型检查。基本上，这个想法是在`propTypes`中定义组件中使用的每个道具的类型。下面是我将为其定义属性类型的示例组件:

```
class Input extends Component {
    ...
    handleChange = (event) => {
        const newValue = event.target.value;
        this.props.updateParent(newValue);
    }
    ...
    render() {
        return (
            <input
                onChange={this.handleChange}
                placeholder={this.props.placeholder}
                type="text"
                value={this.props.value}
            />
        )
    }
} 
```

属性类型将如下所示:

```
 Input.propTypes = {
        placeholder: PropTypes.string,
        updateParent: PropTypes.func.isRequired,
        value: PropTypes.string.isRequired,
    }; 
```

你可能已经注意到，对于`updateParent`和`value`，我在属性类型定义后使用了`isRequired`。这是为了指定属性必须被传递给一个组件，如果不这样做，某些东西可能会损坏。

*如果[呈现一个作为道具发送的列表](https://kolosek.com/react-jsx-loops/)，一定要确保它是必需的或者设置了默认值！*

另一方面，不需要`placeholder`。对于像这样的可选属性，您可能希望在没有按下任何东西时定义一个默认值。您可以通过为组件
定义`defaultProps`来实现这一点

```
 Input.defaultProps = {
        placeholder: "Default placeholder",
    }; 
```

除了标准类型，你还可以添加一个类型检查一个道具是一个实例还是某个类:

```
 Parent.propTypes = {
        inputChild: PropTypes.instanceOf(Input),
    }; 
```

您也可以通过使用`PropTypes.any` :
来指定属性可以是任何数据类型

```
 Parent.propTypes = {
       anyValue: PropTypes.any,
   }; 
```

尽量避免任何错误，这就像根本没有定义正确类型一样——如果你传递一个空数组而不是一个真正的假值，那么[条件渲染](https://kolosek.com/react-jsx-conditions/)将不会像你预期的那样工作！

PropTypes 不限制你只使用预定义的类型验证器，你也可以**定义你自己的**。要获得更多关于类型检查的有用信息，请看一下[官方指南](https://reactjs.org/docs/typechecking-with-proptypes.html)，因为它包含了你可能有的所有问题的答案。

对于类型检查时发现的所有问题，您将在控制台中得到通知。

我强烈建议你**配置 eslint** 来要求所有组件的[道具验证](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prop-types.md)。这样你就不会忘记为你的组件定义 propTypes，这样你就不会错过一些在编码过程中可能出现的意外问题。

如果您愿意的话，您还可以配置 eslint 来强制为不需要的属性定义默认值。虽然这对于组件实例的一致性来说很好，但并不是很重要。

我希望你喜欢这篇文章，学到了一些新的有用的东西，或者至少更新了一点你的知识！

这篇文章最初发表在 [Kolosek 博客](https://kolosek.com/react-props-typechecking/)上。