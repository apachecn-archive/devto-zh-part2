# 为您的 SAAS 构建 SDK

> 原文：<https://dev.to/dviejopomata/building-sdks-for-your-saas-23hb>

# 为您的 SAAS 构建 SDK

我最近构建了一个服务， [docxmerge](https://docxmerge.com) ，它从 Microsoft Word 和 JSON 文档进行合并，并将其转换为 PDF。

API 非常简单，但是为了便于开发人员将它集成到他们的应用程序中，我选择为至少四种语言创建一个库，NodeJS、Dotnet、Java 和 Python。

是啊，看起来我们有点活要干了。这项任务要么太容易，要么太尴尬。但我有一个好处，那就是使用 Swagger 进行 API 定义，所以基本上，我可以生成所有的样板代码，只需用每种语言编写一个类，并将其发布到相应的注册表中。

使用 Swagger，所有都基于后端生成的 JSON，在本例中是下一个:

[https://api.docxmerge.com/api/swagger/v1/swagger.json](https://api.docxmerge.com/api/swagger/v1/swagger.json)

有了这个 JSON，swagger codegen 就能够毫不费力地生成代码，以一种类型化的方式与您的 api 进行交互。我用 docker 生成代码，它要干净得多:

```
docker run --rm --network=host -v ${PWD}/src/swagger:/local swaggerapi/swagger-codegen-cli:v2.3.1 generate -i ${SWAGGER_URL} -l typescript-node -o /local --additional-properties supportsES6=true 
```

正如 a 所说，Swagger 是最大的优势，因为它是免费的，我不必担心路径，查询字符串，多部分请求等。幸运的是，我能够使用这个 [Swashbucle 库](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)从 C#注释中生成它，所以不必编写大型 JSON)

我将针对每种语言发布一篇文章，解释我是如何从 swagger-codegen 到 publish 构建每个库的。

这是我工作的仓库:
[https://github.com/Docxmerge/docxmerge-sdk](https://github.com/Docxmerge/docxmerge-sdk)

它仍在进行中，由于验证的原因，我仍然缺少要部署的 Java，但它可以工作。