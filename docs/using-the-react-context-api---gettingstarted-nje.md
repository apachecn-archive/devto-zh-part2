# 使用 React 上下文 API -入门

> 原文：<https://dev.to/spences10/using-the-react-context-api---gettingstarted-nje>

###### 约翰·迈克尔·汤姆森在 Unsplash 上拍摄的封面照片

让我们使用 React 上下文 API 来更改应用程序中的主题！

[https://player.vimeo.com/video/295024503](https://player.vimeo.com/video/295024503)

## 不过先来点**的脉络**！🤣

好了，把糟糕的双关语放在一边，让我们看看 React Context API 是什么，它做什么。从[反应文件](https://reactjs.org/docs/context.html)中有一个很好的例子...

> 上下文提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。

或者换句话说，你可以使用 React Context API 来避免 [prop drilling](https://blog.kentcdodds.com/prop-drilling-bb62e02cb691) 如果你需要这个概念的更多细节，那么请查看提供的链接。

我之前在我的 Gatsby 博客中回顾了 React Context API 的实现，我在实现的时候记录了它；你可以看到[是如何到达这里的](https://blog.scottspence.me/react-context-api-with-gatsby)。

> ### 给我解释一下上下文 API。
> 
> 从 [@leighchalliday](https://twitter.com/leighchalliday) 中可以找到一个解释 API 的很好的资源，其中有一个关于这个主题的[的很好的用例](https://www.youtube.com/watch?v=yzQ_XulhQFw)。

## 我们在做什么...

在这篇文章中，我们将扩展我们为[styled-components getting started](https://medium.com/styled-components/styled-components-getting-started-c9818acbcbbd)创建的示例，因为它包含了我们开始使用 React Context API 所需的大部分代码。

我们将扩展该示例来管理示例应用程序的主题状态。

所以总而言之:

*   搭建基本的 CreateReact 应用程序
*   使用样式组件💅用于造型
*   使用 React 上下文 API 添加主题进行切换
*   使用 React 上下文 API！

## 我们需要什么...

我们需要的只是一个互联网连接和一个现代化的网络浏览器！因为我们将在棒极了的[代码沙箱](https://codesandbox.io)中在线完成所有这些！

不管你有没有 GitHub 账号，CodeSandbox 都会让你直接开始[编码](https://codesandbox.io/s/new)！

### 版本:

本指南正与以下依赖版本一起使用。

*   反应:16.4.2
*   反应范围:16.4.2
*   反应脚本:1.1.4
*   样式组件:3.4.5

* * *

## 我们开始吧

因此，让我们再次检查基本 create react 应用程序的主题，这一次我们将使用 react 上下文 API 来管理状态，而不是向组件添加状态。有些人会认为这对于主题切换来说有点过了，但是在 React 文档中它是作为何时使用上下文 API 的例子给出的，所以我会让你决定这一点的有效性。
对于这个例子，我希望它能让你更清楚地了解如何在应用程序中使用上下文 API。

### 依赖关系

[打开一个 React CodeSandbox](https://codesandbox.io/s/new) ，添加`styled-components`作为依赖项:

[https://player.vimeo.com/video/295024531](https://player.vimeo.com/video/295024531)

### 文件结构

[bikeshedding](https://en.wiktionary.org/wiki/bikeshedding) 的另一个领域是文件结构，在这个场景中，我们为`components`、`contexts`和`theme`添加文件夹，请随意以您认为合适的方式组织您的文件，这就是我们在❤️这个例子中要做的

将目录添加到`src`文件夹中，这样我们可以添加一些组件，文件结构应该如下所示:

```
context-demo/
├─ public/
├─ src/
│  └─ components/
│  └─ contexts/
│  └─ theme/
└─ package.json 
```

## 支架出一个基本的 Create React App

好的，那么，我们要做的是在`components`文件夹中添加一个`App.js`组件，然后在`src/index.js`文件中使用它。

对于这个例子来说，`App.js`组件可以是一个[无状态功能组件](https://reactjs.org/docs/state-and-lifecycle.html#the-data-flows-down),因为我们将使用上下文 API 处理状态。

在这里你可以看到我创建目录并添加`App.js`组件时的粗略输入:

[https://player.vimeo.com/video/295024556](https://player.vimeo.com/video/295024556)

然后我们可以删除`src/index.js`中的`style.css`文件和引用，因为我们将使用样式化组件进行样式化💅然后使用我们的`App.js`组件:

[https://player.vimeo.com/video/295024576](https://player.vimeo.com/video/295024576)

好的，所以我把`App.js`组件从`src/index.js`文件中抽象出来的原因是，当我们使用上下文 API 时，我们可以把它添加到应用程序的最高级别，也就是`src/index.js`。

### 剩下的呢？

因此，这不是真正的 Create React 应用程序，因为我们使用 CodeSandbox 代替，我已经过了在[styled-components getting started](https://medium.com/styled-components/styled-components-getting-started-c9818acbcbbd)帖子中使用的基本样式，所以是时候参考它来模仿我们需要的样式了。

这意味着我们要做的，不是深入研究构成基本 Create React 应用程序外观的每个组件的样式，而是重用组件，因此现在会涉及一些复制粘贴。

Create React App 样板代码有一个文件，我们在[样式-组件入门](https://medium.com/styled-components/styled-components-getting-started-c9818acbcbbd)帖子中对其进行了样式化处理，这是一个`App.js`文件，其他的被保留或删除，`App.js`的基本样式是:

**`App.css`**T3】

```
.App {
  text-align: center;
}

.App-logo {
  animation: App-logo-spin infinite 20s linear;
  height: 80px;
}

.App-header {
  background-color: #222;
  height: 150px;
  padding: 20px;
  color: white;
}

.App-title {
  font-size: 1.5em;
}

.App-intro {
  font-size: large;
}

@keyframes App-logo-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
} 
```

## 使用样式化组件进行样式化

现在我们要用样式化组件从`App.css`文件中重新创建样式，让我们在这里列出它们并浏览它们:

```
AppWrapper
AppHeader
AppTitle
rotate360
AppLogo
# We're adding our own styles for
AppIntro
Underline
StyledHyperLink
Button 
```

`AppWrapper`是顶层包装器，它在一个更大的组件中可用于 CSS Grid 或 Flexbox 的布局，在我们的例子中，我们将文本居中对齐。

[https://player.vimeo.com/video/295024592](https://player.vimeo.com/video/295024592)

够直接了吧。现在剩下的大部分组件将使用样式化的组件 [`ThemeProvider`](https://www.styled-components.com/docs/advanced#theming) ，这就是我们要从上下文 API 传递主题的内容。

## 用 React 上下文 API 添加主题进行切换

好的，我们需要定义一些主题来传递给`ThemeProvider`，我们将定义几个我们想要改变的主题方面，它们将是:

```
primary // colour
secondary // colour
danger // colour
fontHeader // font
fontBody // font 
```

在`theme`目录下创建一个包含主题对象的文件，命名为`globalStyle.js`，并添加如下内容:

```
import { injectGlobal } from 'styled-components'

export const themes = {
  theme1: {
    primary: '#ff0198',
    secondary: '#01c1d6',
    danger: '#e50000',
    fontHeader: 'Old Standard TT, sans, sans-serif',
    fontBody: 'Nunito, sans-serif'
  },

  theme2: {
    primary: '#6e27c5',
    secondary: '#ffb617',
    danger: '#ff1919',
    fontHeader: 'Enriqueta, sans-serif',
    fontBody: 'Exo 2, sans, sans-serif'
  },

  theme3: {
    primary: '#f16623',
    secondary: '#2e2e86',
    danger: '#cc0000',
    fontHeader: 'Kaushan Script, sans, sans-serif',
    fontBody: 'Headland One, sans-serif'
  }
}

injectGlobal`
  @import url('https://fonts.googleapis.com/css?family=Old+Standard+TT:400,700|Nunito:400,700'|Enriqueta:400,700|Exo+2:400,700|Kaushan+Script:400,700|Headland+One:400,700|');

  body {
    padding: 0;
    margin: 0;
  }
` 
```

[https://player.vimeo.com/video/295024607](https://player.vimeo.com/video/295024607)

好了，除了设置供以后使用的样式之外，这里什么也没有发生。

你会注意到这里使用了`injectGlobal`，这是我们设置整个应用程序使用的字体的地方，`injectGlobal` [应该在应用程序中使用一次](https://stackoverflow.com/a/42899789/1138354)来设置这样的全局样式。

向前！现在让我们把重点放在将基本的应用程序风格放入`App.js`组件中。我们现在可以开始使用`App.js`中的`ThemeProvider`。为了做到这一点，现在，为了获得一些视觉反馈，我们将应用来自`globalStyle.js`中`themes`对象的一个主题。

我们现在可以用`AppHeader`做这件事，它是一个样式化的 div:

```
const AppHeader = styled.div`
  height: 12rem;
  padding: 1rem;
  color: ${({ theme }) => theme.dark};
  background-color: ${({ theme }) => theme.primary};
` 
```

你会注意到我们开始使用样式化的组件，`theme`道具，但是，如果我们现在粘贴这段代码，在`ThemeProvider`被传递给`theme`对象之前不会有任何变化，所以我们要用`ThemeProvider`组件包装`App.js`，这样由`ThemeProvider`封装的任何组件都能够接收`theme`道具。

[https://player.vimeo.com/video/295024623](https://player.vimeo.com/video/295024623)

`AppTitle`将会是 h1 所以:

```
const AppTitle = styled.h1`
  font-family: ${({ theme }) => theme.fontHeader};
` 
```

对于旋转的 React 徽标，我们可以使用之前在[样式化组件入门示例](https://codesandbox.io/s/x26q7l9vyq)中使用的资源

我们可以将它添加到`App.js`组件顶部的导入中，并将其作为`img`标签添加到`AppLogo`样式的组件中:

```
const logo =
  'https://user-images.githubusercontent.com/234708/37256552-32635a02-2554-11e8-8fe3-8ab5bd969d8e.png' 
```

为了 react 徽标上的动画，需要将`keyframes`辅助对象与`ThemeProvider`一起导入。

```
const rotate360 = keyframes`
  from { 
    transform: rotate(0deg); 
  }
  to { 
    transform: rotate(360deg); 
  }
`

const AppLogo = styled.img`
  animation: ${rotate360} infinite 5s linear;
  height: 80px;
  &:hover {
    animation: ${rotate360} infinite 1s linear;
  }
` 
```

[https://player.vimeo.com/video/295024643](https://player.vimeo.com/video/295024643)

### 共享组件

共享组件包含在[风格-组件入门](https://medium.com/styled-components/styled-components-getting-started-c9818acbcbbd)指南中。如果您需要更多信息，对于本例，我们将引入最后几个组件作为`StyledHyperLink`和`Button`在`src/Shared.js`中的共享组件。添加以下内容:

**`src/Shared.js`**T3】

```
import styled, { css } from 'styled-components'

export const Button = styled.button`
  padding: 0.5rem 1rem;
  margin: 0.5rem 1rem;
  color: ${({ theme }) => theme.primary};
  font-size: 1rem;
  box-shadow: 0 3px 5px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  border: 2px solid ${props => props.border};
  background-color: Transparent;
  text-transform: uppercase;
  border-radius: 4px;
  transition: all 0.1s;
  &:hover {
    transform: translateY(1px);
    box-shadow: 0 2px 3px rgba(0, 0, 0, 0.15);
  } ${props =>
    props.primary &&
    css`
      background: ${({ theme }) => theme.primary};
      border: 2px solid ${({ theme }) => theme.primary};
      color: white;
    `}; ${props =>
    props.danger &&
    css`
      background: ${({ theme }) => theme.danger};
      border: 2px solid ${({ theme }) => theme.danger};
      color: white;
    `};
  &:hover {
    transform: translateY(2px);
    box-shadow: 0 2px 3px rgba(0, 0, 0, 0.15);
  }
`

export const StyledHyperLink = styled.a`
  cursor: pointer;
  &:visited,
  &:active {
    color: ${({ theme }) => theme.primary};
  }
  &:hover {
    color: ${({ theme }) => theme.secondary};
  }
  color: ${({ theme }) => theme.primary};
` 
```

然后像导入其他组件一样导入组件:

[https://player.vimeo.com/video/295024669](https://player.vimeo.com/video/295024669)

现在最后三个组件，`AppIntro`、`Underline`和`StyledHyperLink` :

```
const AppIntro = styled.p`
  color: ${({ theme }) => theme.dark};
  font-size: large;
  code {
    font-size: 1.3rem;
  }
  font-family: ${({ theme }) => theme.fontBody};
`

const Underline = styled.span`
  border-bottom: 4px solid ${({ theme }) => theme.secondary};
`

const StyledHyperLink = SHL.extend`
  text-decoration: none;
  font-family: ${({ theme }) => theme.fontBody};
  color: ${({ theme }) => theme.fontDark};
` 
```

[https://player.vimeo.com/video/295024697](https://player.vimeo.com/video/295024697)

将它们添加到`AppLogo`样式的组件下，然后我们可以将其余的组件添加到`App`函数`return`中，那么，准备好另一个[复制意大利面](#)了吗？这里:

```
<AppIntro>
  Bootstrapped with{'  '}
  <Underline>
    <code>
      <StyledHyperLink
        href={`https://github.com/facebook/create-react-app`}
        target="_blank"
        rel="noopener"
      >
        create-react-app
      </StyledHyperLink>
    </code>
  </Underline>. </AppIntro> <AppIntro>
  Components styled with{'  '}
  <Underline>
    <code>
      <StyledHyperLink
        href={`https://www.styled-components.com`}
        target="_blank"
        rel="noopener"
      >
        styled-components
      </StyledHyperLink>
    </code>
  </Underline>{' '}
  <span role="img" aria-label="nail polish">
    💅
  </span> </AppIntro> <AppIntro>
  Fonts picked with{'  '}
  <Underline>
    <code>
      <StyledHyperLink
        href={`https://fontjoy.com/`}
        target="_blank"
        rel="noopener"
      >
        fontjoy.com
      </StyledHyperLink>
    </code>
  </Underline> </AppIntro> <Button>Normal Button</Button> <Button primary>Primary Button</Button> <Button danger>Danger Button</Button> 
```

抱歉代码墙！将它粘贴到结束标签下，我们就有了主题的基础了！

[https://player.vimeo.com/video/295024721](https://player.vimeo.com/video/295024721)

好吗？看起来怎么样？

现在我们有了一个使用样式化组件的基本 React 应用程序！

## 使用 React 上下文 API

现在是重头戏！在这里，我们将讨论:

*   制作主题背景。

*   对组件使用上下文 API。

*   在多个组件中使用上下文 API。

因此，通过组件传递不必要的状态是我们可以使用上下文 API 来避免的。如果我们看一下[样式组件入门示例](https://codesandbox.io/s/x26q7l9vyq)，我们可以看到状态在`App.js`组件中被管理，而`handleThemeChange`函数必须被传递给`ThemeSelect`组件，就像任何道具都需要被传递一样。这是一个简单的例子，但是很容易想象，如果这个组件存在于一个页脚组件或一个菜单项上，将会有几个其他组件需要通过它们传递状态，而实际上并不需要那个状态或属性。有道理吗？

**例题**

```
<App>               {/* state begins here */}
  <Header>          {/* through here */}
    <Navigation>    {/* and here */}
      <ThemeSelect> {/* to be used here */}
    </Navigation>
  </Header>
  <Footer/>
</App> 
```

### 添加站点主题上下文

在我们的`src/contexts/`目录中，我们将创建我们的`SiteThemeContext.js`，导入反应并定义和导出我们的上下文:

```
import React from 'react'

export const SiteThemeContext = React.createContext() 
```

### 那么什么是语境呢？

一个上下文由两个部分组成，一个提供者和一个消费者，你有一个提供者，它将尽可能地位于组件树的高处，这样多个消费者可以从提供者那里获得状态和属性。

希望你还记得我们从`src/index.js`文件中抽象出`function App`组件的地方，这样我们就可以在`src/index.js`文件中添加应用程序最高级别的上下文提供者。这意味着应用程序中的任何消费者，无论组件树有多深，都可以从顶层获得状态和道具。

现在创建一个提供者，这个提供者是一个常规的 React 组件，
所以:

```
import React from 'react'

export const SiteThemeContext = React.createContext()

export class SiteThemeProvider extends React.Component {
  render() {
    return (
      <SiteThemeContext.Provider value={}>
        {this.props.children}
      </SiteThemeContext.Provider>
    )
  }
} 
```

由`<SiteThemeProvider>`返回的是`<SiteThemeContext.Provider>`和该组件的子组件，你必须提供给提供者的一个道具是`value`道具。这是消费者可以访问的变量。消费者是`<SiteThemeContext.Consumer>`(稍后将详细介绍)。

所以我们现在能做的是让传入`value`的东西成为一个对象`value={{}}`，这样它就可以存储在`SiteThemeContext`中定义的状态和函数的多个属性。

上下文的状态需要是`theme`，所以我们需要从`src/theme/globalStyle`导入主题，并将其添加到状态，我们将默认主题(和状态)为`theme1`，并通过扩展到状态`...❤️`将该主题的副本添加到`value`属性中，它看起来应该是这样的:

```
import React from 'react'
import PropTypes from 'prop-types'

import { themes } from '../theme/globalStyle'

export const SiteThemeContext = React.createContext()

export class SiteThemeProvider extends React.Component {
  state = {
    theme: themes['theme1']
  }

  render() {
    return (
      <SiteThemeContext.Provider
        value={{
          ...this.state
        }}>
        {this.props.children}
      </SiteThemeContext.Provider>
    )
  }
} 
```

好了，已经有一段时间没有加 gif 了，是时候再加一张了！

[https://player.vimeo.com/video/295024744](https://player.vimeo.com/video/295024744)

并引入`themes`并添加状态:

[https://player.vimeo.com/video/295024763](https://player.vimeo.com/video/295024763)

现在，我们可以向提供者添加一个函数，根据通过`handleThemeChange`事件值:
选择的内容来更改主题状态

```
handleThemeChange = e => {
  const key = e.target.value
  const theme = themes[key]
  this.setState({ theme })
} 
```

任何想要使用它的提供商都可以使用它，我们需要将它添加到`value` prop 中，就像这样:

```
import React from 'react'
import PropTypes from 'prop-types'

import { themes } from '../theme/globalStyle'

export const SiteThemeContext = React.createContext()

export class SiteThemeProvider extends React.Component {
  state = {
    theme: themes['theme1']
  }

  handleThemeChange = e => {
    const key = e.target.value
    const theme = themes[key]
    this.setState({ theme })
  }

  render() {
    return (
      <SiteThemeContext.Provider
        value={{
          ...this.state,
          handleThemeChange: this.handleThemeChange
        }}>
        {this.props.children}
      </SiteThemeContext.Provider>
    )
  }
} 
```

好了，这就是站点主题上下文组件，非常简单，对吗？

我应该提到的是，`handleThemeChange`函数中的`e`将是我们将要制作的主题选择框中的事件。

让我们完成添加函数并将其添加到状态中:

[https://player.vimeo.com/video/295024781](https://player.vimeo.com/video/295024781)

现在我们可以将主题提供者添加到`src/index.js`中，这样依赖关系树中更低的任何东西都可以通过消费者访问它。

[https://player.vimeo.com/video/295024798](https://player.vimeo.com/video/295024798)

### 添加主题选择

现在我们想要通过`SiteThemeContext`调用`SiteThemeProvider`的一部分`handleThemeChange`函数！我相信现在这一切都很有意义(🤣)所以让我们直接进入这里，定义我们将用来使用`ThemeSelect`组件消费`SiteThemeContext.Provider`的组件！

在`src/components`目录中添加一个新的`ThemeSelect.js`组件，这是我们将使用消费者来消费站点主题上下文的地方

消费者的孩子不是一个组件，而是一个函数，所以我们需要做的是在函数的返回中选择主题。

让我们首先设置组成选择的样式化组件，这是一个选择框、一些选项和一个包装器。

首先，我们将在没有消费者的情况下进行，然后我们将添加消费者。

**`ThemeSelect.js`**T3】

```
import React from 'react'
import styled from 'styled-components'

import { themes } from '../theme/globalStyle'

const SelectWrapper = styled.div`
  margin: 0rem 0.5rem 0rem 0.25rem;
  padding: 0rem 0.5rem 0rem 0.25rem;
`

const Select = styled.select`
  margin: 1.5rem 0.5rem;
  padding: 0.25rem 0.5rem;
  font-family: ${({ theme }) => theme.fontBody};
  border: 2px solid ${({ theme }) => theme.secondary};
  box-shadow: 0px 0px 0px 1px rgba(0, 0, 0, 0.1);
  background: ${({ theme }) => theme.foreground};
  border-radius: 4px;
`

export const SelectOpt = styled.option`
  font-family: ${({ theme }) => theme.fontBody};
`

const ThemeSelect = props => {
  return (
    <SelectWrapper>
      <Select>
        {Object.keys(themes).map((theme, index) => {
          return (
            <SelectOpt key={index} value={theme}>
              Theme {index + 1}
            </SelectOpt>
          )
        })}
      </Select>
    </SelectWrapper>
  )
}

export default ThemeSelect 
```

[https://player.vimeo.com/video/295024813](https://player.vimeo.com/video/295024813)

因此，我们可以在`themes`对象中列出可用的主题。但仅此而已，处理主题变化的功能存在于`SiteThemeProvider`上

回到前面我提到的`SiteThemeContext.Consumer`消费者的孩子是一个函数`() => ()`第一部分是来自提供者的`value`(`<SiteThemeContext.Provider>`)所以让我们快速看一下我们之前在提供者中定义的:

```
value={{
  ...this.state,
  handleThemeChange: this.handleThemeChange
}} 
```

从`SiteThemeContext.Provider`获得的是状态和函数，因此我们可以提取其中的任何一项并将其传递给提供者，或者换句话说，消费者可以访问这些值。

这里我们可以使用析构来拉我们需要改变主题的`handleThemeChange`函数。

```
import React from 'react'

import { SiteThemeContext } from '../contexts/SiteThemeContext'

const ThemeSelect = props => {
  return (
    <SiteThemeContext.Consumer>
      {({ handleThemeChange }) => ()}
    </SiteThemeContext.Consumer>
  )
}

export default ThemeSelect 
```

[https://player.vimeo.com/video/295024838](https://player.vimeo.com/video/295024838)

目前这不会改变主题，因为我们已经将它硬编码到样式组件`ThemeProvider`中，我们想要做的是在`SiteThemeContext`中为当前选择的主题使用一个消费者。

在此之前，我们还需要添加我们想要用来将事件(`e`)传递给`SiteThemeContext`上的`handleThemeChange`函数的`onChange`事件。

然后在`App`组件中，我们可以导入我们的`<SiteThemeContext.Consumer>`来消费`SiteThemeContext`状态下的`theme`，并将其传递给样式化组件`ThemeProvider`。

[https://player.vimeo.com/video/295024865](https://player.vimeo.com/video/295024865)

### 想了解更多？

正如本文开头提到的，一个很好的资源是 [@leighchalliday](https://twitter.com/leighchalliday) 和[他的 YouTube 频道](https://www.youtube.com/channel/UCWPY8W-FAZ2HdDiJp2RC_sQ)，在那里你可以找到他的[React Context API 的伟大用例](https://www.youtube.com/watch?v=yzQ_XulhQFw)。

还有 spectrum 上的 [React 社区](https://spectrum.chat/react)和 spectrum 上的[风格组件](https://spectrum.chat/styled-components)。

[演练的示例代码](https://codesandbox.io/s/5vl16n5oxp)可在 [CodeSandbox](https://codesandbox.io) 上获得。

### 感谢阅读🙏

如果我错过了什么，或者如果有更好的方法做某事，请让我知道。

这篇文章最初发布在[我的盖茨比博客](https://blog.scottspence.me)上，你可以在这里[查看它](https://blog.scottspence.me/react-context-api-getting-started)如果你喜欢这篇文章，请看看我的其他内容。

在 Twitter 上关注我，或者在 GitHub 上关注 T2 问我任何问题。