# 反应上下文

> 原文：<https://dev.to/independnt/react-context-1a58>

自从从熨斗学校毕业后，Redux 一直是反应花生酱的果冻。意大利面和肉丸。冷石·史蒂夫·奥斯汀和啤酒(虽然我听说他最近戒酒了。跑题了)。我可以继续。然而，我最近偶然发现了 React Context，这是一个在组件中处理状态方面相当新的范例。也许我并不总是需要 Redux，因为有了 Context，我可以将状态传递给组件，而不必使用 props。我听到这话时的想法？哇哦。根据文档，应该谨慎使用它，因为它会使组件重用变得困难。让我们使用上下文来重构我在以前的博客文章中一直在做的加密仪表板。

首先，我们需要创建一个上下文，在我当前项目的范围内，我简单地创建了一个名为“AppProvider.js”的新文件。这不是一个除了它的子组件之外还能渲染任何东西的组件。它更像是项目状态的容器。首先，我们将导入 React 并使用 React createContext 函数创建一个新的上下文:

```
 import React from 'react';
    export const AppContext = React.createContext() 
```

现在我们有了一个上下文对象。理解上下文如何工作的关键方面是理解提供者和消费者的关系。请记住，我还在学习，这是我所能理解的。提供者是接受值属性(在本例中是状态)的 React 组件。一旦提供者的值改变，该提供者的所有消费者将重新呈现。消费者也是 React 组件，根据文档，它订阅上下文更改。

话虽如此，让我们继续前进。我正在重构在我当前的项目中如何处理状态，在这种情况下，应用程序如何知道要呈现哪个页面。因此，在我新创建的 AppProvider.js 中，我将创建状态，就像我在其他组件中一样:

```
 export class AppProvider extends React.Component {
      constructor(props){
        super(props);
        this.state = {
          page: 'dashboard',
          setPage: this.setPage
    }
  }
} 
```

现在你会注意到，我在状态中也有一个叫做 setPage 的东西。这将是我的更新功能。我知道将函数传递给状态很奇怪，但是根据上下文文档，它表明状态应该包含更新函数。现在让我们来定义这个函数:

```
 setPage = page => this.setState({page}) 
```

最后，如前所述，让我们创建我们的提供者。

```
 render(){
    return (
      <AppContext.Provider value={this.state}>
        {this.props.children}
      </AppContext.Provider>
    )
  } 
```

现在，所有的孩子都可以访问国家，剩下的就是选择我们的消费者，并在必要的时候引入他们。首先，我需要将需要访问状态的组件包装在新的 AppProvider 对象中。我将进入所需的文件并导入/包装:

```
import {AppProvider} from './AppProvider'

class App extends Component {
  render() {
    return (
      <AppLayout>
        <AppProvider>
          <AppBar/>
          <WelcomeMessage/>
        </AppProvider>
      </AppLayout>
    );
  }
} 
```

现在 AppBar 和 WelcomeMessage 可以访问所需的信息。进入 AppBar，我们现在可以通过创建一个 ControlButton 函数并返回一个消费者组件来创建消费者:

```
function ControlButton({name}){
  return (
    <AppContext.Consumer>
      {({page, setPage}) => (
        <div
          onClick={() => setPage(name)}
        >
          {name}
        </div>
      )}
    </AppContext.Consumer>
  )
} 
```

我的函数接受一个名称(在每个单独的 ControlButton 的 name prop 中标记)并返回 AppContent。Consumer，创建一个新的 Consumer 组件，该组件然后获取处于提供者状态的当前页面，以及 setPage 函数并返回一个具有 onClick 处理程序的 div，该处理程序现在使用 setPage 函数来更改处于提供者状态的页面。

唷。好吧，现在这只是基础，以后会有更多的上下文实验。结束。