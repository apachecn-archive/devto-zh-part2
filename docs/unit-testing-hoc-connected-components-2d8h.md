# 如何测试 React 中的高阶元件

> 原文：<https://dev.to/papaponmx/unit-testing-hoc-connected-components-2d8h>

## [T1】简介](#intro)

**注意**:我假设你对 JavaScript 中的单元测试有点熟悉，并且知道什么是高阶组件。

我正在给我最喜欢的一个项目添加单元测试。我用的是`react-boilerplate`作为入门应用，所以 Enzyme 和 Jest 已经插上了。

这是我刚刚遇到的一个问题的一个简短的讨论。

## 问题

测试 HOCs 是一个非常特殊的场景，因为它使用了`mapStateToProps`和一个高阶元件。

就拿你经典的`Authentication`组件来说吧。它从 state 中读取一个`boolean`,评估用户是否通过了身份验证，并相应地返回组件或重定向到给定的 URL。

这是我们的组件的样子:

```
 /**
 *
 * Auth.js
 *
 */

import React, { Component } from 'react';
import { connect } from 'react-redux';
import { push } from 'react-router-redux';
import { bindActionCreators } from 'redux';
import PropTypes from 'prop-types';

export default function(ComposedComponent) {
  class Authentication extends Component {
    /* eslint-disable */
    componentWillMount() {
      if (this.props.authenticated === false) {
        return this.props.changePage();
      }
    }

    componentWillUpdate(nextProps) {
      if (nextProps.authenticated === false) {
        return this.props.changePage();
      }
    }
    /* eslint-enable */

    render() {
      return <ComposedComponent {...this.props} />;
    }
  }

  Authentication.propTypes = {
    authenticated: PropTypes.bool,
    changePage: PropTypes.func,
  };

  const mapStateToProps = state => ({ authenticated: state.user.isLoaded });

  const mapDispatchToProps = dispatch =>
    bindActionCreators({ changePage: () => push('/login') }, dispatch);

  return connect(
    mapStateToProps,
    mapDispatchToProps,
  )(Authentication);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 解

对于商店，我们很好地使用了一个 sweet 库，它允许我们模仿一个 Redux 商店，正如你可以想象的，它被称为`redux-mock-store`。

`yarn add redux-mock-store --dev`

那么我们的测试应该做以下事情:

1.  使用我们的 HOC 需要映射到的属性创建一个基本存储。在这个场景中，是`store.user.isLoaded`。
2.  创建一个组件，当用户通过认证时，`Auth`应该呈现这个组件。
3.  断言它返回(或呈现)某些东西。

让我们一步一步来。

```
 import configureStore from 'redux-mock-store';
import Auth from './Auth';

let store;

describe('<Auth /> test', () => {
  beforeEach(() => {
    const mockStore = configureStore();

    // creates the store with any initial state or middleware needed
    store = mockStore({
      user: {
        isLoaded: true,
      },
    });
... 
```

Enter fullscreen mode Exit fullscreen mode

请注意，mockStore 将我们的状态应该是什么样子作为一个参数。由于`Auth`只关心`user.isLoaded`，我们将它设为 true，并评估`Component`正在被渲染。我们将使用目前我能想到的最通用的方法。

```
// Auth.spec.js
...
  it('Should render the component only when auth prop is true', () => {
    const Component = <h1>Hola</h1>;
    const ConditionalComponent = Auth(Component);
    const wrapper = shallow(<ConditionalComponent store={store} />);
    expect(wrapper).not.toBe(null); 
```

Enter fullscreen mode Exit fullscreen mode

我们只需要将刚刚创建的存储作为道具传递，然后断言我们的组件正在被呈现。

我们的测试放在一个文件里。

```
 /**
 *
 * Auth.spec.js
 *
 */

import React from 'react';
import { shallow } from 'enzyme';
import configureStore from 'redux-mock-store';

import Auth from '../Auth';

let store;

describe('<Auth />', () => {
  beforeEach(() => {
    const mockStore = configureStore();

    // creates the store with any initial state or middleware needed
    store = mockStore({
      user: {
        isLoaded: true,
      },
    });
  });
  it('Should render the component only when auth prop is true', () => {
    const Component = <h1>Hola</h1>;
    const ConditionalHOC = Auth(Component);
    const wrapper = shallow(<ConditionalHOC store={store} />);
    expect(wrapper).not.toBe(null);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这个测试只覆盖了一个场景，但是所有需要的断言都可以从这里开始。

干杯。