# React 一次性应用 2:电影搜索应用

> 原文：<https://dev.to/fleepgeek/react-throwaway-app-2-movie-search-app-3f3d>

在第一篇[文章](https://dev.to/fleepgeek/react-throwaway-app-1-currency-converter--52c7)中，我向您介绍了该系列的目的，您构建了一个货币转换器。在这个示例中，您将构建一个电影搜索应用程序。

## 规则(只是提醒你一下)

*   你的应用应该在 60 分钟内完成(取决于复杂程度)。
*   必须是纯 React(无 react-router 或 redux)。
*   必须在一周后删除该项目。为什么？这些都是基本的应用程序，你应该能够随时创建，不值得在一个严肃的工作面试中展示。
*   不要花太多时间在设计上。记住，这个想法是检查你是否在反应中思考。60 分钟后，你可以根据自己的口味来设计发型。
*   在你完成你的答案之前，不要看我的答案。否则，你将遭受 5 年的“辅导炼狱”

## App 2 -电影搜索 App

*   构建连接到外部 api 的电影应用程序。
*   搜索电影电影并选择一部进行播放。
*   持续时间应在 1 - 2 小时内(包括造型)。

下面是我希望你建立的截图:

[![movie screenshot](img/38844d88c188b763a4ff8724f81c6cca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--APeAYmfn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rycx1mwmt1juinl0lhxj.png)

此应用程序将显示您了解如何:

*   组件和状态工作
*   从 api 请求数据
*   组件生命周期方法
*   使用事件
*   根据状态变化更新用户界面

你的时间从现在开始！记住在你完成你的之前不要看我的答案。

## 我的解决方案

我使用了 [OMDb API](http://www.omdbapi.com/) 来获取我的电影数据。你必须获得一个 api 密匙(它是免费的)。我必须承认，我花了 60 多分钟来完成这个，因为我必须通过在 PostMan 上处理不同的请求来熟悉 api。和往常一样，我使用 create-react-app 来生成我的项目。

为了构建我的应用程序，我必须决定什么是容器和组件。

[![app structure](img/cddd8aa6aaa84d9998b9f55dbd2b9ad3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4z9GlZQk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nji5hcb8ujfrmt99p6mo.png)

下面是我的文件夹结构:
[![folder struture](img/572f3ecb38d19632a9d8a18ca0abc1c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7X82Ms0e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/krwwqa72ph2fp1a7vjap.JPG)

**MovieCard.js:**

该组件用于显示选定的电影。它通过道具接收电影数据。

```
import React from 'react';

import './MovieCard.css';

const MovieCard = (props) => {
    return (
        <div className="container">
            <div className="movie-card">
                <div className="movie-header" style={{ backgroundImage: `url(${props.movie.Poster})` }}>
                </div>
                <div className="movie-content">
                    <div className="movie-content-header">
                        <h3 className="movie-title">{props.movie.Title}</h3>
                    </div>
                    <div className="movie-info">
                        <div className="info-section">
                            <label>Released</label>
                            <span>{props.movie.Released}</span>
                        </div>
                        <div className="info-section">
                            <label>IMDB Rating</label>
                            <span>{props.movie.imdbRating}</span>
                        </div>
                        <div className="info-section">
                            <label>Rated</label>
                            <span>{props.movie.Rated}</span>
                        </div>
                        <div className="info-section">
                            <label>Runtime</label>
                            <span>{props.movie.Runtime}</span>
                        </div>
                    </div>
                    <div className="plot" style={{fontSize: '12px'}}>
                        <p>{props.movie.Plot}</p>
                    </div>
                </div>
            </div>
        </div>
    );
};

export default MovieCard; 
```

Enter fullscreen mode Exit fullscreen mode

**MovieCard.css:**

```
 .container {
    display: flex;
    flex-wrap: wrap;
    max-width: 100%;
    margin-left: auto;
    margin-right: auto;
    justify-content: center;
}

.movie-card {
    background: #ffffff;
    box-shadow: 0px 6px 18px rgba(0,0,0,.1);
    width: 100%;
    max-width: 290px;
    margin: 2em;
    border-radius: 10px;
    display:inline-block;
    z-index: 10;
}

.movie-header {
    padding:0;
    margin: 0;
    height: 434px;
    width: 100%;
    display: block;
    border-top-left-radius: 10px;
    border-top-right-radius:10px;
    background-size: cover;
}

.movie-content {
    padding: 18px 18px 24px 18px;
    margin: 0;
}

.movie-content-header, .movie-info {
    display: table;
    width: 100%;
}

.movie-title {
    font-size: 24px;
    margin: 0;
    display: table-cell;
    cursor: pointer;
}

.movie-title:hover {
    color:rgb(228, 194, 42);
}

.movie-info {
    margin-top: 1em;
}

.info-section {
    display: table-cell;
    text-transform: uppercase;
    text-align:center;
}

.info-section:first-of-type {
    text-align:left;
}

.info-section:last-of-type {
    text-align:right;
}

.info-section label {
    display: block;
    color: rgba(0,0,0,.5);
    margin-bottom: .5em;
    font-size: 9px;
}

.info-section span {
    font-weight: 700;
    font-size: 11px;
}

@media only screen and (max-width: 400px) {
    .movie-header {
        height: 400px;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**Search.js**

接下来，我们有搜索组件，它包含搜索输入和返回的结果列表。
下面是 Search.js:

```
import React from 'react';

import './Search.css';

const Search = (props) => {
    let resultList = null

    if (props.searching && (props.defaultTitle !== '')) {
        resultList = (
            <ul className="results">
                {props.results.map(item => (
                    <li key={item.imdbID} onClick={() => props.clicked(item)}>
                        <img src={item.Poster} alt="Movie Poster"/>
                        {item.Title}
                    </li>
                ))}
            </ul>
        )
    }

    return (
        <div className="search">
            <input type="search" name="movie-search" value={props.defaultTitle} onChange={props.search} />
            {resultList}
        </div>
    );
};

export default Search; 
```

Enter fullscreen mode Exit fullscreen mode

**Search.css**

```
.search {
    position: relative;
    margin: 0 auto;
    width: 300px;
    margin-top: 10px;
}

.search input {
    height: 26px;
    width: 100%;
    padding: 0 12px 0 25px;
    background: white;
    border: 1px solid #babdcc;
    border-radius: 13px;
    box-sizing: border-box;
    box-shadow: inset 0 1px #e5e7ed, 0 1px 0 #fcfcfc;
}

.search input:focus {
    outline: none;
    border-color: #66b1ee;
    box-shadow: 0 0 2px rgba(85, 168, 236, 0.9);
}

.search .results {
    display: block;
    position: absolute;
    top: 35px;
    left: 0;
    right: 0;
    z-index: 20;
    padding: 0;
    margin: 0;
    border-width: 1px;
    border-style: solid;
    border-color: #cbcfe2 #c8cee7 #c4c7d7;
    border-radius: 3px;
    background-color: #fdfdfd;
}

.search .results li { 
    display: flex;
    align-items: center;
    padding: 5px;
    border-bottom: 1px solid rgba(88, 85, 85, 0.3);
    text-align: left;
    height: 50px;
    cursor: pointer;
}

.search .results li img { 
    width: 30px;
    margin-right: 5px;
}

.search .results li:hover { 
    background: rgba(88, 85, 85, 0.1);
} 
```

Enter fullscreen mode Exit fullscreen mode

**MovieSearch.js**

我让 MovieSearch 成为一个有状态的组件，因为我想在那里管理我的所有状态，并通过 props 将数据传递给其他组件。首先，确保从 omdb api 获得 api 密钥。
这是我的 MovieSearch.js 容器:

```
import React, { Component } from 'react';
import axios from 'axios';

import MovieCard from '../../components/MovieCard/MovieCard';
import Search from '../../components/Search/Search';

class MovieSearch extends Component {
    state = {
        movieId: 'tt1442449', // default imdb id (Spartacus)
        title: '',
        movie: {},
        searchResults: [],
        isSearching: false,
    }

    componentDidMount() {
        this.loadMovie()
    }

    componentDidUpdate(prevProps, prevState) {
        if (prevState.movieId !== this.state.movieId) {
            this.loadMovie()
        }
    }

    loadMovie() {
        axios.get(`http://www.omdbapi.com/?apikey=YOUR_API_KEY&i=${this.state.movieId}`)
            .then(response => {
                this.setState({ movie: response.data });
            })
            .catch(error => {
                console.log('Opps!', error.message);
            })
    }

    // we use a timeout to prevent the api request to fire immediately as we type
    timeout = null;

    searchMovie = (event) => {
        this.setState({ title: event.target.value, isSearching: true })

        clearTimeout(this.timeout);

        this.timeout = setTimeout(() => {
            axios.get(`http://www.omdbapi.com/?apikey=YOUR_API_KEY&s=${this.state.title}`)
                .then(response => {

                    if (response.data.Search) {
                        const movies = response.data.Search.slice(0, 5);
                        this.setState({ searchResults: movies });
                    }
                })
                .catch(error => {
                    console.log('Opps!', error.message);
                })
        }, 1000)

    }

    // event handler for a search result item that is clicked
    itemClicked = (item) => {
        this.setState(
            {
                movieId: item.imdbID,
                isSearching: false,
                title: item.Title,
            }
        )
    }

    render() {
        return (
            <div onClick={() => this.setState({ isSearching: false })}>
                <Search
                    defaultTitle={this.state.title}
                    search={this.searchMovie}
                    results={this.state.searchResults}
                    clicked={this.itemClicked}
                    searching={this.state.isSearching} />

                <MovieCard movie={this.state.movie} />
            </div>
        );
    }
}

export default MovieSearch; 
```

Enter fullscreen mode Exit fullscreen mode

该容器用于处理应用程序中的状态和更新更改。上面的代码只是在挂载时加载一个初始电影。每当我们搜索并更新 movieId 状态时，它就会通过 props 更新 MovieCard 的内容。

## 结论

你可能会认为这有点仓促。请记住，这不是一个教程，而是一个初学者的挑战，他们觉得自己可以在反应中思考。我的代码只是一个指南。感谢你的阅读，我希望在下一部分看到你。

我不认为我会扔掉这个。)

链接到第 1 部分:[此处](https://dev.to/fleepgeek/react-throwaway-app-1-currency-converter--52c7)