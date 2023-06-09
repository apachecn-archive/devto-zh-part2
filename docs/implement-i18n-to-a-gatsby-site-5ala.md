# 在 Gatsby 站点上实现 i18n

> 原文：<https://dev.to/smakosh/implement-i18n-to-a-gatsby-site-5ala>

> 这篇文章最初发表在 [Obytes 博客](https://www.obytes.com/blog/2018/implement-i18n-to-gatsby/)上。

[![i18n on gatsby](img/23d2f1349c47f7e49092bacad0b0d38c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2B4YEeMg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/f5e077cd6bfbfc8c5f1d97953f0ea318ca0943ff/70e5e/asseimg/blog/i18n-gatsby/i18n.png)

在这篇文章中，我们将使用 react-intl 和 [React context API](https://reactjs.org/docs/context.html) 对一个 [Gatsby](https://gatsbyjs.org) 站点实现 i18n(国际化),在这篇文章中，我们将只涉及英语和阿拉伯语，但是如果你愿意，你可以添加更多的语言，在我们开始之前，让我们首先计划我们想要如何实现它。

1-检测用户的默认语言

2-根据用户的默认语言自动切换语言、内容方向和字体系列

3-用户仍然可以选择他们喜欢的语言

让我们从使用他们的 CLI 工具
生成一个新的 Gatsby 站点开始

```
gatsby new gatsby-i18n-example && cd gatsby-i18n-example/ 
```

然后我们将安装我们需要的库(我使用的是`yarn`，但也可以随意使用`npm`)

> 我正在安装[重新组合](https://github.com/acdlite/recompose)来将逻辑从组件中分离出来并保持代码的整洁(可以不用它，但我们强烈推荐它)，以及[样式组件](https://www.styled-components.com/) beta v4 来处理 js 中的 css(可以不用它，但我们强烈推荐它)和一个简单的 google fonts gatsby 插件`gatsby-plugin-google-fonts`

```
yarn add react-intl recompose styled-components@next babel-plugin-styled-components gatsby-plugin-styled-components gatsby-plugin-google-fonts 
```

在我们开始之前，让我们先用一种更好的方式来组织文件，如下图所示

```
.
+-- src
    +-- components
    |   |
    |   +-- common
    |   |   +-- Head
    |   |   |   |
    |   |   |   +-- index.jsx
    |   |   +-- Container
    |   |   |   |
    |   |   |   +-- index.jsx
    |   |   +-- Context
    |   |   |   |
    |   |   |   +-- index.jsx
    |   |   +-- Layout
    |   |   |   |
    |   |   |   +-- index.jsx
    |   |   |   +-- Provider.jsx
    |   |   |   +-- layout.css
    |   |   +-- Trigger
    |   |   |   |
    |   |   |   +-- index.jsx
    |   |   +-- index.js
    |   +-- theme
    |   |   +-- Header
    |   |   |   |
    |   |   |   +-- index.jsx
    +-- messages
    |   |
    |   +-- ar.json
    |   +-- en.json
    +-- pages
        |
        +-- index.js
        +-- 404.js
        +-- about.js 
```

让我们从在上下文组件中创建上下文开始，并将`en`作为默认值。

```
import React from 'react'

export const Context = React.createContext('en') 
```

现在让我们来看看将全局状态传递给消费者的提供者组件，消费者是它的后代。

> [Provider](https://reactjs.org/docs/context.html#provider) 是一个 React 组件，允许消费者订阅上下文变化。

```
import React from 'react'
import { compose, withState, withHandlers, lifecycle } from 'recompose'
import { Context } from '../Context'

const Provider = ({ children, lang, toggleLanguage }) => (
    <Context.Provider value={
        { lang, toggleLanguage: () => toggleLanguage() }
    }>
        {children}
    </Context.Provider>
)

const enhance = compose(
    withState('lang', 'handleLanguage', 'en'),
    withHandlers({
        toggleLanguage: ({ lang, handleLanguage }) => () => {
            if (lang === 'ar') {
                handleLanguage('en')
                localStorage.setItem('lang', 'en')
            } else {
                handleLanguage('ar')
                localStorage.setItem('lang', 'ar')
            }
        }
    }),
    lifecycle({
        componentDidMount() {
            const localLang = localStorage.getItem('lang')
            if (localLang) {
                this.props.handleLanguage(localLang)
            } else {
                this.props.handleLanguage(navigator.language.split('-')[0])
            }
        }
    })
)

export default enhance(Provider) 
```

这将包装我们所有的组件，这样我们就可以访问包含`lang`的值和一个切换语言的函数`toggleLanguage`，组件下面是逻辑。

我们用默认值`en`初始化`lang`，但是当组件挂载时，这个值可能会改变，我们检查 localStorage 是否可用，如果为真:我们将其值赋给`lang`状态，否则:我们检测用户浏览器的默认语言，并拆分该值以获得包含该语言的第一项。

现在转到`Layout`组件，其中:

*   我们将导入英语和阿拉伯语的 json 数据
*   和`IntlProvider`一起包装我们将使用`react-intl`内置组件的内容
*   以及导入`Context`并用它的消费者包装我们的内容，这样我们就可以访问全局状态
*   最后，我们用上面创建的`Provider`包装所有东西。

```
import React from 'react'
import styled from 'styled-components'
import ar from 'react-intl/locale-data/ar'
import en from 'react-intl/locale-data/en'
import { addLocaleData, IntlProvider } from 'react-intl'
import localEng from '../../../messages/en.json'
import localAr from '../../../messages/ar.json'
import { Context } from '../Context'
import Provider from './Provider'
import Header from '../../theme/Header'
import './layout.css'

addLocaleData(ar, en)

const Layout = ({ children }) => (
    <Provider>
        <Context.Consumer>
            {({ lang }) => (
                <IntlProvider locale={lang} messages={lang === 'en' ? localEng : localAr}>
                    <Global lang={lang}>
                        <Header />
                        {children}
                    </Global>
                </IntlProvider>
            )}
        </Context.Consumer>
    </Provider>
)

const Global = styled.div`
    font-family: 'Roboto', sans-serif; ${({ lang }) => lang === 'ar' && `
        font-family: 'Cairo', sans-serif;    
    `} `

export { Layout } 
```

我们忘了说，我们使用了`Global`组件来处理字体变化，所以当语言设置为英语时，它将是`Roboto`,当设置为阿拉伯语时，它将是`Cairo`。

现在一切都准备好了，让我们给标题添加一个按钮来切换语言

```
import React from 'react'
import styled from 'styled-components'
import { Link } from 'gatsby'
import { FormattedMessage } from 'react-intl'
import { Trigger, Container } from '../../common'

const Header = () => (
    <StyledHeader>
        <Navbar as={Container}>
            <Link to="/">
                <FormattedMessage id="logo_text" />
            </Link>
            <Links>
                <Link to="/">
                    <FormattedMessage id="home" />
                </Link>
                <Link to="/about">
                    <FormattedMessage id="about" />
                </Link>
                <Trigger />
            </Links>
        </Navbar>
    </StyledHeader>
)

// Feel free to move these to a separated styles.js file and import them above

const StyledHeader = styled.div`
    padding: 1rem 0;
    background: #00BCD4;
`
const Navbar = styled.div`
    display: flex;
    align-items: center;
    justify-content: space-between;
    a {
        color: #fff;
        text-decoration: none;
    }
`
const Links = styled.div`
    display: flex;
    align-items: center;
    a {
        margin: 0 1rem;
    }
`

export default Header 
```

我们把改变语言的按钮单独分离出来，这样我们就能很好地理解它

```
import React from 'react'
import styled from 'styled-components'
import { FormattedMessage } from 'react-intl'
import { Context } from '../Context'

const Trigger = () => (
    <Context.Consumer>
        {({ toggleLanguage }) => (
            <Button type="button" onClick={toggleLanguage}>
                <FormattedMessage id="language" />
            </Button>
        )}
    </Context.Consumer>
)

// We recommend moving the style down below to a separate file

const Button = styled.button`
    color: #fff;
    padding: .3rem 1rem;
    box-shadow: 0 4px 6px rgba(50,50,93,.11), 0 1px 3px rgba(0,0,0,.08);
    background: #3F51B5;
    border-radius: 4px;
    font-size: 15px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: .025em;
    text-decoration: none;
    cursor: pointer;
    &:focus {
        outline: none;
    }
`

export { Trigger } 
```

我们在这个文件中再次导入了`Context`,这样我们就可以使用它的`Consumer`,从而获得全局状态。现在，当点击按钮时，`toggleLanguage`功能会改变`lang`值。

在获取 Gatsby 配置文件之前，让我们通过从上下文的消费者那里访问`lang`值来处理内容的方向，并有条件地检查它是否是阿拉伯语，如果是，方向必须变成`rtl`，否则变成`lrt`。

```
import React from 'react'
import { Helmet } from 'react-helmet'
import { injectIntl } from 'react-intl'
import { Context } from '../Context'

const Head = ({ title, intl: { formatMessage } }) => (
    <Context.Consumer>
        {({ lang }) => (
            <Helmet>
                <html lang={lang} dir={lang === 'ar' ? 'rtl' : 'ltr'} />
                
                    ${formatMessage({ id: title })}
                
            </Helmet>
        )}
    </Context.Consumer>
)

export default injectIntl(Head) 
```

> 你可以在这个`Head`组件中包含所有的元标签、opengraph、结构化数据和 Twitter 标签，就像我在 [gatsby-i18n-starter](https://github.com/smakosh/gatsby-starter-i18n/blob/master/src/components/common/SEO/index.jsx) 中做的那样

最后，让我们将正在使用的插件包含到`gatsby-config.js`文件中，并准备一些包含支持 i18n 的消息的虚拟页面。

```
module.exports = {
    siteMetadata: {
        title: 'Gatsby i18n Example',
    },
    plugins: [
        'gatsby-plugin-react-helmet',
        'gatsby-plugin-styled-components',
        {
            resolve: 'gatsby-plugin-google-fonts',
            options: {
                fonts: [
                    'Cairo',
                    'Roboto'
                ]
            }
        },
        {
            resolve: 'gatsby-plugin-manifest',
            options: {
                name: 'gatsby-starter-default',
                short_name: 'starter',
                start_url: '/',
                background_color: '#663399',
                theme_color: '#663399',
                display: 'minimal-ui',
                icon: 'simg/gatsby-icon.png',
            },
        },
        'gatsby-plugin-offline',
    ],
} 
```

*   主页

```
import React from 'react'
import { FormattedMessage } from 'react-intl'
import { Layout, Container } from '../components/common'
import Head from '../components/common/Head'

const IndexPage = () => (
    <Layout>
        <>
            <Head title="welcome" />
            <Container>
                <h2>
                    <FormattedMessage id="welcome" />
                </h2>
            </Container>
        </>
    </Layout>
)

export default IndexPage 
```

*   关于页面

```
import React from 'react'
import { FormattedMessage } from 'react-intl'
import { Layout, Container } from '../components/common'
import Head from '../components/common/Head'

const AboutPage = () => (
    <Layout>
        <>
            <Head title="about" />
            <Container>
                <h2>
                    <FormattedMessage id="about" />
                </h2>
            </Container>
        </>
    </Layout>
)

export default AboutPage 
```

这里是包含我们在这个例子中使用的消息的两个 json 文件:

```
{
    "language": "عربي",
    "welcome": "Welcome",
    "Logo_text": "Logo",
    "Home": "Home",
    "About": "About",
    "not_found": "404 - Page Not Found"
} 
```

```
{
    "language": "English",
    "welcome": "أهلا بك",
    "Logo_text": "شعار",
    "Home": "الرئيسية",
    "About": "معلومات عنا",
    "not_found": "الصفحة غير موجودة - 404"
} 
```

让我们通过运行
来测试一下

```
yarn develop 
```

这似乎很有效🎉，[查看演示](https://i18n.netlify.com/)，这里是[到资源库](https://github.com/obytes/gatsby-i18n-example/)的链接，以防您无法跟进，有问题吗？请在评论中留言，我们会尽快回复。

> 请随意使用我自己的 [gatsby-i18n-starter](https://github.com/smakosh/gatsby-starter-i18n) 来轻松开始使用其他一些伟大的功能。