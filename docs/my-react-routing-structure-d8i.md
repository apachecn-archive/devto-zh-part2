# 我的反应路由结构

> 原文：<https://dev.to/legobox/my-react-routing-structure-d8i>

> 这件作品最初发布在我的博客上，地址是 [legobox](https://medium.com/legobox/my-react-routing-structure-20772c685810)

# 背景

我经常构建前端应用程序和 API，通常我使用各种工具，但 react 是我觉得有趣的东西，使我能够快速地构建，虽然我可以说 vue 逐字逐句地匹配它，但这不是今天的争论，react 与 Vue 的废话够多了，所以我两者都做。它们都很棒，我很高兴有可供选择的框架。在使用 react 开发和创建应用程序的过程中，我为我的应用程序提出了一个通用的结构和路由模式。

我认为这是我想分享的东西，并希望通过社区的指导来改进。

# 先决条件

对于这篇文章，只有两个先决条件，(上次我写了一个先决条件，它像野火一样蔓延，希望它不会要求太多，让它再次发生🙃)

*   你知道 react，有使用它的经验。
*   你听说过 redux 吗
*   您熟悉 react 路由器。

如果你记下来了？，那你就乖乖地滚吧。

# 问题

这个问题需要使用 react-router 库在页面之间导航，同时还要跟踪身份验证状态，关于身份验证和导航，对这个问题已经有了一个共同的理解，通常大家都知道可以通过 react router 和 redux 的简单设置来解决这个问题，但是这个设置有许多模式，在本文中，我将解释我的模式。

# 解

为了在应用程序中正确导航，我设置了 react-router 并实现了一个 3 组件身份验证检查流程，一个组件检查仅访问者页面，另一个组件检查受保护的路线，第三个组件包含所有内容并对应用程序的身份验证状态进行一般检查，我知道现在听起来不明确，但我们将查看代码结构并了解事情的本质。

```
 // import scenes here and make routes of them
    import React from 'react';
    import {Route} from 'react-router-dom';
    // import scenes here
    import {Dashboard, Settings, Login} from './Scenes'

    // auth checkers for checking if the routes are authorized
    import AppCheck from './Modules/AppCheck.jsx';
    import EnsureLoggedInContainer from './Modules/EnsureLoggedInContainer.jsx';
    import EnsureVisitorOnlyContainer from './Modules/EnsureVisitorOnlyContainer.jsx';

    const routes = (
      <React.Fragment>
            <AppCheck>
                    <EnsureVisitorOnlyContainer>
                            <Route path="/login" component={Login} />
                    </EnsureVisitorOnlyContainer>
                    <EnsureLoggedInContainer>
                            <Route exact path="/home" component={Dashboard} />
                            <Route path="/settings" component={Settings} />
                    </EnsureLoggedInContainer>
            </AppCheck>
      </React.Fragment>
    );

    export default routes; 
```

Enter fullscreen mode Exit fullscreen mode

这是直接从代码库中提取的，正如你所看到的，这个想法被清晰地呈现出来。

这种情况下的三个组件是`AppCheck`、`EnsureVisitorOnlyContainer`、`EnsureLoggedInContainer`。

让我们来看看这些组件的内部结构。

**AppCheck 组件。**

```
 import React from "react";
    import { connect } from "react-redux";
    import { withRouter } from "react-router";

    class AppCheck extends React.Component {

      componentDidUpdate(prevProps) {

        const { dispatch, redirectUrl } = this.props;
        const isLoggingOut = prevProps.isLoggedIn && !this.props.isLoggedIn;
        const isLoggingIn = !prevProps.isLoggedIn && this.props.isLoggedIn;

        if (isLoggingIn) {
            // dispatch(navigateTo(redirectUrl))
        } else if (isLoggingOut) {
          // do any kind of cleanup or post-logout redirection here
        }
      }

      render() {
        return this.props.children;
      }

    }

    const mapStateToProps = state => {
      console.log(state);
      return {
        isLoggedIn: state.isAuthenticated,
        redirectUrl: state.redirectUrl
      };
    };
    export default withRouter(connect(mapStateToProps)(AppCheck)); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，这个组件在更新时检查 prop 并确认以前的`isLoggedIn`状态，注意用户是否登录或注销，根据这一点，它重定向到 redux 设置中指定的另一个 url，或者如果它试图注销，它继续注销，否则它继续呈现 props 子对象。

**EnsureVisitorsOnlyContainer**

```
 import React from "react";
    import { connect } from "react-redux";
    import { withRouter } from "react-router";
    // import actions from "../Store/Actions";

    class EnsureVisitorOnlyContainer extends React.Component {

      componentDidMount() {

        const { currentURL } = this.props;
        var visitorRoutes = ["/", "", "terms", "conditions"];
        var check = visitorRoutes.indexOf(currentURL) > -1;

        if (this.props.isLoggedIn) {

          // set the current url/path for future redirection (we use a Redux action)
          // then redirect (we use a React Router method)
          //   dispatch(actions.setRedirectUrl(currentURL))
          if (check) {
            this.props.history.replace("/home");
          }
        }
      }

      render() {
        if (!this.props.isLoggedIn) {
          return this.props.children;
        } else {
          return null;
        }
      }
    }
    // Grab a reference to the current URL. If this is a web app and you are
    // using React Router, you can use `ownProps` to find the URL. Other
    // platforms (Native) or routing libraries have similar ways to find
    // the current position in the app.

    function mapStateToProps(state, ownProps) {
      console.log(ownProps);
      return {
        isLoggedIn: state.isAuthenticated,
        currentURL: ownProps.location.pathname
      };
    }
    export default withRouter(connect(mapStateToProps)(EnsureVisitorOnlyContainer)); 
```

Enter fullscreen mode Exit fullscreen mode

该组件集简单地检查当前路线是否在访问者路线中，如果是，那么它检查登录状态，如果应用程序登录，它将用户重定向到`/home`路线。

如果在访问者列表中找到了路径，则此组件集中列出的任何路由都将被重定向到本地路由。

**EnsureLoggedInContainer**

```
 import React from 'react'
    import {connect} from 'react-redux'
    import { withRouter } from 'react-router'
    import actions from '../Store/Actions'

    class EnsureLoggedInContainer extends React.Component {

      componentDidMount() {
        const { dispatch, currentURL } = this.props
        if (!this.props.isLoggedIn) {
          // set the current url/path for future redirection (we use a Redux action)
          // then redirect (we use a React Router method)
          dispatch(actions.setRedirectUrl(currentURL))
          this.props.history.replace("/")
        }
      }

      render() {
        if (this.props.isLoggedIn) {
          return this.props.children
        } else {
          return null
        }
      }
    }
    // Grab a reference to the current URL. If this is a web app and you are
    // using React Router, you can use `ownProps` to find the URL. Other
    // platforms (Native) or routing libraries have similar ways to find
    // the current position in the app.

    function mapStateToProps(state, ownProps) {
      console.log(ownProps)
      return {
        isLoggedIn: state.isAuthenticated,
        currentURL: ownProps.location.pathname
      }
    }

    export default withRouter(connect(mapStateToProps)(EnsureLoggedInContainer)) 
```

Enter fullscreen mode Exit fullscreen mode

这个容器检查应用程序的当前状态是否是未登录，它重定向到`/`路径，因此所有页面都通过这个结构进行管理。

当 prevProp 改变其状态时， **AppCheck** 处理注销过程，而**EnsureVisitorsOnlyContainer**和 **EnsureLoggedInContainer** 根据路线和它在哪个容器中列出来处理不同实例上的重定向。

# 结论

我乐于接受如何改进的建议，并听取您如何实现您的建议，随着时间的推移，我希望分享我在 react 和 vue 开发中使用的其他技巧和技术，并帮助其他人了解如何充分利用这些框架来更快地构建。