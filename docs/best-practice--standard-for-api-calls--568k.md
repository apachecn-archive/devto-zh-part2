# API 调用的最佳实践/标准

> 原文：<https://dev.to/rsummerell/best-practice--standard-for-api-calls--568k>

大家好，

我一直在绞尽脑汁解决一个问题，我想也许有人可以指出适合我的情况的最佳做法。

我已经构建了一个. Net Core 2 Restful API。它仍在拼凑，但我遇到了一个“问题”，我不知道哪一个是最好的解决方案。

打电话给我的时候[https://apiURL.com/AddressBook](https://apiURL.com/AddressBook)

然后我可以通过一个参数数组

PageSize
page number
search for&search by(这两个字段一起作为按什么值搜索的字段，但将有一个默认值)

EG: [https://apiURL.com/AddressBook?pageNumber=1 & Pagesize=500](https://apiURL.com/AddressBook?pageNumber=1&Pagesize=500)

我正在扩展这个列表

以...排序

Filterfor & FilterBy

问题是我不知道如何将这些添加到 URL 中，因为你可以一次按多个列排序，也可以添加多个过滤器。

有人有什么建议吗？