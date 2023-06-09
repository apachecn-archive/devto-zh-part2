# Redux 的 ABC

> 原文：<https://dev.to/radiumsharma06/abc-of-redux-5461>

大家好！

最近，React 和 Redux 已经成为热门话题，我已经玩了一段时间，学到了一些细微差别。我写这篇文章是为了让任何刚开始使用 Redux 的人都能从初学者的角度看到它。

## Redux

Redux 不依赖 react，反之亦然。两人配合默契，各司其职。

**React 提供了*视图*** - **Redux 管理其*状态*逻辑**。

## 冗余术语

*   商店
*   状态
*   派遣
*   行动
*   动作创建者
*   还原剂

## 还原生命周期

[![Redux Lifecycle](img/1c426496b14a7ce41783977ab1a30cdf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fCDvEpjd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/LNQwH.png)

上图很好的概述了 redux cycle 的工作原理。

#### 核心冗余原则

*   它有一个全局状态提供者，称为 **store** ，包含应用程序的整个状态逻辑。这是一个巨大的优势，因为状态只有一个真实的来源，而且一旦进入 redux store，就可以在整个应用程序(即所有组件)中全局访问。例如，我们调用一次 api，将数据存储在 redux 存储中，然后就可以访问任何组件中的数据。我更喜欢使用 **redux saga** 来通过 redux 进行 api 调用，但是让我们在以后的文章中讨论这个问题。 **State** 数据可以是任何东西，从检查单选按钮到来自一些 API 的大量数据。*   Next question is we have a state but how do we update or add to it? Lets see how its done.

    一个状态应该只通过分派一个**动作**通过**动作创建者**(记得我之前提到的关键字)
    来更新，让我们看看它们是什么:-
    动作是一个简单的 javascript 对象，我们分派它或者你可以说 launch 来改变一个状态。有个例子会更好。假设我们有一个写着“hello”的标签，我们需要把它改成“bye ”,那么我们如何通过 redux 来实现呢？我们的行动最初会是这样的

    ```
    {
        type: 'LABEL_VALUE',
        value: 'hello' 
    } 
    ```

    我们的 react 组件将类似于

    ```
    const LabelComponent = () => (
     <Label> { state.labelValue } </label>;
     <Button onClick={changeLabel}> Click Me </Button> ); 
    ```

    现在我们需要在点击一个按钮时更新它的值，那么我们该怎么做呢？我们发送一个点击按钮的动作。
    调度一个动作将是这样:-

    ```
    const changeLabelOnClick = (newLabelValue) => {
      dispatch( changeLabelValue('bye') );
    }

    const LabelComponent = () => (
     <Label> { state.labelValue } </label>;
     <Button onClick={changeLabelOnClick('bye')}> Click Me </Button> ); 
    ```

    注意这里 changeLabelValue 只是一个动作创建器，它返回一个新的对象或动作。以下是 changeLabelValue 的外观:-

    ```
    const changeLabelValue = (labelValue) => {
        type: 'LABEL_VALUE',
        labelValue
    } 
    ```

    如果你注意到 action creator 只是一个动作的包装器，这是一个很好的实践。你可以直接发送一个类似于
    的动作

    ```
     dispatch( {
           type: 'LABEL_VALUE',
           labelValue: 'Bye'
        } ); 
    ```

    下一个问题是，一旦你分派了一个动作，实际上会发生什么，状态会如何变化？

    让我们看看:-
    正如你们可能已经注意到的，在分派一个动作对象时，我们给它添加了一个“type”键(type : 'LABEL_VALUE ')。这是一个字符串，它是存储的哪个部分将发生变化以及如何变化的决定因素。
    **减速器**是订阅这些类型并相应地改变状态的减速器。状态的实际变化发生在减速器中。让我们看看如何:-

    缩减器文件片段:-

    ```
    const labelReducer = (state = {}, action) => {
        switch(action.type){
          case 'LABEL_VALUE':{
            return action.labelValue;
          }
          default:
            return state;  

        }
    } 
    ```

    这是 reducer 中的一个函数，在被调用时返回一个新的状态。
    当使用此缩减器中存在的动作类型调度动作时，触发此功能。在这种情况下，动作类型为**‘标签值’**。
    如果您注意到它返回一个新对象，而不改变现有对象。这就是所谓的不变性，每当有变化时，我们就破坏并创造一个新的状态。

    现在，如果你一直关注到现在，你会注意到我们可以用任何我们想要的字符串来改变标签值。我们只需要分派一个具有适当值的动作。对于例`dispatch( changeLabelValue('yipeee') )`而言，我们完成了，该值将被更新。

    这里需要注意的重要一点是标签是如何改变的，我们在上面已经看到:-

    ```
     <Label> {state.labelValue} </Label> 
    ```

    这在分派动作时如何变化？这就是 react-redux 的妙处。此标签位于组件标签组件中。现在这个组件使用**‘label value’**存储的状态。每当 redux 中的 labelValue 发生变化时，该组件将在订阅 redux 时重新呈现。所以当这个组件被重新呈现时，state.labelValue 已经被更新了。

    *   Now one important thing to note here is we have to bind view to state. All the above code snippets were shown in one file for understanding purpose but we need to separate view logic from container logic. We saw we had a LabelComponent before so it is a pure component as it just renders a view but it needs to have a separate container component which provides data or state data to this component from redux as props.

    让我们看看这两个文件的完整代码:-

    纯组件文件

    ```
    //LabelComponent.js - Pure Component

    const LabelComponent = ({labelValue, changeLabelOnClick}) => (
     <Label> { labelValue } </label>;
     <Button onClick={changeLabelOnClick('bye')}> Click Me </Button> ); 
    ```

    容器文件

    ```
    //LabelComponent.container.js

    import { connect } from 'react-redux';
    import { changeLabelValue} from '../../actions';
    import LabelComponent from './LabelComponent';

    const mapStateToProps = (state) => {
      return {
        labelValue: state.labelValue
      }
    }

    const mapDispatchToProps = (dispatch) => {
      return {
        changeLabelOnClick: (labelValue) => dispatch(changeLabelValue(labelValue))
      };
    };

    export default connect(mapStateToProps, mapDispatchToProps)(LabelComponent); 
    ```

    让我们理解这两个文件，以及数据是如何从这里流向相应的动作和 reducers 的。

    让我们先解码**label component . container . js**:-
    这里我们首先导入我们需要的动作创建器来改变状态。
    之后你会注意到两个函数 **mapStateToProps** 和**mapdispatctoprops**这两个函数做的和它们的名字所暗示的非常相似。
    **mapStateToProps** 从 redux 获取数据，并将其提供给我们的视图组件，即 **LabelComponent** 在本例中，您可以看到我们在 LabelComponent 中使用 labelValue 作为属性。

    与此类似的是 **mapDispatchToProps** 提供函数作为查看组件的道具，该组件可以将数据作为**回调**提供给容器。在本例中， **changeLabelOnClick** 是一个回调函数，作为 LabelComponent 的一个道具提供。当容器中的数据可用后，我们分派一个动作，数据流向 reducer - >存储，并返回到带有更新状态的视图。现在让我们看看 **LabelComponent.js**

    它的大部分在以前的文件解释中有所涉及。这里我们首先从容器中获取道具(我们在道具中使用析构)。那么我想流动是非常清楚的。单击按钮时，labelValue 流回容器，新的现有 labelValue 作为 prop 出现在组件中。

    现在这两个文件终于通过一个非常方便的组件相互连接起来——
    **连接来自‘react-redux’的**。我们在容器中导入 LabelComponent，并通过使用 connect 模块以及 **mapstateToProps** 和**mapdispatctoprops**向其提供状态数据作为道具，并将其作为单个组件导出，如您在文件中所见。

    ```
    export default connect(mapStateToProps, mapDispatchToProps)(LabelComponent); 
    ```

    最后一件事，我没有显示整个商店如何对应用程序可用，以及应用程序如何订阅 redux 更改。我附上一小段概述:-

    ```
    import reducers from './reducers';
    import App from './components/App';    
    import React from 'react';
    import { render } from 'react-dom';
    import { Provider } from 'react-redux';
    import { createStore } from 'redux'

    const store = createStore(reducers);

    render(
      <Provider store={store}>
        <Router>
          <App />
        </Router>
      </Provider>,
      document.getElementById('root')
    ); 
    ```

    请将此视为 react 应用程序的起点，该应用程序呈现为 Dom 并包含所有 react 子组件。这里我们使用了一些非常有用的 node_modules 来帮助我们在 react 和 redux 之间建立联系。你可以在 redux 官方文档中查看他们的详细解释。这里 **createstore** 将整个状态绑定到一个单独的存储中，并将其赋给一个变量。如果你注意到 reducers 只不过是一个包含不同 reducer 文件的文件夹，这些文件包含状态的一部分。

    **Provider** 是我们为整个商店提供的另一个组件，它将商店传播给整个 react 应用程序组件及其子组件，以便可以访问商店。

    现在，如果这一切仍然有点模糊，没关系。我附加了一个到我的 github repo 的链接，

    [反应-还原-应用](https://github.com/radiumsharma06/React-Redux-App)

    希望对你有帮助。如果您有任何问题，请随时发表评论。

    恭喜你，祝你度过愉快而富有成效的一天:)！！