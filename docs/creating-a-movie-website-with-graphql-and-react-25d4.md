# 用 GraphQL 和 React 创建一个电影网站-第一部分

> 原文：<https://dev.to/aurelkurtula/creating-a-movie-website-with-graphql-and-react-25d4>

正如我在之前的教程中解释的那样，GraphQL 是创建 API 的另一种方式。使用 GraphQL 而不是 JSON 或 XML API，您可以让 API 的消费者能够查询他们需要的数据，而不是不管他们的需求而给他们一切。

在接下来的一系列教程中，我将介绍如何通过从现有的 JSON APIs 中收集数据来创建 GraphQL API，以及如何使用 react 来使用新的 GraphQL API。

## 关于 JSON API

我们将使用[moviedb](https://www.themoviedb.org/documentation/api)“可供所有人使用”。这让我们可以接触到他们的电影收藏。因此，我们将通过他们的 JSON API 访问他们的电影数据库，而不是创建自己的电影数据库。你所需要做的就是用它们创建一个帐户，这样你就可以得到你的 API 密匙——这是你学习这一系列教程所需要的。

为了创建我们的应用程序，这些是我们需要发出的所有请求(`https://api.themoviedb.org/3/movie/`是所有请求的根):

```
/${id}/videos?api_key=${YOUR_API}&language=en-US
/now_playing?api_key=${YOUR_API}&language=en-US&page=1
/${id}?api_key=${YOUR_API}&language=en-US&page=1
/${id}/reviews?api_key=${YOUR_API}&language=en-US&page=1
/${id}/credits?api_key=${YOUR_API}&language=en-US&page=1 
```

Enter fullscreen mode Exit fullscreen mode

可以想象，我们可以忽略 GraphQL，直接创建 react 应用程序，让它在需要时发出五个 get 请求。但是，我们将使用 GraphQL 来处理上述问题。因此，react 开发人员(即使我们两个都做)可以向 GraphQL 服务器发出一个请求，然后选择任何需要的内容。

同样，如果你想继续做下去，就去 [themoviedb](https://www.themoviedb.org/documentation/api) 并创建你很快就会需要的 API 密匙。此外，欢迎您使用我的[最小节点环境](https://github.com/aurelkurtula/minimal-node-environment)设置，它没有任何与我们在这里需要学习的内容相关的代码，它只是设置环境。你可以在这里阅读[如何创造环境](https://dev.to/aurelkurtula/setting-up-a-minimal-node-environment-with-webpack-and-babel--1j04)。

### 第一步:连接 GraphQL 到 Express

以下内容将在[第一个 graphql 教程](https://dev.to/aurelkurtula/exploring-graphql-api-serve-3abf)中介绍

我们需要获得 express 才能在特定路径使用 GraphQL。在`./server.js`的完整代码应该是这样的

```
import express from 'express'; 
import dotenv from 'dotenv';
//...
import expressGraphQL from 'express-graphql';
import schema from './schema/schema'; // our schema file
dotenv.config()
const app = express();
app.use(webpackMiddleware(webpack(webpackConfig)));

app.use('/graphql', expressGraphQL({
  schema,
  graphiql: true
}))

app.listen(4000, () => {
  console.log('Listening');
}); 
```

Enter fullscreen mode Exit fullscreen mode

从这一点开始，我们要编码的所有内容都将包含在`./schema/schema.js`中，所以创建这个文件。

最后，第一步在根目录下创建一个`.env`文件，然后像这样添加你的电影 API

```
API=api-string-without-quotes 
```

Enter fullscreen mode Exit fullscreen mode

剩下的就是安装我们在上面
要求的包

```
npm i -S express express-graphql graphql dotenv 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:为新电影创建 API 端点

在内部，我们能够从其他来源获取数据，无论是数据库还是外部 API，并使用它们来构建我们自己的 GraphQL API。在这里，我们将从五个不同的 JSON API 端点获取数据，并以一种其他人可以使用它的方式来设计 GraphQL，就好像数据来自一个调用，或者更确切地说，不关心原始数据来自哪里。

我们将从从**电影数据库**中提取关于目前正在
电影院上映的电影的数据开始

```
https://api.themoviedb.org/3/movie/now_playing?api_key=${YOUR_API}&language=en-US&page=1 
```

Enter fullscreen mode Exit fullscreen mode

如果你提供了自己的 API 并导航到上面的 URL，你会看到每部电影的格式如下:

```
{
    results: [
            {
            vote_count: 3742,
            id: 284054,
            video: false,
            vote_average: 7.3,
            title: "Black Panther",
            popularity: 246.001551,
            poster_path: "/uxzzxijgPIY7slzFvMotPv8wjKA.jpg",
            original_language: "en",
            original_title: "Black Panther",
            genre_ids: [28, 12, 14, 878],
            backdrop_path: "/b6ZJZHUdMEFECvGiDpJjlfUWela.jpg",
            adult: false,
            overview: "King T'Challa returns ....",
            release_date: "2018-02-13"
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

作为 GraphQL API 的架构师，我们决定哪些信息是有用的，哪些是无用的。我们还可以重命名任何键名。

让我们继续研究`./schema/schema.js`

上面的 JSON 结果帮助我们进入 GraphQL 推理。

1.  我们知道每部电影都是一件物品
2.  我们需要一个 ID，这是一个整数
3.  我们还会用到`poster_path`和`title`，它们都是字符串
4.  我们对其他任何事情都不感兴趣

让我们告诉 GraphQL，一部新上映的电影就应该是这样的。

```
import {
    GraphQLObjectType,
    GraphQLString,
    GraphQLInt,
    GraphQLSchema, // we'll use them for the RootQuery
    GraphQLList // we'll use them for the RootQuery
} from 'graphql';

const NewMoviesType = new GraphQLObjectType({
    name: 'NewMovies',
    fields:{
        id: {type: GraphQLInt},
        poster_path: {type: GraphQLString},
        title: {type: GraphQLString},

    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

可以想象，这只是完整代码的一半。那只是说我们需要一个有一个整数和两个字符串的对象。

最后，我们需要向它提供根查询中的数据。正如我在介绍 GraphQL 的教程中所说的，“根查询是 GraphQL 的入口点，它首先触发，然后暴露其他资源”。

```
import axios from 'axios';
...
const RootQuery = new GraphQLObjectType({
    name: 'RootQueryType',
    fields:{
        newMovies: {
            type: new GraphQLList(NewMoviesType),
            resolve() {
              return axios.get(`https://api.themoviedb.org/3/movie/now_playing?api_key=${process.env.API}&language=en-US&page=1`)
              .then(res => {
                  const movies = res.data.results;
                  movies.map(movie=> movie.poster_path = "https://image.tmdb.org/t/p/w500"+movie.poster_path
                 )
                  return movies
              })
            }
          }          
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

正如我们将看到的，在`RootQuery`中，我们将拥有 GraphQL API 将要提供的一切。到目前为止，根查询有一个名为`newMovies`的字段。该字段将成为一个对象，我们已经创建了它的蓝图。实际上，它不会是一个对象，而是一个电影对象的数组，因此有了`GraphQLList(NewMoviesType)`

最后，resolve 方法向我们的对象提供数据。

我们使用 axios 从外部 API 获取数据(同样，如果数据存储在数据库中，它将在 resolve 方法中检索)。

因为我们从中获取的电影 API 是 json，所以它工作得非常好，简单地返回结果就可以了:

```
resolve() {
    return axios.get(`https://api.themoviedb.org/3/movie/now_playing?api_key=${process.env.API}&language=en-US&page=1`)
    .then(res => res.data.results )
} 
```

Enter fullscreen mode Exit fullscreen mode

但是当然我们需要修改`poster_path`的值，因为相对路径没有任何用处。

最后，我们导出根查询并运行项目(`npm start` )

```
export default new GraphQLSchema({
    query: RootQuery
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你一直跟随，导航到`http://localhost:4000/graphql`并添加下面的查询，你应该已经得到所有最新的电影。

```
{  newMovies{  id  title  poster_path  }  } 
```

Enter fullscreen mode Exit fullscreen mode

一个搞定了，还有四个！

### 第三步:获取单部电影的信息

想想看，上面的查询给了我们所有的新电影。接下来，人们可能想了解这些电影的更多信息。

现有的 API 端点是

```
https://api.themoviedb.org/3/movie/${id}?api_key=${YOUR_API}&language=en-US 
```

Enter fullscreen mode Exit fullscreen mode

结果是一个包含特定电影所有信息的对象。让我们像以前一样创建 GraphQL 对象。

```
const MovieInfoType = new GraphQLObjectType({
    name: 'MovieInfo',
    fields: {
        id: {type: GraphQLInit},
        overview: {type: GraphQLString},
        title: {type: GraphQLString},
        poster_path: {type: GraphQLString},
        genres: {type: GraphQLString},
        release_date: {type: GraphQLString},
        vote_average: {type: GraphQLString},
        production_companies: {type: GraphQLString},
        vote_average: {type: GraphQLString},
        runtime: {type: GraphQLString}
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，一旦你明白了，一切都是重复的。

然后我们需要将它添加到根查询中。

```
const RootQuery = new GraphQLObjectType({
    name: 'RootQueryType',
    fields:{
        topMovies: {...},
        movieInfo: {
            type: MovieInfoType,
            args: {id: {type: GraphQLString}},
            resolve(parentValue, args) {
                return axios.get(`https://api.themoviedb.org/3/movie/${args.id}?api_key=${process.env.API}&language=en-US&page=1`)
                .then(res => {
                    const movie = res.data;
                    movie.genres = movie.genres.map(g => g.name).join(', ')
                    movie.production_companies = movie.production_companies.map(c => c.name).join(', ')
                    movie.runtime+= ' min.'
                    return movie
                })
            }
        }
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这比`topMovies`查询复杂得多，但不难理解。

首先，我们指定对于对`movieInfo`的查询，`id`应该作为字符串提供。那当然是因为原来的 API 需要。请注意，我们没有使用`parentValue`，而是将所需的`id` get 添加到了`args`对象中。

在最初的 API 中，`genres`和`production_companies`被表示为数组:

```
genres:  [  {  id:  18,  name:  "Drama"  },  {  id:  10751,  name:  "Family"  }  ],  production_companies:  [  {  id:  9195,  logo_path:  "/ou5BUbtulr6tIt699q6xJiEQTR9.png",  name:  "Touchstone Pictures",  origin_country:  ""  }  ], 
```

Enter fullscreen mode Exit fullscreen mode

我们创建的 GraphQL 对象要求流派和制作公司都是字符串(并不是说我们不能将它们指定为数组)。因此只加入他们的名字。

类似地，电影长度以分钟为单位，所以我决定将`min.`追加到原始字符串中——可以说这不是一个好主意。当我们创建界面时，我们可能希望将这些分钟转换成人类可读的时间，因此通过添加`min.`我们给 react 开发人员带来了更多的工作，但这真的很有趣！

### 第四步:获取电影预告片

我会告诉你，这个[themoviedb.org API](https://www.themoviedb.org/documentation/api)棒极了。除了其他我们在这里不会涉及的东西，它也给了我们电影预告片。可以在
找到它们

```
https://api.themoviedb.org/3/movie/${id}/videos?api_key=${YOUR_API}&language=en-US 
```

Enter fullscreen mode Exit fullscreen mode

结果是对一组 youtube 视频的引用

```
{  id:  43535,  results:  [  {  id:  "533ec6a1c3a3685448004f82",  iso_639_1:  "en",  iso_3166_1:  "US",  key:  "e7bD5BNqfwY",  name:  "A Simple Twist of Fate - Hallmark Channel",  site:  "YouTube",  size:  360,  type:  "Trailer"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

我不得不说，当我看到我有点兴奋过头了！无论如何，让我们创建视频 GraphQL 对象。我们知道这里唯一有用的信息是`key`，但是为了让开发 react 应用变得简单，我们知道`id`很方便，因此:

```
const VideoType = new GraphQLObjectType({
    name: 'Video',
    fields:{
        id: {type: GraphQLString},
        key: {type: GraphQLString}

    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你还没有看到 GraphQL 的亮度，请做好被蒙蔽的准备，因为这是我认为 GraphQL 更亮的地方！

如果我们遵循目前使用的模式，`movieVideo`将是根中的另一个查询。但事实上，我们知道，或者说我们将要决定，使用我们的 GraphQL API 的开发者在请求一部电影时会想要预告片。

所以我们希望可以从`movieInfo`查询中访问预告片，因此`VideoType`数组将成为`MovieInfoType`对象的一部分！

```
const MovieInfoType = new GraphQLObjectType({
    name: 'MovieInfo',
    fields: {
        id: {type: GraphQLString},
        ...
         videos: {
             type: new GraphQLList(VideoType),
             args: {id: { type: GraphQLString } },
             resolve(parentValue, args) {
                return axios.get(`https://api.themoviedb.org/3/movie/${parentValue.id}/videos?api_key=${process.env.API}&language=en-US`)
                .then(res => res.data.results)
             }
         }
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们不对`RootQuery`做任何事情，而是在`MovieInfoType`中添加一个`videos`字段，并在那里对其进行解析。获取特定视频所需的电影`id`是从`video`的兄弟姐妹中检索的，因此`fields.id`被称为`parentValue.id`。

如果我们导航到`http://localhost:4000/graphq`并添加以下查询:

```
{  movieInfo(id:"284054"){  title  videos{  id  key  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

你会得到一堆黑豹的视频。

现在，这很酷！(GraphQL 和电影)

## 第五步:学分和点评

最后，让我们将电影评论和电影演员表添加到 GraphQL API 中。

```
const MovieCreditsType = new GraphQLObjectType({
    name: 'MovieCredits',
    fields:{
        id: {type: GraphQLString},
        character: {type: GraphQLString},
        name: {type: GraphQLString},
        profile_path: {type: GraphQLString},
        order: {type: GraphQLString}
    }
})
const MovieReviewsType = new GraphQLObjectType({
    name: 'MovieReviews',
    fields:{
        id: {type: GraphQLString},
        content: {type: GraphQLString},
        author: {type: GraphQLString},
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

与视频预告片类似，电影演职员表和电影评论仅在与其相关的电影上下文中有意义。所以我们将它们添加到`MovieInfoType`对象中。

```
const MovieInfoType = new GraphQLObjectType({
    name: 'MovieInfo',
    fields: {
        id: {type: GraphQLString},
        ...
         videos: { ... },
         movieReviews: {
            type: new GraphQLList(MovieReviewsType),
            args: {id: {type: GraphQLString}},
            resolve(parentValue, args) {
              return axios.get(`https://api.themoviedb.org/3/movie/${parentValue.id}/reviews?api_key=${process.env.API}&language=en-US&page=1`)
              .then(res =>  res.data.results)
            }
          },
          movieCredits: {
            type: new GraphQLList(MovieCreditsType),
            args: {id: {type: GraphQLString}},
            resolve(parentValue, args) {
              return axios.get(`https://api.themoviedb.org/3/movie/${parentValue.id}/credits?api_key=${process.env.API}&language=en-US&page=1`)
              .then(res =>  res.data.cast.filter(cast => cast.profile_path ) )
            }
          }
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的话

正如您所收集的，仅仅因为我们使用了，比如说`MovieInfoType`对象中的`VideoType`，我们就可以很容易地复制代码并将其添加为子`RootQuery` `fields`

```
const MovieInfoType = new GraphQLObjectType({
    name: 'MovieInfo',
    fields: {
        id: {type: GraphQLString},
        ...
         videos: { ... },
         ...
    }
})
const RootQuery = new GraphQLObjectType({
    name: 'RootQueryType',
    fields:{
        videos: {
            type: new GraphQLList(VideoType),
            args: {id: { type: GraphQLString } },
            resolve(parentValue, args) {
               return axios.get(`https://api.themoviedb.org/3/movie/${args.id}/videos?api_key=${process.env.API}&language=en-US`)
               .then(res => res.data.results)
            }
        },
        newMovies: { ... } ,
        movieInfo: { ... }         
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

因此，可以通过两种方式查询视频

```
{  videos(id:"284054")  {  id  key  }  } 
```

Enter fullscreen mode Exit fullscreen mode

还是通过电影

```
{  movieInfo(id:"284054"){  videos  {  id  key  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

就这些了。这构成了我们的 GraphQL API。完整的代码在 [github](https://github.com/aurelkurtula/IMDBgraphql/tree/Graphql-api) 上，分支`Graphql-api`保存着这里探索的代码，其中主分支会根据下一个教程进行更改——我们将在 react 应用中使用这个新的 API。

**你可以在这里玩现场试玩**