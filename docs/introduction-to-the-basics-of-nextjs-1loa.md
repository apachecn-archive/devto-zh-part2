# Next.js 基础介绍

> 原文：<https://dev.to/aurelkurtula/introduction-to-the-basics-of-nextjs-1loa>

Next.js 是一个在服务器上呈现 react 应用程序的框架。这意味着当我们创建一个普通的 react 应用程序(例如使用`create-react-app`)并将其加载到浏览器时，所有的内容都是由客户端 javascript 创建的。因此，没有 javascript 的用户或设备将无法访问您的应用程序。

您可以在运行中看到这一点，在浏览器中打开 react 应用程序，然后查看页面源代码。您将看不到您创建的内容！事实上，如果项目是通过`create-react-app`创建的，您会看到这个:

```
<noscript>
  You need to enable JavaScript to run this app.
</noscript>
<div id="root"></div> 
```

Enter fullscreen mode Exit fullscreen mode

这是显而易见的，因为我们使用 react 所做的只是将内容附加到`#root`(因此是`document.getElementById('root')`)。如果 javascript 被禁用，内容就会消失。

因此 Next.js 为我们解决了这个问题。

在学习 next.js 的同时，我试图创建一个小应用程序，它将具有 instagram 的基本功能——如果你喜欢，可以是一个迷你 instagram 克隆。我知道我们今天要写的代码是不可扩展的，因此不会对 react 应用程序有太大的贡献，但是它将涵盖 next.js 的基础知识

## 我们开始吧

首先我们需要初始化`npm`并安装 next.js 运行所需的包:

```
npm init
npm install --save next react react-dom 
```

Enter fullscreen mode Exit fullscreen mode

(如果你用`yarn`你知道该怎么做)

现在我们需要将以下脚本添加到生成的`package.json` :

```
 "scripts": {
    "start": "next"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

当我们想在浏览器中运行这个应用程序时，我们需要运行`npm start`。

Next.js 允许我们通过页面组织路线。在本教程中，我们将创建两个页面，看看他们如何互动。让我们首先创建索引页面。需要在`./pages/index.js`。在那里添加以下内容:

```
export default () => (
    <div className="app">
        <header>
            <h1>gallery</h1><h2>Original art</h2>
        </header>
    </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。

如果我们运行`npm start`，我们将显示索引页面。当然，这里最重要的是，如果你查看页面源代码，你会看到上面由 next js 为我们“硬编码”的 HTML。

### 造型

CSS 样式可以直接进入`pages/index.js`。使每个部分模块化。让我们把标题做得漂亮一点:

```
 export default () => (
    <div className="app">
        <header>
          <h1><Link href={{ pathname: '/' }}><a>gallery</a></Link></h1>
        </header>
        <style jsx>{`
            @import url('https://fonts.googleapis.com/css?family=Oleo+Script');
            //... you can get the full code at github
            header h1{
                font-family: 'Oleo Script', cursive;
                font-size: 25rem;
                color: #AD0044;
                text-shadow: #7F0D23 0px 0 40px;
                letter-spacing: -20px;

                display: inline-block;
            }
            h2{
                font-size: 3.5rem;
                color: white;
                text-shadow: none;
                letter-spacing: normal;
                font-weight: normal;
                font-family: 'Dancing Script', cursive;
                position: absolute;
                top: 50%;
                left: 50%;
            }

        `}</style>
    </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

从文档“我们捆绑了 styled-jsx 以提供对隔离的作用域 CSS 的支持”

以这种方式添加样式感觉就像一个黑客！我更喜欢外部风格，但它确实有助于保持一切隔离。我只是需要习惯它-也许吧。

### 添加组件

让我们通过在`./components/Layout.js`
中添加一个布局组件来进一步分解这段代码，这样它就不会变得混乱

```
import Link from 'next/link'
import Head from 'next/head'

export default ({ children, title = 'gallery: original art' }) => (
    <div>
        <Head>
        { title }
        <meta charSet='utf-8' />
        <meta name='viewport' content='initial-scale=1.0, width=device-width' />
        </Head>
        <div className="app">
            <header>
                <h1><Link href={{ pathname: '/' }}>gallery</Link></h1>
                <h2>Original art</h2>             </header>
            { children }
            <footer>
            Footer
            </footer>
        </div>
        <style global jsx>{`
            :root {
                --green:  #65C5D9; 
                --white: #F4F5F7;
                --light-gray: #EAEEEF;
            }
            ...
            body{...}
        `}</style>
        <style jsx>{`
            header{ ...}
            header h1 a{ ...}
            h2{....}
            footer { ...}

        `}</style>
    </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

上面需要注意的几个重要事项:

1.  Next.js 给了我们一个`Head`组件，通过它我们可以操纵 HTML `<head>`标签中的内容。例如，您可能想要更多元信息。
2.  我们应该使用`Link`组件来浏览页面。它也需要一个锚标记。我添加了一个空的锚标记，但是 Next 会相应地填充它。
3.  我使用了两个样式标签，一个是`global`。最好的例子是 css 变量和 body 标签。每个页面/组件都需要访问这些信息，因此有了`global`。

回到`./pages/index.js`我们这样进行:

```
import Layout from '../components/Layout';
export default () => (
    <Layout>
       <p>this is the content </p>
    </Layout>
) 
```

Enter fullscreen mode Exit fullscreen mode

## 处理一些数据。

我们可以将`Index`页面组件转换为 react 组件，并在 react 组件生命周期中获取数据，或者继续使用无状态函数方法。对于本教程，我们将保持简单，不会介绍 react 组件(我们将在下一个教程中介绍)

```
import Layout from '../components/Layout';
import getPhotos from '../data/data.js'
const Index = (props) => (
    <Layout>
       <p>{props.images[0].tagline}</p>
    </Layout>
)
Index.getInitialProps = async ({ }) => {
  // Would fetch data
  return {  images: getPhotos()  } // return { images: [ { }, { } ] }
}

export default Index 
```

Enter fullscreen mode Exit fullscreen mode

`getInitialProps`我们将执行获取操作，并将获取的数据传递给`Index` props，但是为了方便起见，我们在这里对其进行了硬编码。我们在`./data/data.js`有硬编码数据，看起来像这样:

```
export default () => {
    return [
        {
            id: 0,
            tagline: 'You\'re looking at me punk?', 
            image: '927756_283684128492129_838664181_n',
            likes: 2,
            comments: [
                {
                    user: 'rex2018',
                    body: 'Hey this is dope! xxx'
                }
            ]
        },
        ...
    ]
  } 
```

Enter fullscreen mode Exit fullscreen mode

所有这些数据都可以通过 props 获得。我们如何处理`Index`组件中数据是基本的 react 内容。首先让我们编辑`Index`组件

```
import Layout from '../components/Layout';
import getPhotos from '../data/data.js'
import Photo from '../components/Photo';
const Index = (props) => (
    <Layout>
       {
        props.images.map((image, key) => <Photo id={key} id={key} data={image} />)
       }
    </Layout> )
... 
```

Enter fullscreen mode Exit fullscreen mode

我们遍历每个图像并呈现一个`Photo`组件。我们需要在`./components/Photo.js`创建那个组件。

```
import CommentsFunctionality from './InteractiveButtons'
import Link from 'next/link'

export default (props) => {
    return (
        <div className="photoComponent">
            <div style={{flex: '1 0 auto'}}>
                <Link href={{ pathname: '/photo', query: { id: props.id } }}>
                    <img src={`/static/art/${props.data.image}.jpg`} alt=""/>
                </Link>
                <div className="meta">
                    <p className="tagline">{props.data.tagline}</p>
                    <CommentsFunctionality likes={props.data.likes} />
                </div>
            </div>
            <style>{`
                .photoComponent {
                    ...
                }
            `}</style>
        </div>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

概括一下，在`./pages/Index.js`中，我们循环所有的图像。对于每个图像，我们渲染上面的`Photo`组件。新的事情是，我决定将一点代码移到一个单独的`InteractiveButtons`组件中。最初我这样做纯粹是为了保持`Photo`组件的 CSS 干净。总之，这里是`InteractiveButtons`的代码 T7

```
import { MdModeComment, MdFavoriteBorder } from 'react-icons/md'
export default ({likes}) => (
    <div className="meta">
        <a href="/" className="heart"><MdFavoriteBorder />34</a>
        <a href="/"><MdModeComment />{likes}</a>
        <style>{`....`}</style>
    </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

在寻找一种不用下载任何 css 框架就能添加图标的方法时，我发现了`react-icons` npm 模块，我真的很喜欢这个。太好用了。你可以在这里看到[提供的所有图标](https://react-icons.netlify.com/#/)。在上面，我请求材料设计图标中的`MdModeComment`和`MdFavoriteBorder`(因此是`/md`

### 静态文件

在`./components/Photo.js`组件中，我们试图从`static`目录中访问图像文件。我们需要做的就是创建那个目录并添加图像文件，Next.js 会处理剩下的事情。

目前的最终结果是:

[![](img/abb1f986a88178e316c2ff075ee9a560.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0PObBUy---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n2dn0aoi6jt0m12g9pht.png)

## 创建另一个页面

我们现在需要能够点击上面的一张照片，并看到关于它们的更多信息。

在`./components/Photo.js`组件中，我们有这个代码

```
<Link href={{ pathname: '/photo', query: { id: props.id } }}>
    <img src={`/static/art/${props.data.image}.jpg`} alt=""/>
</Link> 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，点击后我们会转到`/photo?id=0`。因此`Link`组件接受一个查询，我们可以在其中添加自己的查询字符串。在这种情况下，我们简单地从道具中获取 id 并传递它。

然后我们需要创建`./pages/photo.js`页面。

```
import Layout from '../components/Layout';
import Photo from '../components/Photo';
import CommentsFunctionality from '../components/InteractiveButtons'
import getPhotos from '../data/data.js' 
const PhotoPage = (props) => (
    <Layout>
        <div className="container">
            <div className="display_image">
                <img src={`/static/art/${props.image.image}.jpg`} alt=''/>
                <CommentsFunctionality />
            </div>
            <div className="comments">
                <p className="tagline">{props.image.tagline}</p>
                {
                    props.image.comments.map((comment, key) => <p key={key}><strong>{comment.user}:</strong>{comment.body}</p>)
                }
                <form className="comment-form" >
                    <input type="text"placeholder="Author" />
                    <input type="text"  placeholder="comment..." />
                    <input type="submit" hidden />
                </form>
            </div>
        </div>
        <style>{` `}</style>
    </Layout> )

PhotoPage.getInitialProps = async ({query}) => {
    // could fetch data here
    let {id} = {...query}
    let image = getPhotos().find(m => m.id == id)
    return { image } 
}

export default PhotoPage 
```

Enter fullscreen mode Exit fullscreen mode

重要的功能在`getInitialProps`里面。在其他信息中，`getInitialProps`可以访问查询字符串，因此我们传递 as `{query}`，从中我们可以获得来自索引页面的 id。然后我们只需找到具有相同 id 的`image`对象，并将其作为组件道具进行传递。

这是我在一个单独的文件中添加数据的唯一原因(在`./data/data.js`)。展示`getInitialProps`如何基于查询字符串获取数据的最基本方式。获取 url 看起来像这样`fetch("http:.../" + id)`

单张照片页面完成:

[![](img/c4064c2c7232d8822a4cff8e587a045f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9bHf5DQ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/irlbts6oerwyguyksoo3.png)

## 结论

很容易看出，这不是处理数据的最佳方法。我们真的需要添加 react 组件，并利用 react 的组件生命周期。尽管我认为即使这样也比不上 redux。

在下一篇教程中，正如我所说的，我们将使用 react 组件，从 API 获取完全相同的数据，并更动态地获得赞和评论。

同时你可以在 [github(分支‘part 1’)](https://github.com/aurelkurtula/basics-of-nextJS/tree/part1)中获得所有这些代码

[![aurelkurtula image](img/e1a1b48b6a401b86d5a91c8729e26a35.png)](/aurelkurtula) [## Next.js 简介——从 API 获取数据

### aurel Kurt ula Aug 24 ' 185 分钟阅读

#nextjs #node #react #tutorial](/aurelkurtula/introduction-to-the-basics-of-nextjs---part-two-pad)