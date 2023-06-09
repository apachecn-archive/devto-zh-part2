# 如何在 RSpec 中停止 API 调用并返回自己的数据

> 原文：<https://dev.to/husteadrobert/how-to-stop-api-calls-in-rspec-and-return-your-own-data-4fa2>

TL；dr 您可以使用 Webmock 和 stubbing 请求从外部 API 请求中显式返回您想要的任何数据。

如何测试一个向另一个网站发出 API 请求的控制器，但是您希望控制返回的确切内容？例如，您想测试一个向 Twitter 查询 tweet 的控制器，但是您不想受当时正在创建的任何 tweet 的支配，因为您想测试您能多好地抓取某些关键字。

这就是 Webmock 的用武之地。[https://github.com/bblimke/webmock](https://github.com/bblimke/webmock)

通过将下面的代码添加到 spec_helper 或 rails_helper 文件中，您可以存根外部 API 请求并控制返回到控制器的数据。

```
config.before(:each) do
 stub_request(:get, /some.website.api/).
 with(headers: {‘Accept’: ‘*/*’, ‘User-Agent’: ‘Ruby’ }).
 to_return(status: 200, body:’{“test”: 0}’, headers: {})
end 
```

让我们看一下每一行:

这样做告诉 RSpec 在每个规范之前运行下面的代码。

这是我们从 Webmock 获得的 stub_request 方法的第一部分。它指定了请求的类型(:get - > GET)，但是您也可以使用任何其他 RESTful 请求，包括符号:any 来存根任何进来的请求。第二部分是 URI。你可以像上面一样使用 reqex，或者像“[www.example.com](http://www.example.com)”这样的字符串。基本上，这个 stub_request 现在正在等待向与 reqex /some.website.api/匹配的 URI 发出 GET 请求

`with(header: {'Accept': '*/*', 'User-Agent': 'Ruby' }).`在第二部分，您可以显式设置想要匹配的头信息。如果您在代码中向/some.website.api/发出 GET 请求，但“用户代理”不是“Ruby ”,则不会调用这个 stub_request。

最后，当我们的代码调用位于/some.website.api/的 API 时，我们到达指定我们想要什么响应的行。特别是，我们可以设置状态代码(这里是 200，表示 OK)。身体，不出所料，就是返回给我们的数据。In 可以是以下类型之一:[Proc，IO，Pathname，String，Array]。您还可以显式设置响应的标头。

总结一下:通过使用 Webmock gem 和 RSpec helper 文件中的上述代码，您可以停止代码发出的 API 请求，并返回您想要的任何数据。

更多信息请点击:[https://robots . thoughtbot . com/how-to-stub-external-services-in-tests](https://robots.thoughtbot.com/how-to-stub-external-services-in-tests)
Webmock Gem:[https://github.com/bblimke/webmock](https://github.com/bblimke/webmock)

编码快乐！