# 使用自定义事件对视图切换做出反应

> 原文：<https://dev.to/stevebrownlee/react-view-switching-with-custom-events-270j>

*最初发表于[stevebrownlee.com](https://stevebrownlee.com/react-view-switching-custom-events/)。*

在我与纳什维尔社区的人讨论时，我发现大多数团队都使用某种形式的路由包来监听 URL 的变化以触发视图呈现。因此，我们向学生展示如何使用 [react-router-dom](https://www.npmjs.com/package/react-router-dom) 包进行视图切换。

有些不使用 URL 路由。不是每个页面应用程序都需要它。

虽然我一直在玩 React，慢慢地为纳什维尔软件学校的学生建立适当的教学技术，但我已经尝试了几种不同的视图切换机制。昨晚，当我在另一个 JavaScript 项目中使用`CustomEvent()`构造函数时，一个💡走火了。

与其在 **Matriarch** 组件中定义一个函数(这是我在每个维护应用程序状态的应用程序中对组件的称呼),它的引用被传递，为什么不只是监听一个事件呢？然后，任何需要基于用户手势或其他事件切换视图的组件都可以简单地分派一个带有数据有效负载的事件。有效负载是可选的，包含下一个视图完成工作可能需要的任何数据。

## 视图管理器

我在我的应用程序中创建了一个简单的模块，它的[职责](https://code.tutsplus.com/tutorials/solid-part-1-the-single-responsibility-principle--net-36074)是双重的——在 DOM 元素上设置一个事件监听器，并提供调度自定义事件的能力。

> src/modules/ViewManager.js

```
const ViewManager = Object.create(null, {
    init: {
        value: function (selector, eventName, fn) {
            this.eventName = eventName
            this.element = document.querySelector(selector)
            this.element.addEventListener(this.eventName, fn)
        }
    },
    broadcast: {
        value: function (view, payload) {
            this.element.dispatchEvent(
                new CustomEvent(this.eventName, {
                    detail: {
                        view: view,
                        payload: payload
                    }
                })
            )
        }
    }
})

export default ViewManager 
```

Enter fullscreen mode Exit fullscreen mode

## 主要应用

在 React 中，使用`create-react-app`时主组件默认为`App.js`。你的主要成分可能是别的。

在构造函数中，视图管理器由三部分初始化:

1.  将附加事件侦听器的 DOM 元素。
2.  将要广播的自定义事件的名称。
3.  处理事件的本地函数引用。

> src/App.js

```
import React, { Component } from "react"
import ViewManager from "./modules/ViewManager"

class App extends Component {
    constructor(props) {
        super(props)

        // Initialize ViewManager for switching main view
        ViewManager.init("#root", "changeView", this.switch)
    }

    switch = event => {
        const _viewProps = Object.assign({
            notifications: notes,
            exampleAdditionalInfo: localStorage.getItem("preferences")
        }, event.detail.payload)

        // Update state to trigger the view change
        this.setState({
            currentView: event.detail.view,
            viewProps: _viewProps
        })
    }

    ...

    // Returns a component to be rendered in the JSX below
    ShowView = () => {
        switch (this.state.currentView) {
            case "profile":
                return <Profile {...this.state.viewProps} />
            case "results":
                return <SearchResults {...this.state.viewProps} />
            case "home":
            default:
                return <Home {...this.state.viewProps} />
        }
    }

    render() {
        return (
            <React.Fragment>
                <NavBar {...this.state.viewProps} />

                {this.ShowView()}
            </React.Fragment>
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 任何组件

任何组件现在都可以导入 **`ViewManager`** ，并使用`broadcast()`方法来触发视图更改。在这个精简的组件中，当用户点击一个*视图概要文件*超链接时，一个定制事件被调度，并且 **`App`** 组件的监听器触发并切换视图。

> src/search/SearchResults.js

```
import React, { Component } from "react"
import ViewManager from "../modules/ViewManager"

export default (props) => (
    <div className="searchResults">
        props.foundItems.users.map(user =>
            <a href="#" 
                className="btn btn-outline-success"
                onClick={() => {
                    // Switch to profile view with a data payload
                    ViewManager.broadcast("profile", {userId: user.id})
                }}
                >View profile</a>
        )
    </div>
) 
```

Enter fullscreen mode Exit fullscreen mode