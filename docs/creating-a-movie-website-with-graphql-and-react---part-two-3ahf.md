# 用 GraphQL 和 React 创建一个电影网站-第二部分

> 原文：<https://dev.to/aurelkurtula/creating-a-movie-website-with-graphql-and-react---part-two-3ahf>

在第一部分中，我们创建了 GraphQL API。现在，我们将创建一个使用该 API 的 react 应用程序。

在我们继续之前，仅仅因为我认为它很酷，我们可以使用一个 HTTP 客户端，比如 axios，向我们的 GraphQL 服务器发出请求！看看这个:

```
const query = `{
    newMovies {
      id
    title
    }  
}`
const url = 'http://localhost:4000/graphql?query='+query;

axios.get(url)
  .then(res => console.log(res.data.data.newMovies)) 
```

Enter fullscreen mode Exit fullscreen mode

如果您感兴趣，您可以通过注意使用 graphQL 接口时 url 的变化来查看设置的运行情况——我们在第一部分的[中讨论过](https://dev.to/aurelkurtula/creating-a-movie-website-with-graphql-and-react-25d4)

然而，为了使生产更加容易和愉快，我们可以使用 GraphQL 客户端来代替 HTTP 客户端。

可供选择的客户很少。在本教程中，我将使用[阿波罗客户端](https://www.apollographql.com/docs/react/)。Apollo 也提供了一个 graphQL 服务器，但是我们已经用`express-graphql`创建了它，所以我们没有使用 Apollo 的这一部分，但是 Apollo 客户端，顾名思义，是让我们能够在 react 中编写 GraphQL 的部分。

## 在坚果壳里

如果你想继续下去，你应该从 [github](https://github.com/aurelkurtula/IMDBgraphql/tree/Graphql-api) 中克隆库，检查分支名称`Graphql-api`，因为我们现在将重点放在 react 端，所有代码将被写入`client`目录，这是 react 应用程序代码。

显然这不是初学者教程。如果你不知道 react，但对学习基础知识感兴趣，我已经写了关于它的介绍。

首先安装以下软件包。

```
npm install apollo-boost react-apollo graphql-tag graphql --save 
```

Enter fullscreen mode Exit fullscreen mode

游戏计划是用一个`ApolloProvider`来包装我们的 react 应用程序，然后将 GraphQL 客户端添加到 react props 中。然后通过`graphql-tag`进行 graphQL 查询。

此刻，在`./client/index.js`中，你可以看到这个设置

```
import React from 'react';
import ReactDOM from 'react-dom';
import './style/style.scss';
const App = () => {
  return <div>Hello World2</div> }
ReactDOM.render(
  <App />,
  document.querySelector('#root')
); 
```

Enter fullscreen mode Exit fullscreen mode

第一步，用`ApolloProvider`包装整个应用。提供者还需要一个 GraphQL 客户端来传递反应。

```
import { ApolloProvider, graphql } from 'react-apollo';
...
const client = new ApolloClient({
  uri: "http://localhost:4000/graphql"
});
ReactDOM.render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider> ,
  document.querySelector('#root')
); 
```

Enter fullscreen mode Exit fullscreen mode

如果 GraphQL 服务器没有指向`/graphql`，那么`ApolloClient`需要一个`uri`。所以在我们的例子中，省略它，只使用`new ApolloClient()`就可以了

现在我们可以访问客户端了，我们可以像这样进行查询:

```
import { ApolloProvider, graphql } from 'react-apollo';
import gql from 'graphql-tag';
import ApolloClient from 'apollo-boost';

const AppComponent = (props) => {
  if(props.data.loading) return '<div>loading</div>';
  return <div>{props.data.newMovies[0].title}</div> }
const query = gql`{ newMovies { title } }`;
const App = graphql(query)(AppComponent) 
```

Enter fullscreen mode Exit fullscreen mode

我们用`graphql`包装`AppComponent`，我们也将查询注入到道具中，然后`props.data.newMovies`给我们电影结果。

## 我们开始吧

因为我们正在构建的应用程序比上面显示单个标题的例子要大，所以让我们把它分开。

从`./client/index.js`开始

```
import React from 'react';
import ReactDOM from 'react-dom';
import ApolloClient from 'apollo-boost';
import { ApolloProvider } from 'react-apollo';
import { HashRouter, Switch, Route } from 'react-router-dom'
import NewMovies from './components/NewMovies';
import './style/style.scss';
const client = new ApolloClient();
const Root = () => {
  return (
    <HashRouter >
      <ApolloProvider client={client}>
      <Switch >
        <Route exact path="/" component={NewMovies} />
      </Switch>
      </ApolloProvider>
    </HashRouter>
  )
}
ReactDOM.render(
  <Root />,
  document.querySelector('#root')
); 
```

Enter fullscreen mode Exit fullscreen mode

很简单，几条路线。导入的组件(`NewMovies`)还不存在，但这就是`./client/index.js`中需要的所有代码。

同样，我们将使用的所有组件都将在`Switch`组件中指定。因此，整个应用程序被包装在`ApolloProvider`中，与果壳部分完全相同。

## 获得热门电影

让我们在`./client/components/NewMovies.js`创建一个文件，并从导入所需的包
开始

```
import React, { Component} from 'react'
import gql from 'graphql-tag'
import { graphql } from 'react-apollo' 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将`newMovies` GraphQL 查询结果注入到`NewMovies`组件
中

```
class NewMovies extends Component {
...
}
const query = gql`
{
    newMovies {
        id
        poster_path
        title
    }
}
`
export default graphql(query)(NewMovies); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个设置，一个对象数组被注入到`NewMovies`组件 props 中，并且可以被`this.props.data.newMovies`访问。让我们利用它们:

```
class NewMovies extends Component {
    Movies(){
        return this.props.data.newMovies.map(movie => {
            return (
                <article key={movie.id} className="movie_list">
                    <img src={movie.poster_path} />
                    <h1>{movie.title}</h1>
                </article>
            );
        })
    }
    render() {
        if(this.props.data.loading) return <div>loading</div>
        return this.Movies()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们找到了。需要注意的是

*   react 组件在获取`newMovies`结果之前加载。
*   graphql 为我们提供了一个`loading`属性，当数据被提取时，该属性被设置为`true`，当数据准备好被使用时，该属性被设置为`false`

在我们进入另一个组件之前，让我们用一个锚点来包装电影海报，这样当一个海报被选中时，我们可以获得更多的信息。

为此，我们将使用`react-router-dom`包中的`Link`组件。

```
import { Link } from 'react-router-dom'

class NewMovies extends Component {
    Movies(){
        return this.props.data.newMovies.map(movie => {
            return (
                <article key={movie.id} className="movie_list">
                    <Link to={"/info/"+movie.id}> 
                        <img src={movie.poster_path} />
                    </Link>
    ... 
```

Enter fullscreen mode Exit fullscreen mode

例如，无论何时点击海报，我们都会被引导至`/info/1`。

我们需要返回到`./client/index.js`并添加一个捕捉该路由的路由器。

```
...
import MovieInfo from './components/MovieInfo';
...
const Root = () => {
  return (
    <HashRouter >
      <ApolloProvider client={client}>
      <Switch >
        <Route exact path="/" component={TopMovies} />
        <Route exact path="/info/:id" component={MovieInfo} />
      </Switch>
      ... 
```

Enter fullscreen mode Exit fullscreen mode

当然，这就是 react 路由的强大之处([在](https://dev.to/aurelkurtula/introduction-to-react-router-ha)之前就在这里介绍过)。

## 让我们来研究一下`MovieInfo`组件

从在`./client/components/MovieInfo.js`创建文件开始，然后添加以下内容:

```
import React, { Component } from 'react'
import gql from 'graphql-tag'
import { graphql } from 'react-apollo'
class MovieInfo extends Component {
    render(){
        if(this.props.data.loading) return <div>loading</div>
        return (
            <div>{this.props.data.movieInfo.title}</div>
        )
    }
}
const query = gql`
{movieInfo(id: "284054") {
        title
}}`;

export default graphql(query)(MovieInfo); 
```

Enter fullscreen mode Exit fullscreen mode

它有点工作，对吗？

我们正在查询一个我们硬编码的`id`,这不是我们想要的，相反，我们想要将一个 ID 从我们的 react 组件 props 传递给 graphql 查询。`react-apollo`给了我们一个`Query`组件，使我们能够做到这一点。

```
import { Query, graphql } from 'react-apollo'
class MovieInfo extends Component {
    render(){
        const id = this.props.match.params.id;
        return (
            <Query query={query} variables={{id}} >
            {
                (({loading, err, data}) => {
                    if(loading) return <div>loading</div>
                    return (
                        <div>{data.movieInfo.title}</div>
                    )
                })
            }
            </Query>
        )
    }
}
const query = gql`

query MovieInfo($id: String) {
    movieInfo(id: $id) {
        title
      }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

几乎完全一样的东西，但是用`Query`我们可以传递变量。

现在让我们开发组件的其余部分。在`Query`里面返回下面的代码

```
return(
    <div>
        <header style={{backgroundImage:  'url("https://image.tmdb.org/t/p/w500///'+data.movieInfo.poster_path+'")'}}>
            <h2 className="title">{data.movieInfo.title}</h2>
        </header>
        <article className="wrapper">  
            <p className="description">{data.movieInfo.overview}</p>                
            <div className="sidebar">
                <img src={"https://image.tmdb.org/t/p/w500///"+data.movieInfo.poster_path} className="cover_image" alt="" />
                <ul>
                    <li><strong>Genre:</strong> {data.movieInfo.genres}</li>
                    <li><strong>Released:</strong>{data.movieInfo.release_date}</li>
                    <li><strong>Rated:</strong> {data.movieInfo.vote_average}</li>
                    <li><strong>Runtime:</strong> {data.movieInfo.runtime}</li>
                    <li><strong>Production Companies:</strong> {data.movieInfo.production_companies}</li>
                </ul>
                <div className="videos">
                    <h3>Videos</h3>
                    {/* videos */}
                </div>
                    {/* reviews */} 
            </div>
                {/* credits */}                                         
        </article>
    </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们正在尝试访问尚未请求的查询属性。如果你运行它，当请求失败时，它会给你一个 404 错误。因此，我们需要更新查询来请求比`title`属性更多的内容:

```
query MovieInfo($id: String) {
    movieInfo(id: $id) {
        title
        overview
        poster_path
        genres
        release_date
        vote_average
        runtime
        production_companies
      }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

有了这个更新，有了 git 存储库中即将可用的 css，我们正在处理的部分看起来会像这样:

[![](img/fa68573ed54092cd243caf603f81d1f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JdmI7eTc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y57fdje3yerthywh3pkf.png)

正如你所看到的代码注释，我们需要在页面上添加视频，评论和学分。

## 添加视频

还记得我们在第一部分[中设计 GraphQL 查询的方式吗](https://dev.to/aurelkurtula/creating-a-movie-website-with-graphql-and-react-25d4)让我们能够在`movieInfo`查询中获取视频。我们先这么做:

```
const query = gql`
query MovieInfo($id: String) {
    movieInfo(id: $id) {
        ...
        videos {
            id 
            key
        }
      }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

这些视频以数组的形式出现——有时不止一个。所以处理这些数组的最好方法是在`MovieInfo`组件中创建一个单独的方法，让它返回所有的视频。

```
class MovieInfo extends Component {
    renderVideos(videos){
        return videos.map(video => {
            return (
                <img key={video.id} 
                    onClick={()=> this.videoDisplay(video.key)} 
                    className="video_thumbs" 
                    src={`http://img.youtube.com/vi/${video.key}/0.jpg`}
                />
            )
        })
    }
    render(){
        ...
        {/* videos */}
        {this.renderVideos(data.movieInfo.videos)}
        ... 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在第一篇教程中提到的，`videos`对象中的`key`指的是 youtube 视频 ID。Youtube 让我们能够使用特定格式的截屏图像(在`src`属性中传递)。此外，正如我们之前提到的，我们获取 ID 正是因为我们知道我们需要为`key`提供一些独特的东西 React 所需要的。

当用户点击这些缩略图时，我想在屏幕上加载一个 youtube 视频，因此`onClick={()=> this.videoDisplay(video.key)}`。让我们创建这个功能。

我们要实现的方法是改变状态

```
class MovieInfo extends Component {
    constructor(){
        super();
        this.state={
            video: null
        }
    }
    videoDisplay(video){
        this.setState({
            video
        })
    }
    videoExit(){
        this.setState({
            video: null
        })
    }
    ... 
```

Enter fullscreen mode Exit fullscreen mode

当页面加载`video`状态为`null`时，那么当点击缩略图并触发`videoDisplay`时，`video`状态以 youtube 视频`key`为值。正如我们将看到的，如果触发了`videoExit`方法，则`video`状态将重置回`null`

最后，我们需要一种在状态改变时显示视频的方法，所以让我们创建另一种方法。就在上面的方法下面，加上这个方法:

```
videoToggle(){
    if(this.state.video) return(
        <div className="youtube-video">
            <p onClick={() => this.videoExit()}>close</p>
            <iframe  width="560" height="315" src={`//www.youtube.com/embed/${this.state.video}` } frameborder="0" allowfullscreen />
        </div>
    ) 
} 
```

Enter fullscreen mode Exit fullscreen mode

然后简单地让它呈现在页面的任何地方

```
<div className="videos">
    {this.videoToggle()}
    <h3>Videos</h3>
    {this.renderVideos(data.movieInfo.videos)}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

同样，如果`video`状态是`null`，`{this.videoToggle()}`什么也不做。如果状态不为空——如果`video`有一个键，那么`{this.videoToggle()}`呈现一个视频。

## 添加电影片头和评论

我决定把电影评论和电影演职员表放在它们自己独立的组件中。让我们创建空的组件文件，在`MovieInfo`组件中导入和使用它们，并更新查询。

在`./client/components/MovieInfo.js`内添加这些变化

```
import MovieReviews from './MovieReviews'
import MovieCredits from './MovieCredits'

class MovieInfo extends Component {
...
{/* reviews */}
    <MovieReviews reviews={data.movieInfo.movieReviews} />  
    </div>
        {/* credits */}
        <MovieCredits credits={data.movieInfo.movieCredits} />              </article> }
...

const query = gql`

query MovieInfo($id: String) {
    movieInfo(id: $id) {
        ...
        movieReviews {
            id
            content
            author
        }
        movieCredits{
            id
            character
            name
            profile_path
            order
          }
      }
  }
`;
... 
```

Enter fullscreen mode Exit fullscreen mode

我们从`movieReviews`和`movieCredits`查询中获取数据，并将它们传递给各自的组件。现在我们只是快速显示数据

### 电影演职员表组件

将下面的代码添加到`./client/components/MovieCredits.js`

```
import React, { Component } from 'react'
export class MovieCredits extends Component {
    renderCast(credits){
        return credits.map(cast => {
            return (
                <li key={cast.id}>
                    <img src={`https://image.tmdb.org/t/p/w500//${cast.profile_path}`} />
                    <div className="castWrapper">
                        <div className="castWrapperInfo">
                            <span>{cast.name}</span>
                            <span>{cast.character}</span>
                        </div>
                    </div>
                </li>
            )
        })
    }
  render() {
    return (<ul className="cast">{this.renderCast(this.props.credits)}</ul>)
  }
}
export default MovieCredits 
```

Enter fullscreen mode Exit fullscreen mode

从上面没有什么新的解释

### 电影评论组件

将下面的代码添加到`./client/components/MovieReviews.js`

```
import React, { Component } from 'react'
class MovieReviews extends Component {
    renderReviews(reviews){
        return reviews.map(review => {
            return (
                <article key={review.id}><h4>{review.author} writes</h4>
                    <div>{review.content}</div>
                </article>
            )
        })
    }
    render() {
        return(
            <div className="reviews">
                {this.renderReviews(this.props.reviews)}
            </div>  
        )
    }
} 
export default MovieReviews; 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。这是如何学分，视频和评论会出现。

[![](img/b009e5bbd942e2b6ddc6fbbd5a24f9c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OUYS8V7B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1x9u077agbcja9t9fr82.png)

## 结论

完整的应用程序可以在同一个存储库中找到，您可以在这里查看的[演示。它有三个分支](https://aurelsgraphql.netlify.com/#/) [react-app 分支](https://github.com/aurelkurtula/IMDBgraphql/tree/react-app)和 [master 分支](https://github.com/aurelkurtula/IMDBgraphql)拥有完整的代码，每个教程都建立在彼此之上。其中, [Graphql-api 分支](https://github.com/aurelkurtula/IMDBgraphql/tree/Graphql-api)包含了[第一篇教程](https://dev.to/aurelkurtula/creating-a-movie-website-with-graphql-and-react-25d4)中的代码