# 将 Bootstrap 4 与 React 配合使用

> 原文：<https://dev.to/techiediaries/using-bootstrap-4-with-react-273m>

Bootstrap 4 是用于构建响应式布局的最流行的 CSS 框架，具有许多新特性，如支持 Flexbox 和一个新的卡组件。

Bootstrap 4 依赖于 jQuery 和 Popper.js，但是不建议将 jQuery 与 React 一起使用，因为 jQuery 使用直接的 DOM 操作。

如果您需要将 Bootstrap 4 样式添加到 React 应用程序中，社区已经创建了一些包，可以在没有 jQuery 的情况下使用 Bootstrap 4，但仍然可以使用 BS 4 的完整功能和组件。

在本教程中，我们将看到如何使用 Reactstrap

所以回到您的终端，在 React 项目中导航，运行下面的命令来安装 bootstrap 和 reactstrap

```
npm install --save bootstrap reactstrap@next 
```

Enter fullscreen mode Exit fullscreen mode

我们需要安装引导程序，因为*需要引导程序 4 个 CSS 文件*

使用
在`src/index.js`文件中导入引导 CSS

```
import 'bootstrap/dist/css/bootstrap.css'; 
```

Enter fullscreen mode Exit fullscreen mode

您也可以在您的项目`index.html`文件中使用一个`<link>`标签。

然后，您可以导入单个 Bootstrap 4 组件并使用它们。

打开`src/App.js`然后添加下面的[例子](https://gist.github.com/eddywashere/e13033c0e655ab7cda995f8bc77ce40d) :

```
import React, { Component } from 'react';
import {
  Collapse,
  Navbar,
  NavbarToggler,
  NavbarBrand,
  Nav,
  NavItem,
  NavLink,
  Container,
  Row,
  Col,
  Jumbotron,
  Button
} from 'reactstrap';
class App extends Component {
  constructor(props) {
    super(props);

    this.toggle = this.toggle.bind(this);
    this.state = {
      isOpen: false
    };
  }
  toggle() {
    this.setState({
      isOpen: !this.state.isOpen
    });
  }
  render() {
    return (
      <div>
        <Navbar color="inverse" inverse toggleable>
          <NavbarToggler right onClick={this.toggle} />
          <NavbarBrand href="/">reactstrap</NavbarBrand>
          <Collapse isOpen={this.state.isOpen} navbar>
            <Nav className="ml-auto" navbar>
              <NavItem>
                <NavLink href="/components/">Components</NavLink>
              </NavItem>
              <NavItem>
                <NavLink href="https://github.com/reactstrap/reactstrap">Github</NavLink>
              </NavItem>
            </Nav>
          </Collapse>
        </Navbar>
        <Jumbotron>
          <Container>
            <Row>
              <Col>
                <h1>Welcome to React</h1>
                <p>
                  <Button
                    tag="a"
                    color="success"
                    size="large"
                    href="http://reactstrap.github.io"
                    target="_blank"
                  >
                    View Reactstrap Docs
                  </Button>
                </p>
              </Col>
            </Row>
          </Container>
        </Jumbotron>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

如果您无法在 React 应用程序中导入引导 CSS 文件，您可以查看这个 [StackOverflow 解决方案](https://stackoverflow.com/questions/42436728/webpack2-how-to-import-bootstrap-css-for-react-bootstrap-to-find-its-styles/42440360)

[https://upscri.be/wvwuxi](https://upscri.be/wvwuxi)