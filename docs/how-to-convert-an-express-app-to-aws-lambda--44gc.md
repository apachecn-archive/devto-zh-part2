# 如何将一个 express app 转换成 AWS Lambda？

> 原文：<https://dev.to/brightdevs/how-to-convert-an-express-app-to-aws-lambda--44gc>

在本帖中，我们将看到如何将现有的 [express](https://expressjs.com/) 应用程序转换为 AWS Lambda。这有助于减少 AWS 账单，甚至减少一个数量级。我们也将使用[云形](https://github.com/bright/cloudform)来描述[云形](https://aws.amazon.com/cloudformation/)堆栈。

[![express.js](img/8ec8799e2dfbabbdcc803ee25818fbcc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8v1OhUUv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cixfubqtggza96d88v2r.png)

## 某快递 app

为了使我们的例子完整，我们需要一个 express 应用程序。让我们用一个单独的`index.js`文件来定义它:

```
const express = require('express');

function apiRoutes(){
    const routes = new express.Router();

    routes.get('/v1/version', (req, res) => res.send({version: '1'}));

    routes.post('/v1/echo', (req, res) => res.send({...req.body}));

    return routes;
}

const app = express()
    .use(express.json())
    .use(apiRoutes());

app.listen(3000, () => console.log(`Listening on 3000`)); 
```

Enter fullscreen mode Exit fullscreen mode

上述应用程序只有两个端点:

*   `GET /v1/version`返回 API 版本
*   `POST /v1/echo`发回请求正文

我们用`node index.js`启动应用程序，就像其他节点应用程序一样。

## 将 express app 转换为 AWS Lambda

[AWS Lambda node . js](https://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-handler.html)运行时模型不同于简单的`node fileName.js`调用。为了让 AWS Lambda 调用我们的应用程序代码，我们需要适当地构建它。谢天谢地， [aws-serverless-express](https://github.com/awslabs/aws-serverless-express) 模块使 express 适应 AWS Lambda Node.js 运行时模型。

让我们修改我们的`index.js`文件来支持`aws-serverless-express` :

```
const isInLambda = !!process.env.LAMBDA_TASK_ROOT;
if (isInLambda) {
    const serverlessExpress = require('aws-serverless-express');
    const server = serverlessExpress.createServer(app);
    exports.main = (event, context) => serverlessExpress.proxy(server, event, context)
} else {
    app.listen(3000, () => console.log(`Listening on 3000`));
} 
```

Enter fullscreen mode Exit fullscreen mode

`main`函数由 AWS Lambda Node.js 运行时调用。注意，我们使用了 [`LAMBDA_TASK_ROOT`](https://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html#lambda-environment-variables) 环境变量来检测应用程序是否在 AWS Lambda 中运行。最好将快速应用程序设置和`listen`调用分成单独的文件，并使用 2 个不同的*主*文件，例如`development.js`调用`listen(port)`和`lambda.js`使用`aws-serverless-express`。然而，这会不必要地使我们的例子变得复杂。

## 将 express app 部署到 AWS Lambda

我已经向[展示了我们如何使用 cloudform](https://dev.to%20post_url%202018-03-12-deploy-lambda-with-cloudformation%20) 部署 lambda。我们将以前面的例子为基础:

```
import cloudform, { Lambda, IAM, Fn, ApiGateway, Refs,  } from 'cloudform';
import { FunctionProperties } from 'cloudform/types/lambda/function';
import { readFileSync } from 'fs';

const
    LambdaExecutionRole = 'LambdaExecutionRole',
    ExpressMain = 'ExpressMain',
    RestApi = 'RestApi',
    RestApiMainResource = 'RestApiMainResource',
    PackageKey = 'PackageKey',
    RestApiDeployment = 'RestApiDeployment';

export default cloudform({
    Parameters: {
        PackageKey: {
            Type: 'String',
            Default: 'express-lambda.zip'
        }
    },
    Resources: {
        [ExpressMain]: new Lambda.Function({
            Code: { S3Bucket: 'bright-tmp', S3Key: Fn.Ref(PackageKey) },
            Handler: "index.main",
            Role: Fn.GetAtt(LambdaExecutionRole, "Arn"),
            Runtime: "nodejs6.10"
        }),
        [LambdaExecutionRole]: new IAM.Role({
            AssumeRolePolicyDocument: {
                Statement: [{
                    Effect: "Allow",
                    Principal: { Service: ["lambda.amazonaws.com"] },
                    Action: ["sts:AssumeRole"]
                }]
            },
            ManagedPolicyArns: ["arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"]
        }),
        PermissionToInvokeLambda: new Lambda.Permission({
            Action: 'lambda:InvokeFunction',
            FunctionName: Fn.GetAtt(ExpressMain, 'Arn'),
            Principal: 'apigateway.amazonaws.com',
            SourceArn: Fn.Join('', [
                'arn:aws:execute-api:', Refs.Region, ':', Refs.AccountId, ':', Fn.Ref(RestApi), '/*']
            )
        })
        ... // Rest Api Gateway see below
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

对于 AWS Lambda 函数源代码，我们使用一个 zip 文件，该文件保存在一个名为`packages`的 S3 桶中，在模板参数`PackageKey`中指定了关键字。包 zip 文件必须包含所有应用程序源代码，包括`node_modules`。我们还定义了`PermissionToInvokeLambda`，以便 API Gateway 可以调用 lambda 函数。

## 添加 API 网关调用 AWS Lambda

为了能够使用 HTTP 协议调用 AWS Lambda 函数，我们将使用 [API 网关](https://aws.amazon.com/api-gateway/)。设置 API 网关有多种方法，但我们将使用一种我认为最简单的方法。

```
[RestApi]: new ApiGateway.RestApi({ Name: "Express API" }),
[RestApiMainResource]: new ApiGateway.Resource({
    RestApiId: Fn.Ref(RestApi),
    ParentId: Fn.GetAtt(RestApi, 'RootResourceId'),
    PathPart: "{proxy+}",
}),
RestApiMethod: new ApiGateway.Method({
    HttpMethod: 'ANY',
    ResourceId: Fn.Ref(RestApiMainResource),
    RestApiId: Fn.Ref(RestApi),
    AuthorizationType: 'NONE',
    Integration: {
        Type: "AWS_PROXY",
        IntegrationHttpMethod: "POST",
        Uri: Fn.Sub("arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${ExpressMain.Arn}/invocations", {})
    }
}),
[RestApiDeployment]: new ApiGateway.Deployment({
    RestApiId: Fn.Ref(RestApi),
    StageName: 'test'
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们首先定义一个 [`RestApi`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-apigateway-restapi.html) ，它是一个资源集合，具有描述 API 的各种配置选项。接下来是描述单个 REST 资源的 [`RestApiMainResource`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-apigateway-resource.html) 。然而，在我们的例子中，我们使用通配符`PathPart`来匹配所有的路径。这样，我们可以拥有一个包含所有 API 端点的单一资源。对于`RestApiMainResource`我们还需要定义一个单独的 [`RestApiMethod`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-apigateway-method.html) 。注意`HttpMethod`被设置为`ANY`，它匹配所有动词。`Integration`指定我们希望将请求代理给`ExpressMain` AWS Lambda 函数。最后但同样重要的是，我们定义了一个 [`RestApiDeployment`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-apigateway-deployment.html) 以便我们有一个 URL 来调用 API。

在我们的模板中定义`Outputs`很方便，这样我们就可以轻松地访问 API url:

```
Outputs: {
    RestApiUrl: {
        Value: Fn.Join('', [Fn.Ref(RestApi), '.execute-api.', Refs.Region, '.amazonaws.com/test'])
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试 API 网关调用 AWS Lambda

使用一个命令部署我们的 cloudform 模板:

```
aws cloudformation update-stack \
  --stack-name lambda-example \
  --capabilities CAPABILITY_IAM \
  --template-body file://<(./node_modules/.bin/cloudform aws-template.ts) 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以获取 API url:

```
API_URL=$(aws cloudformation describe-stacks \
  --stack-name lambda-example \
  --query 'Stacks[0].Outputs[?OutputKey==`RestApiUrl`].OutputValue' \
  --output text) 
```

Enter fullscreen mode Exit fullscreen mode

最后，借助 [`httpie`](https://httpie.org/) 我们可以测试我们的 API:

```
> http https://${API_URL}/v1/version

HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 15
Content-Type: application/json; charset=utf-8
Date: Mon, 28 May 2018 19:58:13 GMT
Via: 1.1 XXXXXXXXXXXXX.cloudfront.net (CloudFront)
X-Amz-Cf-Id: XXXXXXXXXXXXX==
X-Amzn-Trace-Id: Root=1-5b0c5f54-806e190d437c7d31a4a0d4ba
X-Cache: Miss from cloudfront
etag: W/"f-sHigu4BMVa0IJ0LR3NDJ5y8l4sc"
x-amz-apigw-id: HnPVQH4yjoEF8-w=
x-amzn-Remapped-connection: close
x-amzn-Remapped-content-length: 15
x-amzn-Remapped-date: Mon, 28 May 2018 19:58:13 GMT
x-amzn-RequestId: 73eff8a6-62b1-11e8-907c-79117668835e
x-powered-by: Express

{
    "version": "1"
} 
```

Enter fullscreen mode Exit fullscreen mode

以及一个`POST`到回声端点:

```
http https://${API_URL}/v1/echo message="Hello 👋 My name is Piotr"

HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 41
Content-Type: application/json; charset=utf-8
Date: Mon, 28 May 2018 20:00:31 GMT
Via: 1.1 XXXXXXXXXXXXX.cloudfront.net (CloudFront)
X-Amz-Cf-Id: XXXXXXXXXXXXX==
X-Amzn-Trace-Id: Root=1-5b0c5fdf-802178cceb5160684ef2cc34
X-Cache: Miss from cloudfront
etag: W/"29-SKqhJThIfjmVId6IIeTilD7Mkk0"
x-amz-apigw-id: HnPq5HeJjoEFuRQ=
x-amzn-Remapped-connection: close
x-amzn-Remapped-content-length: 41
x-amzn-Remapped-date: Mon, 28 May 2018 20:00:31 GMT
x-amzn-RequestId: c67b41c0-62b1-11e8-a23f-c7cbebde15f2
x-powered-by: Express

{
    "message": "Hello 👋 My name is Piotr"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 降低基础设施成本

有了上面的设置，我们不再需要为一直运行的 EC2 实例付费。我们每月的账单取决于针对公开的 API 执行的请求数量。不仅如此，我们还获得了更好的可伸缩性，特别是当我们有非线性请求率时。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)