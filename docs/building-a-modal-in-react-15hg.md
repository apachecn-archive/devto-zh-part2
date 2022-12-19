# 在 React 中建立模型。

> 原文：<https://dev.to/achowba/building-a-modal-in-react-15hg>

在本教程中，我们将在 React 中构建一个模型。下面是它的一个演示。

[![](../Images/b79b00403a18b090a817e5f8f35a7d7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Z9ar7ig--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/achowba/image/upload/v1537016409/Articles/react-modal/react-modal-demo.gif)

### 先决条件

这些是遵循本教程所需的先决条件。

*   [反应](https://reactjs.org/)的知识(你至少要对状态和道具有个基本的了解)。

*   在您的 PC 上安装[节点 JS](https://nodejs.org/en/) 。

*   [安装在您电脑上的 create-react-app](https://reactjs.org/docs/create-a-new-react-app.html) 。

### 初始设置

通过在项目的根目录下运行下面的命令来初始化项目，它会安装启动项目所需的库和工具。

```
create-react-app  basic-modal 
```

Enter fullscreen mode Exit fullscreen mode

### 建筑构件

在创建组件或编写任何代码之前，请按照下面的步骤删除不需要的文件，并创建项目所需的文件。

1.  从 ***src*** 文件夹中删除以下文件: ***app.css，app.test.js，logo.svg，registerserviceworker . js***。

2.  在 ***src*** 目录下创建一个文件夹，命名为 ***组件*** 。

3.  在 ***组件*** 文件夹中，创建另一个名为 ***模态*** 的文件夹。

4.  在 ***Modal*** 文件夹中，创建两个文件分别为 ***Modal.js*** 和 ***Modal.css*** 。

完成此操作后，您的项目结构应该与此类似:

[![](../Images/9be37dba73debf70c39d09d51bfcb660.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tlLmSDB4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/achowba/image/upload/v1537017253/Articles/react-modal/folder-structure.png)

##### Modal.js

打开 components 文件夹中的 Modal.js 文件，并添加以下代码。

```
import React from 'react';

import './Modal.css';

const modal = (props) => {
    return (
        <div>
            <div className="modal-wrapper"
                style={{
                    transform: props.show ? 'translateY(0vh)' : 'translateY(-100vh)',
                    opacity: props.show ? '1' : '0'
                }}>
                <div className="modal-header">
                    <h3>Modal Header</h3>
                    <span className="close-modal-btn" onClick={props.close}>×</span>
                </div>
                <div className="modal-body">
                    <p>
                        {props.children}
                    </p>
                </div>
                <div className="modal-footer">
                    <button className="btn-cancel" onClick={props.close}>CLOSE</button>
                    <button className="btn-continue">CONTINUE</button>
                </div>
            </div>
        </div>
    )
}

export default modal; 
```

Enter fullscreen mode Exit fullscreen mode

模态组件有三个[道具](https://reactjs.org/docs/components-and-props.html)，分别是:

*   show -一个布尔值，指示模式是否应该显示。

*   关闭——点击取消按钮时关闭模态的功能

*   children——在模态组件的开始和结束标记之间传递，作为模态组件的内容。

模式组件是一个功能组件，它接收模式的内容作为组件的子组件。模式页脚还包含一个按钮，当单击它时，该按钮执行关闭模式的功能。该函数作为道具传递给模态组件。 **modal-wrapper** div 中的内联样式包含一个条件语句，该语句根据 show props 的布尔值设置 modal 的可见性状态值。

##### Modal.css

将以下代码添加到 Modal.css 文件中，以便为 Modal 组件添加样式。

```
.modal-wrapper {
    background: white;
    border: 1px solid #d0cccc;
    box-shadow: 0 5px 8px 0 rgba(0,0,0,0.2), 0 7px 20px 0 rgba(0,0,0,0.17);
    margin: 100px auto 0;
    transition: all .8s;
    width: 60%;
}

.modal-header {
    background: #263238;
    height: 40px;
    line-height: 40px;
    padding: 5px 20px;
    text-align: right;
}

.modal-header h3 {
    color: white;
    float: left;
    margin: 0;
    padding: 0;
}

.modal-body {
    padding: 10px 15px;
    text-align: center;
}

.modal-footer {
    background: #263238;
    height: 35px;
    padding: 15px;
}

.close-modal-btn {
    color: white;
    cursor: pointer;
    float: right;
    font-size: 30px;
    margin: 0;
}

.close-modal-btn:hover {
    color: black;
}

.btn-cancel, .btn-continue {
    background: coral;
    border: none;
    color: white;
    cursor: pointer;
    font-weight: bold;
    outline: none;
    padding: 10px;
}

.btn-cancel {
    background-color: #b71c1c;
    float: left;
}

.btn-continue {
    background-color: #1b5e20;
    float: right;
}

.back-drop {
    background-color: rgba(48, 49, 48, 0.42);
    height: 100%;
    position: fixed;
    transition: all 1.3s;
    width: 100%;
}

.open-modal-btn {
    margin: 15px;
    padding: 10px;
    font-weight: bold;
} 
```

Enter fullscreen mode Exit fullscreen mode

##### App.js

```
import React, { Component } from 'react';

import Modal from './components/Modal/Modal';

class App extends Component {

    constructor() {
        super();

        this.state = {
            isShowing: false
        }
    }

    openModalHandler = () => {
        this.setState({
            isShowing: true
        });
    }

    closeModalHandler = () => {
        this.setState({
            isShowing: false
        });
    }

    render () {
        return (
            <div>
                { this.state.isShowing ? <div onClick={this.closeModalHandler} className="back-drop"></div> : null } 
                <button className="open-modal-btn" onClick={this.openModalHandler}>Open Modal</button> 
                <Modal
                    className="modal"
                    show={this.state.isShowing}
                    close={this.closeModalHandler}>
                        Maybe aircrafts fly very high because they don't want to be seen in plane sight?
                </Modal>
            </div>
        );
    }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

App.js 文件包含两个自定义函数，它们是:

*   openModalHandler() -这个函数将 isShowing 的状态设置为 true，从而打开模态。

*   **closeModalHandler()** -这个函数将 isShowing 的状态设置为 false，从而关闭模态。

在 render 方法中，根据模态的可见性动态添加了一个 background div，还向 div 传递了一个 **onClick** 侦听器，这样它就可以控制模态的打开和关闭。还增加了一个带有 **onClick** 监听器的按钮来控制模态的开启。

先前的模态组件被导入并在 render 方法中使用，同时将 props 的值传递给它。

### 测试模态

在项目的根目录下运行 **npm start** 来查看模式。

[项目回购](https://github.com/achowba/react-modal)

谢了。