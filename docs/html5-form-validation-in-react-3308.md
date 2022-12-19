# React 中的 HTML5 表单验证

> 原文：<https://dev.to/_arpy/html5-form-validation-in-react-3308>

[![](../Images/395ac9222c01454ed7113d83e490c2e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bcnek76T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AHsjdj3i8TIc4_zrU.)

<figcaption>[赛·基兰·阿纳加尼](https://unsplash.com/@_imkiran?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

上的《电脑屏幕上的彩色代码行》

### 最佳数据是经过验证的数据

用户…数据收集…表格…我敢肯定你知道用户输入是好的，当它是有效的✅.这就是为什么网站必须鼓励用户尽可能填写最合适的数据。

有各种各样的库，如 **jQuery Validation** 或 **Validate.js、**帮助表单输入验证。它们都实现了在提交之前对每个表单域执行预定义检查的思想。然后，如果有不符合条件的字段，它们会显示错误消息。

但是还有强大的 HTML5 验证 API。太棒了。现代浏览器几乎完全支持 API。当然，它们都实现了自己的验证和显示错误消息的方式。有时候看起来真的很恶心🙈

那么，为什么不为验证错误实现我们自己的布局呢？听起来可行。我们将使用 HTML5 约束验证 API 的以下方面:表单的 ***checkValidity*** 方法和 ***:valid/:invalid*** 状态，以及表单元素的 ***validity*** 属性。如果你好奇的话，在这个极好的 MDN Web Docs (Mozilla 开发者网络)页面中有详细的解释。我要做的是使用 API 为 **React** 应用程序实现自定义验证消息。我们走吧！🏁 🚗

### 反应组分

嗯，React 就是组件的意思！我们确实需要为此任务创建一个。令人惊讶的是，它将是一个定制的有状态的表单组件，带有相应的样式文件。

首先，让我们定义如何显示我们的验证错误。我更喜欢在每个表单字段旁边有单独的消息。为了方便起见，我们假设每个输入字段都被赋予了 ***。form-control*** 类，每个类都有一个带 ***的兄弟< *span >* 。无效反馈*** 类。每个 span 将保存其相关表单元素的错误消息。在这个实现中，每个表单元素旁边都有自己的错误消息容器。当然，您可以自由定义自己的错误容器，甚至可以只使用一个容器在一个地方显示所有的消息。

您可能已经知道，表单元素的有效性是由 CSS 伪类标识的。如果元素( *input、textarea、checkbox、…* )通过了定义的规则，则赋予其 ***:有效的*** 伪类。否则得到 ***:无效的*** 伪类。我们将使用这个行为来决定一个错误信息是否应该显示在一个元素的旁边。我们将在我们的 **Form.css** 中定义一个样式，它将隐藏有效元素的消息。

```
.form-control:valid~.invalid-feedback {display: none;} 
```

组件的思想如下。在 React 中，我们通常不希望在表单提交时重新加载页面。相反，我们希望用 ajax 请求发送数据。对于我们的验证组件来说，如何提交数据以及提交什么数据并不重要。我们只是处理验证。这就是为什么它将接收一个名为**提交**的属性，这是一个函数，只要允许提交表单，就应该调用这个函数。该组件将以下列方式覆盖本机表单提交事件。首先，它将使用 ***checkValidity*** 方法检查整个表单的有效性。如果没有发现错误，它将通过从 **props 调用 *submit* 方法来执行提交。**如果至少有一个无效元素，它将显示相应的消息并取消表单提交。当然，该组件将呈现一个常规的 *<表单>* 标记，所有子元素都嵌套在其中。

听起来很简单，对吧？让我们看看它作为一个代码是什么样子的😉

```
import React, {Component} from 'react';
import PropTypes from 'prop-types';
import './Form.css';

class Form extends Component {
    state = {
        isValidated: false
    }

    validate = () => {
        const formLength = this.formEl.length;

        if (this.formEl.checkValidity() === false) {
            for(let i=0; i<formLength; i++) {
                const elem = this.formEl[i];
                const errorLabel = elem.parentNode.querySelector('.invalid-feedback');

                if (errorLabel && elem.nodeName.toLowerCase() !== 'button') {
                    if (!elem.validity.valid) {
                        errorLabel.textContent = elem.validationMessage;
                    } else {
                        errorLabel.textContent = '';
                    }
                }
            }

            return false;
        } else {
            for(let i=0; i<formLength; i++) {
                const elem = this.formEl[i];
                const errorLabel = elem.parentNode.querySelector('.invalid-feedback');
                if (errorLabel && elem.nodeName.toLowerCase() !== 'button') {
                    errorLabel.textContent = '';
                }
            };

            return true;
        }
    }

    submitHandler = (event) => {
        event.preventDefault();

        if (this.validate()) {
            this.props.submit();
        }

        this.setState({isValidated: true});
    }

    render() {
        const props = [...this.props];

        let classNames = [];
        if (props.className) {
            classNames = [...props.className];
            delete props.className;
        }

        if (this.state.isValidated) {
            classNames.push('.was-validated');
        }

        return (
            <form ref={form => this.formEl = form} onSubmit={this.submitHandler} {...props} className={classNames} noValidate>
                {this.props.children}
            </form>
        );
    }
}

Form.propTypes = {
    children: PropTypes.node,
    className: PropTypes.string,
    submit: PropTypes.func.isRequired
};

export default Form; 
```

让我们从最底层的⬆️.开始深入研究因此，我们呈现一个常规的 *<表单>* ，其中包含了传递给我们组件的所有子组件。这还得了一个**。在我们没有错误的情况下验证了**类。例如，我们可以使用这个类进行样式设计。我们还在组件中保存了对表单元素的引用。因此，我们可以用 JavaScript 方法来处理它。同样，我们为带有 ***onSubmit*** 事件的表单分配了一个*提交处理程序*函数。

当表单被提交时(通常有一个提交按钮)，我们调用名为 ***validate()的组件方法。*** 不难猜测，这就是我们组件的主要功能被隐藏的方法。所以，如果它返回 ***true*** ，表单有效，我们可以自由调用组件的 ***submit*** 方法。现在，验证是如何工作的🤔？

#### 验证方法

在 HTML5 中，使用***check validation()***方法检查表单的有效性。如果所有表单元素都符合定义的规则，则返回 ***true*** ，如果至少有一个验证规则失败，则返回 ***false*** 。我们将在组件中使用这种行为。

如果表单是**有效的**，我们将遍历它的元素并删除它们对应的错误容器的文本。

如果表单无效，我们需要显示每个出错元素的消息。如果表单元素无效，其 ***validity.valid*** 属性将为 false。在这种情况下，我们将填写**。无效-反馈** *<跨度>* 以及相应的错误信息。API 提供的错误消息可通过元素的***validation message***属性访问。我们将使用该消息，因为它非常详细，并且已经根据浏览器语言环境进行了本地化。如果你想使用你的自定义错误消息，你应该给 *errorLabel.textContent* 赋值，而不是*elem . validation message .*这么简单。

注意，如果一个元素是一个按钮，我们就跳过它。这是因为表单引用了用户可以与之交互的所有元素，包括按钮。

现在，我们所有的无效字段旁边都有错误消息，我们的表单直到所有错误都被修复后才被提交👍剩下唯一要做的就是造型了！但是我想我会把有趣的部分留给你，比如*“我想相信”*(👽)在你的创造力中😜

感谢你读到这里，希望你在这里玩得开心并学到一些新东西。

这是一个完整的工作代码笔，我把它创建成了一个游戏场🎮享受吧，程序员们！

[https://codepen.io/_arpy/embed/xYoyPW/?height=505&theme-id=dark&default-tab=js,result&embed-version=2%20?height=500&default-tab=result&embed-version=2](https://codepen.io/_arpy/embed/xYoyPW/?height=505&theme-id=dark&default-tab=js,result&embed-version=2%20?height=500&default-tab=result&embed-version=2)

还有一件事…

### 从两边保护自己

记住，在客户端验证用户输入是不够的。像你这样聪明的人总能找到办法避开验证。这就是为什么**总是**也在你的**后端做检查。相信我，你会为那个☺️而感谢自己的**

[![](../Images/07a3552be5e48400b51b5518aa0b33b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VlsT44Mm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.giphy.com/media/u4KibgMsDLWM0/giphy.gif)