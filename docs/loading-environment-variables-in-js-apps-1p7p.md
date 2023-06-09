# Javascript 环境变量在 JS 应用程序中加载环境变量

> 原文：<https://dev.to/deammer/loading-environment-variables-in-js-apps-1p7p>

#### 如何为本地开发存储和消费环境变量

API 和第三方集成要求开发人员使用名为**环境的配置数据或配置变量**。这些变量通常存储在密码保护的地方，如 CI 工具或部署管道，但是当我们在本地开发应用程序时，我们如何使用它们呢？

[![](img/923d5e008779d67209be558e19c20b56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5ng7r_Zd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiTLvajtJ6tN3DnHArGKkDA.png)

#### TL；博士:

*   不要在源代码控制中存储环境变量
*   使用 [dotenv](https://github.com/motdotla/dotenv) 从您的读取数据。环境文件
*   create-react-app 在环境变量上强制命名空间

这个简短的教程将解释一种在本地开发时将环境变量加载到代码中的方法。主要的好处是像 API 键这样的秘密不会被提交到源代码控制中，以保证应用程序的安全性。

#### 要求:

*   Javascript 应用程序
*   包装经理(yarn 和 npm 都很棒)
*   7+节点

### 设置变量

创建一个名为。env”在您的存储库的根目录下。这个文件被称为“点文件”，与普通文件不同，它通常隐藏在文件浏览器中。

大多数 ide 都允许用户创建没有名字的文件，但是如果不是这样的话，就直接进入你的终端，把 cd 放入你的应用程序的根文件夹。

```
touch .env 
```

Enter fullscreen mode Exit fullscreen mode

接下来，用 key=value 格式设置变量，用换行符分隔:

```
API_KEY=abcde
API_URL=https://my-api.com/api 
```

Enter fullscreen mode Exit fullscreen mode

最后，确保。env 文件未提交到您的存储库中。这可以通过打开(或创建)一个. gitignore 文件并添加以下代码行来实现:

```
.env # This contains secrets, don't store in source control 
```

Enter fullscreen mode Exit fullscreen mode

### 消耗变量

前往您的终端安装 [dotenv](https://github.com/motdotla/dotenv) 和您喜欢的软件包管理器:

```
# Using npm:
npm i dotenv

# Using yarn:
yarn add dotenv 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以从您的。环境文件。在应用程序中尽早添加这行代码。对于 React 应用程序，通常是 index.js 或 App.js，但这完全取决于您的设置:

```
require('dotenv').config(); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您的应用程序应该可以通过 process.env 对象访问环境变量。您可以通过调用
进行复查

```
console.log(process.env); 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您应该会看到如下内容:

```
{
 NODE_ENV: "development",
 API_KEY: "abcde",
 API_URL: "https://my-api.com/api"
} 
```

Enter fullscreen mode Exit fullscreen mode

🎉现在，您可以在应用程序中使用环境变量了！

现在，对于我们这些使用 create-react-app 的人来说，有一个问题，我希望它被更好地记录下来。

### 使用创建-反应-应用

脸书的[创建-反应-应用](https://github.com/facebook/create-react-app)的工作方式略有不同。如果您按照上面的步骤操作，并且没有弹出应用程序，那么您应该会看到 NODE_ENV 变量。那是因为 **create-react-app 只允许应用程序读取带有** **REACT_APP_ 前缀的变量。**

所以为了让我们的变量起作用，我们需要更新我们的。env 文件如下:

```
REACT_APP_API_KEY=abcde
REACT_APP_API_URL=https://my-api.com/api 
```

Enter fullscreen mode Exit fullscreen mode

再次通过将 process.env 记录到控制台来验证您的设置:

```
{
 NODE_ENV: "development",
 REACT_APP_API_KEY: "abcde",
 REACT_APP_API_URL: "https://my-api.com/api"
} 
```

Enter fullscreen mode Exit fullscreen mode

你完了😎

### 提示

变量在。env 文件不需要引号，除非值中有空格。

```
NO_QUOTES=thisisokay
QUOTES="this contains spaces" 
```

Enter fullscreen mode Exit fullscreen mode

创建一个. env.sample 文件来跟踪应用程序应该预期的变量是一个很好的做法。下面是我自己的示例文件在当前项目中的样子。请注意，它解释了在哪里可以找到这些键和 URL。

```
CONTENTFUL_SPACE_TOKEN="see Contentful dashboard"
CONTENTFUL_API_KEY="see Contentful dashboard"
S3_BUCKET_URL="check AWS"
SHOW_DEBUG_SIDEBAR="if true, show debug sidebar" 
```

Enter fullscreen mode Exit fullscreen mode

### 进一步阅读:

*   [在 12 因素应用程序方法中读取环境中的配置](https://12factor.net/config)

感谢您的阅读！您是否更喜欢本地加载环境变量的另一种方式？我很想在下面的评论中听到它！