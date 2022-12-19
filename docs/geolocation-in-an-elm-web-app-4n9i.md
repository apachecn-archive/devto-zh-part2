# Elm web 应用程序中的地理定位

> 原文：<https://dev.to/elmupdate/geolocation-in-an-elm-web-app-4n9i>

获取 web 应用程序的位置归结为基于 IP 地址或使用浏览器提供的 HTML5 地理定位。

在 IP 地址的情况下，位置信息可以来自 IP 地址的注册地址以及其他来源。

对于浏览器方式，这将取决于您的设备是否有 GPS。如果是，那么它可以使用该系统，否则它也可以查找 IP 地址的第三方数据库。一个例子是谷歌街景调查收集的数据。

使用 Elm 获取 IP 地址来源的位置数据非常简单。首先通过谷歌搜索 IP 地址位置数据找到一个来源。我发现这个列表非常有用:([https://ahmadawais.com/best-api-geolocating-an-ip-address/](https://ahmadawais.com/best-api-geolocating-an-ip-address/))

然后您想要发送一个 Http.get 请求。例如:

设置 get，其中 decodeData 使用原始方法或管道解码器样式，YourData 是反映数据格式的类型别名。

```
getData = 
    Http.get "https://api.ipdata.co/?format=json" decodeData

decodeData =
    decode YourData
        |> required "latitude" Json.Decode.float
        |> required "longitude" Json.Decode.float 
```

使用 Http.send 或 RemoteData.send 方法进行发送。在这种情况下，LoadData 是您定义的用于处理响应的消息`(Result Http.Error YourData)`。

```
sendData =
    Http.send LoadData getData 
```

如果你想使用浏览器内置的 HTML5 解决方案，那么有一个 Elm 模块可以使用——Geolocation . Elm-[http://package . Elm-lang . org/packages/Elm-lang/Geolocation/1 . 0 . 2/Geolocation](http://package.elm-lang.org/packages/elm-lang/geolocation/1.0.2/Geolocation)

在最简单的情况下，您可以从一个更新函数中发出 Cmd 消息，其中 FoundLocation 将是另一个解释结果的更新函数:

```
GetLocation ->  
            ( model, Task.attempt FoundLocation Geolocation.now )

FoundLocation -> 
    case location of
           Err error ->
               ... do a case match on the various error types

           Ok location ->
               ... access location.latitude, location.longitude and any other data you need 
```

请注意，系统会要求用户确认是否可以共享位置信息。

你可能还想让你的应用程序在用户移动时被告知。您可以设置一个 Elm 订阅，当有一个位置类型参数发生变化时，它将调用一个指定的更新函数——例如 MoveLocation。

```
subscriptions : Location -> Sub Msg
subscriptions location =
    Sub.batch
        [ Geolocation.changes MoveLocation ] 
```

您是否需要在模型上存储状态将取决于您的应用程序。如果是这样的话，那么在开始的时候你不知道位置，所以你需要存储位置作为一个可能的字段，并为此合并处理。