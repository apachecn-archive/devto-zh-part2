# 隐式状态共享:React 的上下文 API &在 Vue 中提供/注入

> 原文：<https://dev.to/bnevilleoneill/implicit-state-sharing-react-s-context-api-provide-inject-in-vue-30m6>

[![](img/09b63524a08394dc4c8926daa3e80cf0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0KrtjxKI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0WqXbHxXGkzhmiv0wkYWFA.jpeg)

假设您正在创建一个 accordion 组件，并希望通过 npm 包公开发布。您希望这个 accordion 的用户能够通过将多个组件组合在一起，以非常灵活的方式使用组件。

想象这是你理想的 API:

```
<Accordion>
    <AccordionItem>
        <AccordionHeader>Header content</AccordionHeader>
        <AccordionPanel>Panel content</AccordionPanel>
    </AccordionItem>
</Accordion> 
```

AccordionItem 将包含 accordion 的每个可以展开或折叠的部分，AccordionHeader 将是用户可以单击以展开或折叠的地方，AccordionPanel 将包含要显示或隐藏的内容。

每个 AccordionItem 都需要维护某种状态——不管它是否是*展开的*。但是 AccordionHeader 也需要访问这个值，这样它才能显示适当的切换按钮。而 AccordionPanel 可能也需要访问这个，因为它是被展开和折叠的东西。

一种可能是通过 render props 向用户公开扩展的值，并确保您的文档让他们知道他们需要将该值传递给 header 和 panel 组件。

```
<Accordion>
    <AccordionItem render={({expanded}) => (
        <AccordionHeader expanded={expanded}>
            Header content
        </AccordionHeader>
        <AccordionPanel expanded={expanded}>
            Panel content
        </AccordionPanel>
    )} 
    />
</Accordion> 
```

虽然这看起来是一个不错的解决方案，但是让组件的消费者担心组件的内部结构并不理想。AccordionHeader 和 AccordionPanel 需要访问扩展状态的事实不应该是我们的用户必须关心的事情。

还应该注意，虽然这是一个简单的例子，但是您的组件可能要复杂得多，具有多层嵌套组件，在这种情况下，正确的钻探可能会变得非常繁琐。

我们真正需要的是一种方式来*含蓄地*传递道具。

### 使用 React 的上下文 API

对于这种情况，有一个更好的解决方案——React 的上下文 API。我们可以使用上下文 API 来创建一些状态，并在幕后需要的地方提供它，从我们面向公众的 API 中消除这种顾虑。

首先，我们将创建一个上下文，并定义该上下文的*形状*。我们将从一个扩展值和一个 toggleExpansion 方法开始。我们将这个上下文定义为与我们的手风琴项目特别相关:

```
const AccordionItemContext = React.createContext({
    expanded: false,
    toggleExpansion: () => {}
}); 
```

现在，在我们的 AccordionItem 组件中，我们将定义 expanded 和 toggleExpansion 值，并将它们作为 Provider 组件的值输入。

```
class AccordionItem extends React.Component {
    constructor (props) {
        super(props)

        this.toggleExpansion = () => {
            this.setState({ expanded: !this.state.expanded })
        }

        this.state = {
            expanded: false,
            toggleExpansion: this.toggleExpansion
        }
    }

    render () {
        return (
            <AccordionItemContext.Provider value={this.state}>
                <div className="accordion-item">
                    {this.props.children}
                </div>
            </AccordionItemContext.Provider>
        )
    }
} 
```

提供者是上下文等式的一半。另一半是消费者。提供者允许消费者订阅上下文变化，我们很快就会看到。

接下来，我们需要将 AccordionHeader 和 AccordionPanel 设置为这个上下文的*消费者*:

```
const AccordionHeader = (props) => {
    return (
        <AccordionItemContext.Consumer>
            {({ expanded, toggleExpansion }) => (
                <h2 className="accordion-header">
                    <button onClick={toggleExpansion}>
                        { expanded ? '▼ ' : '► ' } 
                        { props.children }
                    </button>
                </h2>
            )}
        </AccordionItemContext.Consumer>
    )
} 
```

使用者组件需要一个函数作为其子组件。该函数将接收上下文值，我们将其析构为 expanded 和 toggleExpansion。然后，我们的组件能够在其模板中使用这些值。

我们将类似地使用 Consumer 向 AccordionPanel 提供对上下文值的访问:

```
const AccordionPanel = (props) => {
    return (
        <AccordionItemContext.Consumer>
            {({ expanded }) => <div className={"accordion-panel " + (expanded ? 'expanded' : '')}>{props.children}</div>}
        </AccordionItemContext.Consumer>
    )
} 
```

现在，我们真的可以为 accordion 组件实现我们理想的 API 了。我们组件的用户不必担心在组件树中上下传递状态。那些组件内部结构对它们是隐藏的:

```
<Accordion>
    <AccordionItem>
        <AccordionHeader>Header content</AccordionHeader>
        <AccordionPanel>Panel content</AccordionPanel>
    </AccordionItem>
</Accordion> 
```

[https://codepen.io/jonathanharrell/embed/WgBzyX?height=600&default-tab=result&embed-version=2](https://codepen.io/jonathanharrell/embed/WgBzyX?height=600&default-tab=result&embed-version=2)

### 提供/注入 Vue

Vue 提供了一个类似 React 的上下文 API 的工具，叫做 provide/inject。为了使用它，我们将在 accordion-item Vue 组件上使用 provide 方法:

```
Vue.component('accordion-item', {
    data () {
        return {
            sharedState: {
                expanded: false
            }
        }
    },

    provide () {
        return {
            accordionItemState: this.sharedState
        }
    },

    render (createElement) {
        return createElement(
            'div', 
            { class: 'accordion-item' }, 
            this.$slots.default
        )
    }
}) 
```

我们从 provide()返回一个对象，该对象包含我们希望提供给其他组件的状态。请注意，我们将一个对象传递给 accordionItemState，而不是简单地传递扩展值。为了具有反应性，provide 必须传递一个对象。

注意，我们在这里使用一个 render 函数来创建这个组件，但是这对于使用 provide/inject 来说并不是必需的。

现在，我们将把这个状态注入到我们的子组件中。我们将简单地使用 inject 属性，它接受对应于我们在 provide 中定义的对象属性的字符串数组。

```
Vue.component('accordion-header', {
    inject: ['accordionItemState'],

    template: `
        <h2 class="accordion-header">
            <button @click="accordionItemState.expanded = !accordionItemState.expanded">
                {{ accordionItemState.expanded ? '▼' : '►' }} 
                <slot></slot>
            </button>
        </h2>
    `
}) 
```

一旦我们在 inject 中包含了属性名，我们就可以访问模板中的那些值。

```
Vue.component('accordion-panel', {
    inject: ['accordionItemState'],

    template: `
        <div class="accordion-panel" :class="{ expanded: accordionItemState.expanded }">
            <slot></slot>
        </div>
    `
}) 
```

[https://codepen.io/jonathanharrell/embed/PdvRoP?height=600&default-tab=result&embed-version=2](https://codepen.io/jonathanharrell/embed/PdvRoP?height=600&default-tab=result&embed-version=2)

### 慎用

值得注意的是，只有在真正有意义的时候，才应该隐式地传递道具。这样做太多会混淆组件的真实行为，并给其他可能参与您的项目的开发人员带来困惑。

打包并分发到其他应用程序中使用的组件库是一个完美的用例，因为组件的内部属性实际上不需要向最终用户公开。

React 的上下文 API 和 Vue 的提供/注入特性都使得通过隐式状态共享来实现这一点成为可能。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[隐式状态共享:React 的上下文 API &在 Vue 中提供/注入](https://blog.logrocket.com/implicit-state-sharing-reacts-context-api-provide-inject-in-vue-679062a50f05/)最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。