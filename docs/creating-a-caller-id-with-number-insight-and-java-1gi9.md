# 使用 Number Insight 和 Java 创建来电显示

> 原文：<https://dev.to/vonagedev/creating-a-caller-id-with-number-insight-and-java-1gi9>

## 简介

在本教程中，您将创建一个可以用作基于 web 的来电显示的应用程序。您的应用程序将向 [Nexmo Number Insight API](https://developer.nexmo.com/number-insight/overview) 发出请求，并显示结果。

在您的后续工作中，请随意参考[NEX mo-community/Java-caller-id](https://github.com/nexmo-community/java-caller-id)存储库。

```
git clone --branch getting-started git@github.com:nexmo-community/java-caller-id.git 
```

## 先决条件

要完成本教程，您需要一个 [Nexmo 帐户](https://dashboard.nexmo.com/sign-up)。如果您还没有帐户，请立即注册。

在注册过程中，您将被分配一个 API 密钥和密码。在本教程的后续步骤中，您将需要这些。

您将使用 [Gradle](https://gradle.org) 来管理您的依赖项并运行您的应用程序。此外，您需要确保安装了 JDK 的副本。

## 用 Java 构建来电显示

本教程将引导您完成以下步骤:

1.  使用 Gradle 创建一个新的 Java 项目。
2.  使用 [Spark](http://sparkjava.com) 框架来服务呼叫者 ID 页面以及处理号码查找请求。
3.  创建一个前端，可用于收集电话号码进行查找。

### 使用 Gradle 设置一个新的 Java 项目

您将使用 Gradle 来管理您的依赖项，并创建和运行您的 Java 应用程序。

`gradle init --type=java-application`命令将创建您需要的所有文件夹以及一个示例类，您将在其中编写代码。

在命令行中，使用以下命令创建一个新的 Java 项目:

```
mkdir java-caller-id
cd java-caller-id
gradle init --type=java-application 
```

### 使用 Spark 框架

您将使用 Spark 框架来服务呼叫者 ID 页面以及通过 API 处理号码查找请求。

#### 添加依赖关系

将以下内容添加到您的`build.gradle`文件中的`dependencies`块:

```
// Spark Framework
compile 'com.sparkjava:spark-core:2.7.2'

// Nexmo Java Client
compile 'com.nexmo:client:3.10.0'

// DotEnv
compile 'io.github.cdimascio:java-dotenv:3.1.1' 
```

#### 创建 API 路由

Gradle 在包含`getGreeting`和`main`方法的`src/main/java`中创建了一个`App`类。删除`getGreeting`方法，因为我们不需要它。

##### 定义环境

您将把它存储在一个环境变量中，而不是硬编码您的 Nexmo API 密钥和秘密。

出于演示的目的，您将使用 [Dotenv](https://github.com/cdimascio/java-dotenv) 库和一个`.env`文件。

在`src/main`中创建一个`resources`文件夹，并用以下信息创建一个名为`.env`的文件:

```
NEXMO_API_KEY=your-api-key
NEXMO_API_SECRET=your-api-secret 
```

分别用您的 Nexmo API 密钥和密码替换`your-api-key`和`your-api-secret`。

##### 定义路线

将事物存储为常量会很有帮助。您将使用之前定义的`NEXMO_API_KEY`和`NEXMO_API_SECRET`实例化一个新的`NexmoClient`。

此外，您将定义一些将在 Spark 路由中使用的常量和一个用于从 API 构建 JSON 响应的`ObjectWriter`。

在`App`中定义以下常量和变量:

```
// Environment
private static final String KEY = Dotenv.load().get("NEXMO_API_KEY");
private static final String SECRET = Dotenv.load().get("NEXMO_API_SECRET");

private final InsightClient insightClient = new NexmoClient(new TokenAuthMethod(KEY, SECRET)).getInsightClient();
private final ObjectWriter writer = new ObjectMapper().writer(); 
```

接下来，您将创建一个返回`spark.Route`的新方法。这个路由将接受 URL 中的一个`NUMBER_PARAM`，执行一个 [Nexmo Number Insight API](https://developer.nexmo.com/number-insight/overview) 请求，并返回一个包含`AdvancedInsightResponse`信息的 JSON 响应。

在`main`方法下面的`App`中创建以下方法:

```
/**
* @return A {@link Route} which will handle looking up the number insight information.
*/
private Route createRequestRoute() {
    return (request, response) -> {
        final String number = request.params(NUMBER_PARAM);
        final AdvancedInsightResponse advancedInsightResponse = insightClient.getAdvancedNumberInsight(number,
                "",
                "",
                true
        );

        response.type(ContentType.APPLICATION_JSON.getMimeType());
        return this.writer.writeValueAsString(advancedInsightResponse);
    };
} 
```

##### 登记路线

您需要配置 Spark，以便它知道何时使用您刚刚定义的路由。

首先您将配置 Spark 运行的端口，然后您将配置您新创建的路由将响应的路径(`/api/:number`)。

您定义的路径将使用`:number`参数，该参数代表路径中的电话号码。

将以下常量添加到您创建的所有其他常量附近的`App`中:

```
private static final int PORT = 3000;

private static final String NUMBER_PARAM = ":number";
private static final String REQUEST_ROUTE = "api/" + NUMBER_PARAM; 
```

在`createRequestRoute`方法之上的`App`中增加以下方法:

```
/**
* Start the Sparkframework Application
*/
private void start() {
    Spark.port(PORT);
    Spark.get(REQUEST_ROUTE, createRequestRoute());
} 
```

现在，您将更改`App`中的`main`方法，以便在应用程序运行时启动 Spark。

将`App`中的`main`方法修改为:

```
public static void main(String[] args) {
    new App().start();
} 
```

使用`gradle run`命令启动应用程序。

您现在可以导航到[http://localhost:3000/API/13034997111](http://localhost:3000/api/13034997111)，应该会看到类似下面的响应:

```
{  "status":  0,  "ported":  "ASSUMED_NOT_PORTED",  "roaming":  {  "status":  "NOT_ROAMING",  "roaming_country_code":  null,  "roaming_network_code":  null,  "roaming_network_name":  null  },  "status_message":  "Success",  "error_text":  null,  "request_id":  "bf845c39-d0e7-4004-bd68-b8bbadb3b5a6",  "international_format_number":  "13034997111",  "national_format_number":  "(303) 499-7111",  "country_code":  "US",  "country_code_iso3":  "USA",  "country_name":  "United States of America",  "country_prefix":  "1",  "request_price":  "0.04000000",  "remaining_balance":  "39.02969834",  "original_carrier":  {  "name":  "United States of America Landline",  "country":  "US",  "network_code":  "US-FIXED",  "network_type":  "LANDLINE"  },  "current_carrier":  {  "name":  "United States of America Landline",  "country":  "US",  "network_code":  "US-FIXED",  "network_type":  "LANDLINE"  },  "caller_name":  "Us Time & Frequency Division",  "first_name":  null,  "last_name":  null,  "caller_type":  "BUSINESS",  "valid_number":  "VALID",  "reachable":  "UNKNOWN",  "lookup_outcome":  1,  "lookup_outcome_message":  "Partial success - some fields populated"  } 
```

#### 存储静态内容

你需要一个文件夹来存放你的静态内容。这个文件夹应该在您之前创建的`src/main`下的`resources`目录中。一个好的惯例是将文件夹命名为`public`,并放置一个`css`和`js`文件夹。

[![Resource folder containing a public folder which contains a css and js folder](img/baa4fb103e68ae9b8b7335914714ab2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FjbXbLPu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2018/08/resource-folder.png)

在`public`文件夹中添加一个`index.html`文件，内容如下:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    Caller ID
</head>
<body>
<h1>Hello World!</h1>
</body>
</html> 
```

#### 上菜静态内容

静态内容由特定的文件夹提供。您将创建一个常数来表示该文件夹的位置。

在`App`中定义以下常量:

```
private static final String STATIC_FILE_LOCATION = "/public"; 
```

现在您需要更新 Spark，以便它知道从这个位置提供静态内容。

将`App`中的`start`方法更新为:

```
/**
* Start the Spark Framework Application
*/
private void start() {
    Spark.staticFileLocation(STATIC_FILE_LOCATION);
    Spark.port(PORT);
    Spark.get(REQUEST_ROUTE, createRequestRoute());
} 
```

运行`gradle run`命令。

这将启动您的应用程序，并开始在 [http://localhost:3000](http://localhost:3000) 提供您的`index.html`文件。在您的浏览器中导航到这个地址将会在您之前制作的`index.html`页面中显示 **Hello World** 文本。

### 构建用户界面

既然 Java 和 Spark 已经执行了对 [Nexmo Number Insight API](https://developer.nexmo.com/number-insight/overview) 的请求并提供静态内容，那么是时候继续构建用户界面了。

#### 示例用户界面

以下是用户界面的一种方法。它使用 JavaScript 对您在前面步骤中构建的 API 进行 AJAX 调用。然后，它在页面上呈现信息。它还使用 [cleave.js](https://github.com/nosir/cleave.js/) JavaScript 库来格式化输入的电话号码。

结果会产生如下页面:
[![index.html page with a form for entering a phone number and the resulting information](img/ce3f4ae0a2020ecc839c0fc2ea80b24b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dHLZrZmW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nexmo.com/wp-content/uploads/2018/08/user-interface.png)

##### 创建索引页面

将您的`index.html`替换为以下内容:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1,user-scalable=no">
    <link rel="stylesheet" href="css/style.css">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.2.0/css/all.css"
          integrity="sha384-hWVjflwFxL6sNzntih27bfxkr27PmbbK/iSvJ+a4+0owXq79v+lsFkW54bOGbiDQ" crossorigin="anonymous">
    Nexmo Caller ID
</head>
<body>
<div class="nav">
    <div class="inner-nav">
        <h1>Nexmo Caller ID</h1>
    </div>
</div>
<div class="page">
    <div class="search">
        <div class="inner-search">
            <h1>Enter a phone number</h1>
            <div class="search-bar">
                <div class="search-bar-input">
                    <form>
                        <input type="text" class="phone-input" id="phone-number" placeholder="+1 123 456 7890"
                               autofocus>
                        <button class="form-button" id="look-up"><i class="fas fa-search"></i></button>
                    </form>
                </div>
            </div>
            <p>Searches are performed against the Nexmo Number Insight API</p>
        </div>
    </div>
    <div class="details" hidden></div>
</div>
<div class="footer">
    <div class="inner-footer">
        <ul>
            <li><a href="https://nexmo.com">Nexmo</a></li>
            <li><a href="https://developer.nexmo.com/number-insight/overview">Documentation</a></li>
        </ul>
    </div>
</div>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/cleave.js/1.4.2/cleave.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/cleave.js/1.4.2/addons/cleave-phone.i18n.js"></script>
<script src="js/app.js"></script>
</html> 
```

##### 样式索引页面

在`src/main/resources/public/css`文件夹中创建`style.css`，内容如下:

```
body, html {
    height: 100%;
    margin: 0;
    padding: 0;
    font-family: Lato, sans-serif;
    background-color: #deedf7;
    font-weight: 200;
}

form {
    display: flex;
}

h1, h2, h3, h4 {
    font-weight: 100;
    margin: .25em;
}

.inner-nav h1 {
    font-weight: 300;
    font-size: 3em;
}

.nav {
    background-color: #0077c8;
    color: #eef6fb;
}

.inner-nav {
    display: flex;
    width: 100%;
    max-width: 970px;
    margin: auto;
    justify-content: center;
}

.search {
    background: #0077c8;
    display: flex;
    text-align: center;
    width: 100%;
    flex-flow: column;
}

.details {
    background: #ffffff;
    display: flex;
    text-align: center;
    width: 100%;
    flex-flow: row;
    justify-content: center;
    flex-wrap: wrap;
}

.inner-details {
    text-align: left;
    border: 1px solid #deedf7;
    border-radius: 4px;
    margin: 0.25em;
    padding: 0.25em;
}

.inner-details h1 {
    border-bottom: 1px solid #deedf7;
}

.output-table {
    text-align: left;
}

.output-table .side-heading {
    font-weight: normal;
    text-align: left;
}

.output-table td {
    padding: 5px;
}

.inner-search {
    width: 100%;
    max-width: 970px;
    margin: auto;
    padding: 0 5px;
    color: #eef6fb;
}

.inner-search h1 {
    font-weight: 100;
}

.inner-search p {
    color: #fcfcfd;
    font-weight: 100;
}

.search-bar {
    position: relative;
    width: 100%;
    margin: auto;
    z-index: 50;
    max-width: 300px;
}

.search-bar-input {
    position: relative;
    z-index: 51;
    background: #ffffff;
    display: flex;
    text-align: center;
    height: 50px;
    border-radius: 5px;
}

.search-bar-input input {
    border: 0;
    background-color: transparent;
    display: block;
    width: 100%;
    font-size: 1.5em;
    padding: .5em 1em;
    font-weight: 100;
}

.search-bar-input input:focus, .search-bar-input button:focus {
    outline: none;
}

.search-bar-input button {
    background-color: transparent;
    border: 0;
    cursor: pointer;
    font-size: 1.5em;
    color: #0077c8;
}

.inner-footer ul {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    list-style: none;

}

.inner-footer ul a {
    padding: 1em;
    font-size: 1em;
    text-decoration: none;
    display: flex;
    color: #717171;
} 
```

##### 创建 JavaScript

在`src/main/resources/public/js`文件夹中创建`app.js`，内容如下:

```
let cleave = new Cleave('.phone-input', {
    phone: true,
    phoneRegionCode: 'US'
});

let lookUpButton = document.getElementById("look-up");
lookUpButton.onclick = function (event) {
    event.preventDefault();
    let phoneNumber = document.getElementById("phone-number").value.replace(/[^0-9.]/g, "");
    handlePhoneNumberLookup(phoneNumber);
};

function handlePhoneNumberLookup(phoneNumber) {
    fetch("/api/" + phoneNumber)
        .then(response => {
            if (response.ok) {
                return Promise.resolve(response);
            }

            return Promise.reject("Error Fetching Number Information");
        })
        .then(response => response.json())
        .then(data => {
            renderResponse(data);
        })
        .catch(function (error) {
            console.log("Error: " + error)
        })
}

function renderResponse(data) {
    let detailsContainer = document.getElementsByClassName("details")[0];
    detailsContainer.removeAttribute("hidden");
    detailsContainer.innerHTML = '';
    console.log(data);
    if (data.international_format_number && data.national_format_number) {
        detailsContainer.innerHTML += `
                <div class="inner-details">
                <h1>Number Details</h1>
                <table class="output-table">
                    <tr>
                        <td class="side-heading">International Format</td>
                        <td>${data.international_format_number}</td>
                    </tr>
                    <tr>
                        <td class="side-heading">National Format</td>
                        <td>${data.national_format_number}</td>
                    </tr>
                </table>
                </div>`;
    }

    if (data.caller_type && data.country_name) {
        detailsContainer.innerHTML += `
<div class="inner-details">
                <h1>Caller Details</h1>
                <table class="pure-table pure-table-bordered output-table">
                    <tr>
                        <td class="side-heading">Name</td>
                        <td>${data.caller_name || 'Unknown'}</td>
                    </tr>
                    <tr>
                        <td class="side-heading">Type</td>
                        <td>${data.caller_type}</td>
                    </tr>
                    <tr>
                        <td class="side-heading">Country</td>
                        <td>${data.country_name}</td>
                    </tr>
                </table></div>`
    }

    if (data.roaming && data.roaming.roaming_network_name && data.roaming.roaming_country_code) {
        detailsContainer.innerHTML += `
<div class="inner-details">
                <h1>Roaming Details</h1>
                <table class="output-table">
                    <tr>
                        <td class="side-heading">Network Name</td>
                        <td>${data.roaming.roaming_network_name}</td>
                    </tr>
                    <tr>
                        <td class="side-heading">Country Code</td>
                        <td>${data.roaming.roaming_country_code}</td>
                    </tr>
                </table></div>
            `
    }

    if (data.ported && data.original_carrier && data.current_carrier) {
        detailsContainer.innerHTML += `
<div class="inner-details">
    <h1>Porting Information</h1>
                <table class="output-table">
                    <tr>
                        <td class="side-heading">Status
                        </td>
                        <td>${data.ported}</td>
                    </tr>
                </table>
                <h2>Current Carrier</h2>
                <table class="output-table">
                    <tr>
                        <td class="side-heading">Name</td>
                        <td>${data.current_carrier.name}</td>
                    </tr>
                    <tr>
                        <td class="side-heading">Country</td>
                        <td>${data.current_carrier.country}</td>
                    </tr>
                </table>
                <h2>Original Carrier</h2>
                <table class="output-table">
                    <tr>
                        <td class="side-heading">Name</td>
                        <td>${data.original_carrier.name}</td>
                    </tr>
                    <tr>
                        <td class="side-heading">Country</td>
                        <td>${data.original_carrier.country}</td>
                    </tr>
                </table></div>
            `
    }
} 
```

#### 测试你的最终应用

在您的`java-caller-id`目录中用`gradle run`命令启动您的应用程序。导航到 [http://localhost:3000](http://localhost:3000) ，你将准备好获得洞察力。

## 结论

Nexmo Number Insight API 是获取有用数字信息的强大工具。它使用来自运营商数据库和 [Nexmo](https://nexmo.com) 数据库的大量实时数据，为您提供最准确的信息。

探索使用 [Nexmo Number Insight API](https://developer.nexmo.com/number-insight/overview) 的其他方式。想想你还能把它和什么其他服务集成在一起。

查看我们关于 [Nexmo Developer](https://developer.nexmo.com) 的文档，在那里您可以了解更多关于 [Nexmo Number Insight API](https://developer.nexmo.com/number-insight/overview) 和其他 [Nexmo](https://nexmo.com) 产品的信息。参见我们的[Nexmo Java 快速入门示例](https://github.com/nexmo-community/nexmo-java-quickstart)获取本教程的完整代码示例及更多内容。