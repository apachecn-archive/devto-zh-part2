# 使用 F#查询 Last.fm web API

> 原文：<https://dev.to/bohdanstupak1/querying-lastfm-web-api-with-f-5h9b>

本文最初发表于 [Codeproject](https://www.codeproject.com/Tips/1213993/Querying-Last-fm-web-API-with-Fsharp)

## 简介

让我们想象一下，你有一种前卫的音乐品味，所以你想向你的朋友推荐那些最主流的艺术家。如果你在 [last.fm](https://www.last.fm/home) 上有一个配置文件，那么你可以编写一个小工具来查询和处理你的听力统计数据，以自动完成这项任务。

我做这项工作的工具是 F#编程语言，我将向您展示它的一些好处，如类型提供程序或与对象相比单元测试函数的简易性。

完整的源代码可以在这里访问[。](https://github.com/Wkalmar/LastFmArtistGetter)

## 工作流程

任务如下:

1.  从 last.fm 排行榜中获得前 50 名艺术家。
2.  将它们转换成它们名字的集合。
3.  去掉那些已经被推荐的。
4.  替换艺术家姓名中的非 url 字符以进行进一步的 API 调用。
5.  对于每个艺术家，进行额外的 API 调用以获得一定数量的听众。
6.  将给定的信息转换成一个更简单的数据类型，只包含艺术家姓名和听众人数。
7.  根据听众数量对艺术家进行排序，这意味着听众越多的艺术家越主流。请注意 F# pipeline 是如何以一种真正地道的方式表达这个工作流的。

```
let result = getTopArtists
                |> getTopArtistNames
                |> removeAlreadyRecomendedArtists
                |> getUrlEncodedArtistNames 
                |> mapArtistNamesToArtistInfo getArtistInfo
                |> getArtistsShortInfo
                |> orderArtistsByListenersCount 
```

Enter fullscreen mode Exit fullscreen mode

## 利用类型提供者

[类型提供者](https://docs.microsoft.com/en-us/dotnet/fsharp/tutorials/type-providers/)可以说是 F#最受关注的特性。类型提供者允许我们访问许多上下文，如 Web APIs、数据库模式等。作为强类型实体，这允许我们获得编译器时间支持和一些 IDE 自动完成的好处。

为了在我们的应用中使用它，我们:

Import `FSharp.Data`
声明我们的 API 响应的一个片段:

```
let [<Literal>] TopArtistsSample = """{  
   "topartists":{  
      "artist":[  
         {  
            "name":"Porcupine Tree",
            //skipped for the sake of breivety
         }
      ],
      "@attr":{  
         "user":"Morbid_soul",
         "page":"1",
         "perPage":"2",
         "totalPages":"165",
         "total":"330"
      }
   }
    }""" 
```

Enter fullscreen mode Exit fullscreen mode

通过`JsonProvider` :
从我们的样本构造类型

```
type TopArtists = JsonProvider<TopArtistsSample> 
```

Enter fullscreen mode Exit fullscreen mode

享受强类型响应的编译时支持。

## 利用高阶函数提高单元测试

让我们仔细看看下面的函数:

```
let mapArtistNamesToArtistInfo getArtistInfoFn artists = 
    artists
        |> Array.map (fun i -> getArtistInfoFn i) 
```

Enter fullscreen mode Exit fullscreen mode

`getArtistInfoFn`响应与远程 web API 的交互。下面是这种场景的单元测试是如何执行的。

```
let getArtistInfoStub input = 
        match input with
        | "Nokturanl Mortum" -> 1
        | "Krobak" -> 2
        | _ -> 3

[<Fact>]
let mapArtistNamesToArtistInfo_returns_expected_result() =
    let result = mapArtistNamesToArtistInfo getArtistInfoStub [| "Nokturanl Mortum"; "Heinali"; "Krobak"|]
    Assert.Equal(result.[0], 1)
    Assert.Equal(result.[1], 3)
    Assert.Equal(result.[2], 2) 
```

Enter fullscreen mode Exit fullscreen mode

这比典型的可测试的 OO 解决方案要优雅得多，后者需要引入一个接口，将其注入调用程序类，并在测试项目中引入一些重量级的模仿库。有人可能会说，将一个不纯的函数注入纯函数并不是真正的函数方式，但是 F#是一种相当宽容的语言，它允许我们不用一些聪明的概念，比如自由单子等等。

## 错误处理

细心的读者可能已经注意到，我们依赖 web API 完美地工作，这并不是健壮编程的标志。为了正确处理，我们将采用[铁路导向规划](https://fsharpforfunandprofit.com/rop/)的概念。
主要思想是将成功和不成功的函数执行编码到返回类型中，这样管道中的所有函数将使用一些有用的业务逻辑来处理成功的结果，而不成功的结果将被排除在进一步的执行之外。

但是我强烈建议你不要相信我的话，而是去读一下原始文章，它对这个概念的解释要详细得多。

配方如下:

```
type Result<'TSuccess,'TFailure> = 
    | Success of 'TSuccess
    | Failure of 'TFailure

let switch switchFunction1 switchFunction2 input = 
    match switchFunction1 input with
    | Success s -> switchFunction2 s 
    | Failure f -> Failure f

let (>=>) switchFunction1 switchFunction2 input = 
    switch switchFunction1 switchFunction2 input 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以将返回值包装成所提供的类型:

```
let getTopArtists () = 
    try
        let path = String.Format(getTopArtistsPattern, baseUrl, userName, apiKey)
        let data = Http.Request(path)
        match data.Body with
        | Text text -> Success(TopArtists.Parse(text).Topartists.Artist)
        | _ -> Failure "getTopArtists. Unexpected format of reponse message"
    with
    | ex -> Failure ex.Message 
```

Enter fullscreen mode Exit fullscreen mode

这样，管道将转换为:

```
let pipeline = 
    getTopArtists
        >=> getTopArtistNames
        >=> removeAlreadyRecomendedArtists
        >=> getUrlEncodedArtistNames 
        >=> mapArtistNamesToArtistInfo getArtistInfo
        >=> getArtistsShortInfo
        >=> orderArtistsByListenersCount 
```

Enter fullscreen mode Exit fullscreen mode

让我们也看一下单元测试片段，对调用者如何处理函数输出有一个总体的感觉:

```
[<Fact>]
    let orderArtistsByListenersCount_returns_expected_result() =
        let Satie = {name = "Erik Satie"; listeners = 750000}
        let Chopin = {name ="Frederic Chopin"; listeners = 1200000}
        let Barber = {name = "Samuel Barber"; listeners = 371000}
        let artists = [|Satie; Chopin; Barber|]
        let result = orderArtistsByListenersCount artists
        match result with
        | Success s -> 
            Assert.Equal(s.[0], Chopin)
            Assert.Equal(s.[1], Satie)
            Assert.Equal(s.[2], Barber)
        | Failure _ -> Assert.True(false) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我希望那些今天第一次接触 F#的人不会觉得这种不常见的语法太复杂，以至于无法体会到语言的好处，如类型提供者或由于函数组合而带来的简单单元测试。我也希望那些已经有扎实基础的人会发现面向铁路的编程技术非常有用。