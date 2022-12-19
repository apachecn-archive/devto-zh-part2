# 离线-首先使用 Node.js 和 Hoodie:渐进式 Web 应用程序的实用介绍

> 原文：<https://dev.to/pmbanugo/offline-first-with-nodejs-and-hoodie-a-practical-introduction-to-progressive-web-apps-5f57>

**渐进式网络应用**(或简称 PWAs)旨在通过使用现代网络标准和最佳实践应用渐进式增强来提供更好、更吸引人的用户体验。其中包括[服务人员](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)、[推送 API](https://developer.mozilla.org/en/docs/Web/API/Push_API)、[后台同步](background%20sync)以及通过 HTTPS 为你的页面提供服务。

如果应用程序在不支持网络标准的浏览器中加载，它应该像普通网站一样工作。然而，在现代浏览器中，应该通过确保应用程序:

*   无论用户网络条件如何(4G、3G、2G 或离线)，都能可靠工作
*   可在用户的设备上安装，感觉很自然

我们将逐步构建一个购物跟踪器 PWA，它将向您介绍如何使用服务人员、缓存 API 和连帽衫。为了跟进，您需要安装 [Node.js](https://nodejs.org/en/) 和 [npm](https://www.npmjs.com/) 。

# 开发设置

要设置您的环境，克隆[https://github.com/pmbanugo/shopping-list-starter](https://github.com/pmbanugo/shopping-list-starter)上的文件。通过在命令行中运行以下命令，克隆并安装项目依赖项:

```
git clone https://github.com/pmbanugo/shopping-list-starter.git
cd shopping-list-starter/
npm install 
```

安装的依赖项是 Babel 和相关的插件，我们稍后将使用它们进行传输。Babel 允许我们用 JavaScript 编写最新的标准，然后将其转换成旧的标准，比如 ES5，这样我们的代码就可以在当今的任何浏览器上运行。我们将使用 ES2015 的一些功能，如 [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let) 、 [const](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/const) 、[箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)和 [ES 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)。包含在 **public** 目录中的文件是呈现一个好看的 UI 所需的页面和 CSS 文件。

以下是您将要建立的目标:

[![](../Images/bd573d0dcb960b1604e758ca164fccfa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0kR7rRmz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/81wGimLqxB8U3xtzvB3rJnrrGo39fK3BsQFzodw63_B0TuD3gOzpMR7X5NGn-I07XpxJ3sKGwisV9qXnNO9eEWsMgzLvVlIB43w_-ydgmjvZet8dB5MX8BrN26YFWgTw7WLxoriz.png)

[![](../Images/fe89a2c00b8359e3fc63ec0b260eba3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z3mt8c7Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/1SZ-kxjbuM83S4pmMZ0LzgtUE0diKobqJtEFqGW0KQWnTh5Js0CqRNVmLQFh_XAXuFoxzrH04iTOWl9K0coAbygVoCBLjpdIdrh1nyrJhd9bSuoQwj2BviIYoJYMh39K8JYF_FwQ.png)

该应用程序允许将商品及其价格添加到列表中，保存它，并在单独的页面上查看摘要。

# 保存数据

为了添加保存和删除购物项目的功能，我们将在项目中添加一个名为 [Hoodie](http://hood.ie/) 的`npm`包。运行以下命令来安装它(在撰写本文时，我使用的是 hoodie 的 28.2.2 版本)

```
npm install --save hoodie 
```

# 快速介绍帽衫和*离线-第一次*

PWA 的一个主要特性是能够离线和在线工作，因此，我们需要将*离线优先*的概念应用到应用程序中。

***“离线优先”*** 是一种软件开发方法，在这种方法中，缺少网络连接不被视为错误。你首先要开发它，使它能在没有互联网连接的地区工作。然后，随着用户进入有网络连接的区域或随着他们的连接速度提高，应用程序会逐步增强，以使应用程序中提供更多功能。对于本教程，我们希望能够在用户离线或在线时添加和删除数据。这就是帽衫可以帮忙的地方。

Hoodie 是离线优先 web 应用程序的 JavaScript 后端。它提供了一个前端 API，允许您存储和管理数据，并添加用户验证。它将数据存储在本地设备上，当有网络连接时，将数据同步到服务器并解决任何数据冲突。它在客户端使用 [PouchDB](https://pouchdb.com/) ，在服务器端使用 [CouchDB](https://couchdb.apache.org/) 和 [hapi](https://hapijs.com/) 。我们将使用它来进行用户身份验证以及存储购物项目。

# 添加物品

我们将添加的第一个功能允许用户添加新项目。在`public/js/src.`中有一个名为`index.js`的文件。它包含在页面中显示保存到 Hoodie 的项目的功能。

通过在第 197 行的`</body>`标签前添加对`index.js`和`hoodie`客户端脚本的引用来编辑`index.html`。

```
<script src="/hoodie/client.js"></script>
<script src="/js/src/index.js"></script>
</body> 
```

运行应用程序时，可以从`/hoodie/client.js`访问连帽衫客户端脚本。按照惯例，它还为公共文件夹中的文件提供服务。打开`index.js`并在其中添加以下内容

```
function saveNewitem() {
  let name = document.getElementById("new-item-name").value;
  let cost = document.getElementById("new-item-cost").value;
  let quantity = document.getElementById("new-item-quantity").value;
  let subTotal = cost * quantity;

  if (name && cost && quantity) {
    hoodie.store.withIdPrefix("item").add({
      name: name,
      cost: cost,
      quantity: quantity,
      subTotal: subTotal
    });

    document.getElementById("new-item-name").value = "";
    document.getElementById("new-item-cost").value = "";
    document.getElementById("new-item-quantity").value = "";
  } else {
    let snackbarContainer = document.querySelector("#toast");
    snackbarContainer.MaterialSnackbar.showSnackbar({
      message: "All fields are required"
    });
  }
}

function init() {
  hoodie.store.withIdPrefix("item").on("add", addItemToPage);

  document.getElementById("add-item").addEventListener("click", saveNewitem);

  //retrieve items on the current list and display on the page
  hoodie.store
    .withIdPrefix("item")
    .findAll()
    .then(function(items) {
      for (let item of items) {
        addItemToPage(item);
      }
    });
}

init(); 
```

当这个脚本被加载到浏览器中时，它调用`init()`，通过调用`hoodie.store.withIdPrefix("item")`获取所有保存在本地的条目，并通过调用`addItemToPage(item)`将它们呈现在页面上。

我们使用`hoodie.store.withIdPrefix("item").on("add", addItemToPage)`在**商品**商店订阅`add`事件。随着每一个新商品被添加到商店，它调用`addItemToPage`函数。当在页面上点击 Add Item 按钮时，它调用`saveNewItem`保存数据。

# 删除项目

要从商店中删除商品，您可以用要删除的商品的 ID 调用`hoodie.store.withIdPrefix("item").remove(itemId)`。

修改`index.js`在`init()`调用前增加以下内容。

```
function deleteRow(deletedItem) {
  let row = document.getElementById(deletedItem._id);
  let totalCost = Number.parseFloat(
    document.getElementById("total-cost").value
  );
  document.getElementById("total-cost").value =
    totalCost - deletedItem.subTotal;
  row.parentNode.removeChild(row);
}

function deleteItem(itemId) {
  hoodie.store.withIdPrefix("item").remove(itemId);
} 
```

修改`init()`函数，使其包含以下几行:

```
function init() {
  hoodie.store.withIdPrefix("item").on("add", addItemToPage);

  hoodie.store.withIdPrefix("item").on("remove", deleteRow);

  document.getElementById("add-item").addEventListener("click", saveNewitem);

  //retrieve items on the current list and display on the page
  hoodie.store
    .withIdPrefix("item")
    .findAll()
    .then(function(items) {
      for (let item of items) {
        addItemToPage(item);
      }
    });

  window.pageEvents = {
    deleteItem: deleteItem
  };
} 
```

我们订阅了`remove`事件，该事件调用一个方法从页面的列表中删除项目。此外，我们向页面公开了一个`deleteItem`函数，当项目从页面中移除时，该函数将被调用。在`index.html`的第 189 行，您会发现将删除按钮的`onclick`事件连接到该方法的语句

```
//Line 189
<td class="mdl-data-table__cell--non-numeric">
  <button class="mdl-button mdl-js-button mdl-button--icon mdl-button--colored"
  onclick="pageEvents.deleteItem('{{item-id}}')">
  <i class="material-icons">remove</i>
  </button>
</td> 
```

现在我们已经有了添加和删除项目的代码，让我们运行应用程序，看看它是否工作。在`package.json`的“脚本”部分添加以下内容，以创建开始命令:

```
"scripts":  {  ...  "start":  "hoodie"  }, 
```

在命令行中运行命令`npm start`来启动服务器。在浏览器中打开 [http://localhost:8080](http://localhost:8080) ，您应该会看到页面已经加载完毕，可以使用了。通过添加和删除一些项目进行测试:

[![](../Images/bac6e89a30ce50684639cf03cb848335.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X4gJ6GmG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/WWkwSfo19pxjXhJNFq2igwF4d6jx7TVzH_4XPnh-QST5VY6wgrJDHC7a5ki4kLyAQY_HdEd5ytL4PIUrtc5wq33jR2sNg_df7HFgJ7RQWZzkDDOmujKPVuQ7L7wq8v5NNsOTP6A1.png)

我们可以看到我们的列表工作了，数据被保存了。但是这些数据只存储在本地，并不保存在服务器上。我们如何让它将数据推送到服务器？

对于 Hoodie，只有在用户通过身份验证后，数据才会被保留。当用户通过身份验证时，数据首先保存在本地，然后推送到服务器，并在用户登录的其他设备之间同步。让我们添加这个必需的身份验证。

# 用连帽衫登录和注册功能

我们已经为*登录*、*注销*和*注册*添加了标记，作为您克隆的`index.html`内容的一部分。如果你想看看标记，就去看看吧。

在`public/js/src`中打开名为`shared.js`的文件。该文件将保存使用 Hoodie 验证用户的代码。我将它放在一个单独的文件中，因为它将与我们稍后添加的另一个页面共享。用以下代码编辑`login`和`register`功能:

```
 let login = function() {
  let username = document.querySelector("#login-username").value;
  let password = document.querySelector("#login-password").value;

  hoodie.account
    .signIn({
      username: username,
      password: password
    })
    .then(function() {
      showLoggedIn();
      closeLoginDialog();

      let snackbarContainer = document.querySelector("#toast");
      snackbarContainer.MaterialSnackbar.showSnackbar({
        message: "You logged in"
      });
    })
    .catch(function(error) {
      console.log(error);
      document.querySelector("#login-error").innerHTML = error.message;
    });
};

let register = function() {
  let username = document.querySelector("#register-username").value;
  let password = document.querySelector("#register-password").value;
  let options = { username: username, password: password };

  hoodie.account
    .signUp(options)
    .then(function(account) {
      return hoodie.account.signIn(options);
    })
    .then(account => {
      showLoggedIn();
      closeRegisterDialog();
      return account;
    })
    .catch(function(error) {
      console.log(error);
      document.querySelector("#register-error").innerHTML = error.message;
    });
}; 
```

添加以下功能来处理`shared.js`中的签出:

```
let signOut = function() {
  hoodie.account
    .signOut()
    .then(function() {
      showAnonymous();
      let snackbarContainer = document.querySelector("#toast");
      snackbarContainer.MaterialSnackbar.showSnackbar({
        message: "You logged out"
      });
      location.href = location.origin;//trigger a page refresh
    })
    .catch(function() {
      let snackbarContainer = document.querySelector("#toast");
      snackbarContainer.MaterialSnackbar.showSnackbar({
        message: "Could not logout"
      });
    });
};

let updateDOMWithLoginStatus = () => {
  hoodie.account.get("session").then(function(session) {
    if (!session) {
      // user is signed out
      showAnonymous();
    } else if (session.invalid) {
      // user has signed in, but session has expired
      showAnonymous();
    } else {
      // user is signed in
      showLoggedIn();
    }
  });
}; 
```

更新导出语句，以包含两个新添加的函数:

```
export {
  register,
  login,
  ...
  signOut,
  updateDOMWithLoginStatus
}; 
```

我们定义了一个用用户名和密码调用`hoodie.account.signUp()`的`register`函数。当它成功时，它调用`hoodie.account.signIn()`让用户登录。我们还分别添加了`login`和`signOut`方法来登录和退出。这些用于认证的 API 存在于`hoodie.account`中。方法`updateDOMWithLoginStatus()`根据用户是否通过身份验证来更新导航栏以显示不同的链接。

更新`index.js`以使用该文件。首先在文件顶部添加一条导入语句:

```
import * as shared from "shared.js"; 
```

修改`init`函数以在页面加载时调用`shared.updateDOMWithLoginStatus()`来更新导航栏。然后，将`login`和`signOut`函数映射到`pageEvents`对象(在 deleteItem 函数后添加一个逗号):

```
function init() {
  shared.updateDOMWithLoginStatus();
  hoodie.store.withIdPrefix("item").on("add", addItemToPage);
  hoodie.store.withIdPrefix("item").on("remove", deleteRow);

  window.pageEvents = {
    ...
    closeLogin: shared.closeLoginDialog,
    showLogin: shared.showLoginDialog,
    closeRegister: shared.closeRegisterDialog,
    showRegister: shared.showRegisterDialog,
    login: shared.login,
    register: shared.register,
    signout: shared.signOut
  };
} 
```

我们在这里使用了 ES 模块。我们一直在代码中使用 ES2015 模块，然而，并非所有浏览器都支持这一点，因此我们需要一种方法来让所有浏览器都支持这一点。我们将使用 Babel 来传输代码，以便与 [SystemJS](https://github.com/systemjs/systemjs) 一起工作，这是一个模块加载器，支持浏览器和 Node.js 中的动态 ES 模块工作流。(我们已经有了这样做所需的文件。)

# 传输我们的代码以增加浏览器支持

您克隆的 GitHub repo 在`public/resources/system.js`中已经有一个`system.js`文件。我们还安装了 Babel 作为依赖项的一部分(见`package.json`)，以及一个 Babel 配置文件(见`.babelrc`)。

```
//file  ->  .babelrc  {  "plugins":  ["transform-es2015-modules-systemjs"],  "presets":  ["es2015"]  } 
```

这告诉 Babel 将我们的 JavaScript 代码转换为 ES5 兼容代码，并将任何 ES2015 模块转换为 SystemJS 模块。

为了触发传输，我们将在`package.json`中添加一个构建脚本，如下所示:

```
"scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1",  "start":  "hoodie",  "build":  "babel public/js/src --out-dir public/js/transpiled"  } 
```

编辑`index.html`,在我们的`hoodie`包含下面包含对 SystemJS 和 transpiled `index.js`的引用:

```
<body>
....
  <script src="/hoodie/client.js"></script>
  <script src="resources/system.js"></script>
  <script>
    System.config({ "baseURL": "js/transpiled" });
    System.import("index.js");
  </script>
</body> 
```

现在，运行下面的命令来传输代码

```
npm run build 
```

如有必要，重新启动服务器(`npm start`)，刷新页面，然后尝试注册、登录和注销

[![](../Images/362f67c262c18d67cc6f20a1f0f0ce81.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VYI4Sq8v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/MFgw39sCu5LemsduhV9-6kcoTpiJQyfZ2LSahDt2VnmLgZYv3X7mIwEocSbFUXo5G6NhFORSZ4_ZD6JESCnpFrOsXPvnyFjXVk8JZD5u08B6xfhkj_sKfaxHT0zsESv7LtEnI2L4.png)

# 保存购物清单和查看历史记录

随意添加和删除项目后，您可能希望保存项目列表，并在单独的页面上获得汇总。在完成的 GitHub repo 中，我在 index.js 中有一个函数 [saveList()](https://github.com/pmbanugo/shopping-list/blob/d660f5e25fe55c45b720eb22a61df47dd8b31897/public/js/src/index.js#L63) ，这个方法:

1.  通过调用`hoodie.store.withIdPrefix("item").findAll()`从项目存储中获取所有保存的项目，
2.  计算所有项目的总和
3.  将项目的总成本与列表存储中的项目一起保存(`hoodie.store.withIdPrefix("list").add({cost, items})`)
4.  从项目存储中移除所有项目，以便添加新项目。

我们将在另一页`history.html`上汇总包含价格和添加日期的列表。处理这个问题的脚本在 GitHub 上已完成代码的`public/js/src/`中的文件`history.js`中。为了简洁起见，我在这里省略了展示这段代码。代码类似于我们到目前为止所写的内容。

如果您将代码从这些源代码复制到您的工作目录中，请再次运行构建脚本(`npm run build`)并刷新页面。如果有效，添加一些项目并保存列表。当您转到历史页面时，您应该在那里看到保存的列表:

[![](../Images/ba30372630ce7e302a86c9f5a239768d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MM0xf_D1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/26Dop4gvU_JNU8bnEHaeb-5IFu-fSLzTex6OARsORc5lQm6f95rN307apMDG458d0h3NeFDDnp96CxTqW6Xwk0_vK6Yq8LG_mGp4HbAhIsAmY-1ds8uIQLxpYihQ1cGwK2i4SOPA.png)

干得好，真的是走到一起了！现在让我们讨论一下如何让我们的应用程序适应无缝离线使用。

# 离线页面加载

到目前为止，我们已经能够保存和查看数据。这在用户离线时有效，甚至在服务器关闭时也有效，当有连接时，它会同步到服务器。

然而，目前我们将看到一个错误，当我们试图在离线时加载页面。让我们利用服务人员和缓存 API 来解决这个问题。

## 服务人员和缓存 API 的快速介绍

一个[服务工作者](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)是一个可编程的网络代理，它运行在一个单独的浏览器线程上，允许你拦截网络请求并按照你的选择处理它们。您可以拦截并缓存来自服务器的响应，下次应用程序请求该资源时，您可以发送缓存的版本。无论页面当前是否打开，它都会运行。

我们将添加一个服务工作者脚本，该脚本将拦截所有网络请求，并在资源引用我们的页面及其相关资产时以缓存版本进行响应。将使用缓存 API 缓存这些资源。

缓存 API 是服务工作者规范的一部分，它使服务工作者能够缓存网络请求，以便他们即使在离线时也能提供适当的响应。

## 创建服务工作者脚本

在`public/sw.js`的公共文件夹中添加一个名为`sw.js`的新文件。要告诉浏览器我们希望这个脚本成为我们的服务人员脚本，打开`shared.js`，将这段代码添加到您的文件的顶部:

```
if ("serviceWorker" in navigator) {
  navigator.serviceWorker
    .register("sw.js")
    .then(console.log)
    .catch(console.error);
} 
```

这段代码将首先检查浏览器是否支持服务工作者，然后将文件`sw.js`注册为服务工作者脚本。

再次运行构建脚本(`npm run build`)并刷新页面。

如果你还没有，打开你的浏览器 JavaScript 控制台(这里是如何在 [Chrome](https://developers.google.com/web/tools/chrome-devtools/console/?utm_source=dcc&utm_medium=redirect&utm_campaign=2016q3) 、 [Firefox](https://developer.mozilla.org/en-US/docs/Tools/Browser_Console) 和 [Edge](https://docs.microsoft.com/en-us/microsoft-edge/devtools-guide/console) 中操作)，你应该会看到一些关于服务人员的内容被打印到控制台。在您的开发工具中导航到应用程序选项卡(或者类似的，如果您不在 Chrome 中),并在侧菜单中单击“服务人员”,您应该会看到类似于此屏幕的内容:

[![](../Images/cfba8b75883f28d0d7f6a25d950309b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rdiEwsz---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/dMunIdK88bKlfquLZFr5aE3jOx4mLDQSFFF5nhN5YnhOYaf9_tJUSvlYfmSs7C3wY5UseMQoP4ybbeAiMjwG2Xfk90kOGgxXuadIC6RIYjAUp4GZrzvbzr2NqjcOMGaQTSyWfxQF.png)

注册服务人员将导致浏览器在后台启动服务人员`install`步骤。在这个安装步骤中，我们希望获取和缓存我们的资产。

如果资产被成功缓存，则它被安装并移动到 ***激活步骤*** 。如果失败，将不会安装服务人员。在激活步骤中，我们需要删除旧的资产缓存，以便我们的服务人员可以提供更新的资源。

在激活步骤之后，服务工作者将控制其范围内的所有页面。最初第一次注册服务人员的页面在再次加载之前不会受到控制。

注册后发生的所有这些步骤(安装和激活)都是服务人员生命周期的一部分。稍后你可以阅读更多关于这些概念。

## 修改我们的服务人员

我们的服务人员脚本当前为空。为了让我们监听安装步骤并使用缓存 API 缓存所有资产，在`sw.js`中添加以下代码:

```
const CACHE_NAME = "cache-v1";
const assetToCache = [
  "/index.html",
  "/",
  "/history.html",
  "/resources/mdl/material.indigo-pink.min.css",
  "/resources/mdl/material.min.js",
  "/resources/mdl/MaterialIcons-Regular.woff2",
  "/resources/mdl/material-icons.css",
  "/css/style.css",
  "/resources/dialog-polyfill/dialog-polyfill.js",
  "/resources/dialog-polyfill/dialog-polyfill.css",
  "/resources/system.js",
  "/js/transpiled/index.js",
  "/js/transpiled/history.js",
  "/js/transpiled/shared.js",
  "/hoodie/client.js"
];
self.addEventListener("install", function(event) {
  console.log("installing");
  event.waitUntil(
    caches
      .open(CACHE_NAME)
      .then((cache) => {
        return cache.addAll(assetToCache);
      })
      .catch(console.error)
  );
}); 
```

我们调用`caches.open(CACHE_NAME)`，它打开或创建一个缓存，并返回一个带有缓存对象的承诺。一旦我们有了这个对象，我们就调用`cache.addAll()`来存储我们想要缓存的所有东西，以便在离线时加载应用程序。

这个调用被封装在`events.waitUntil`中，它告诉浏览器不要终止服务工作者，直到传递给它的承诺被解决或拒绝。一个服务工作者可以在空闲一段时间后被浏览器终止，我们需要在完成缓存之前防止这种情况发生。

刷新页面，这将触发服务人员的注册。如果您使用 Chrome(或您首选的开发浏览器中的开发工具)，请在 DevTools 中打开“应用程序”选项卡，单击打开“缓存”菜单，您应该会找到一个与我们使用的名称相同的缓存。点击它，你会看到文件列在那里

[![](../Images/54d2097abd2b6c3dc3b8c87edfc97071.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kDPHc-Kn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/xIvEH5AVhrOC_FgsvVdIfe7odplyDs818gbjPwwAP88rRVaXw3Vh2aSxHPWy7b1HzULylAvVWAep2uppi-gpKlt2hJHQxFXoRZFDN6cTyrJIOyP3sIPvHNHEW1kg1Rnqe8xHAI14.png)

我们已经将我们的资产添加到了缓存中，但是我们需要在浏览器每次发出请求时为其提供缓存的资产。为了做到这一点，我们监听每次浏览器将要发出请求时调用的`fetch`事件。

在`sw.js`中添加以下代码来拦截所有网络请求，如果是对我们的任何缓存资产的请求，则使用缓存响应进行响应:

```
self.addEventListener("fetch", function(event) {
  event.respondWith(
    caches.match(event.request).then(function(response) {
      if (response) {
        return response; //return the matching entry found
      }
      return fetch(event.request);
    })
  );
}); 
```

我们使用了`event.respondWith()`，一种[获取](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent/respondWith)的方法。它阻止浏览器默认处理请求，并返回一个[响应](https://developer.mozilla.org/en-US/docs/Web/API/Response)对象的承诺。我们的实现或者从缓存中返回响应(如果可用的话),或者使用 Fetch API 发出正式请求并返回我们从该响应中获得的任何内容。

保存`sw.js`，再次打开并刷新页面，重新安装维修人员。您可能会注意到服务人员停在了激活步骤:

[![](../Images/ed65c13844db96c29ab1e28649fcf4c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JCHYLewR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/PLzu84mpBQuT-OnA7tVY-ua4DHlbDpyeAMWYQFuu7kGuF0EPenq3jGEX8_44yK8tyXSAY4ZZ03Z2MB3aTqqAGpo0Z1MH8h39SVa2hTJDHsJL2xJijJWmjFGBa6Cr_LM1g0hJkssJ.png)

单击跳过等待链接立即激活它。

刷新页面，查看从“网络”选项卡中的服务人员
加载的资产:

[![](../Images/307a3a1567dca94b01073496ab365593.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8rEJ4xQB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/CHlTkcvGpasgSzvFI8DFeQq5EvnM1soO19lB_upAjroU4zaO7_pktIxAFvkIOU_6BZCxcz3ZfrqYX8u40-XKAMCV4RCTCOUNUkeKvSTFx_N08bNJ65QZJPhbiRSLyh5EBDQVsGMs.png)

然后，您可以在开发工具中选择离线网络节流选项，刷新页面并浏览以查看它的工作情况:

[![](../Images/c9c8135020557adabf55cf4dd752cf5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ESgpd__L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/03/WH50vyewOHRfvj9udO-BcfpxqGX1hc_G_ZA6Lb5jeP2HohUXNBcUV7h-OLopeP100FWUR2pImFiVGk_Egjoco_V1EuxvpjTcbQmP6sEIFe2ADWtGtbhQUKhSeaFJWj8-59Kr8vXS.png)

# 就这么定了！

我们构建了一个基本的离线购物跟踪应用程序。离线优先是渐进式网络应用的核心部分，我们已经通过帽衫和服务人员解决了这个问题。Hoodie 提供了后端，可以轻松构建离线优先的应用程序，在服务器和所有连接的设备之间同步数据。服务工作者允许我们拦截请求并使用缓存的结果进行响应。对于不支持服务人员的浏览器，我们将优雅地退回到像正常网站一样工作。

我们还没有一个完整的 PWA，但是，我们只是奠定了基础。在另一篇帖子中，我们将查看使它成为 PWA 所需的其他一些内容，以及一些生成所需内容的有用工具(包括使用 workbox 生成服务人员脚本)。敬请期待！

你可以在 [GitHub](https://github.com/pmbanugo/shopping-list) 上找到最终应用的完整源代码。如果你对我在这篇文章中谈到的任何事情感到困惑，请留下评论，并随时在 Twitter 上与我联系(我很乐意连接，看看你接下来会做什么🚀).

> Peter Mbanugo 对离线优先感兴趣，并不断寻求学习更好的方法来构建快速、轻便和高性能的 web 应用程序。他目前的项目是一个实时应用状态同步服务。你可以邀请他在你的活动上发言，为你写作，编写软件，或者讨论离线优先和软件架构。在推特上随时联系他

# 引用

*   [线下第一社区](http://offlinefirst.org/)
*   [帽衫](http://hood.ie/)
*   [服务人员](https://developers.google.com/web/fundamentals/primers/service-workers)
*   [缓存 API](https://developers.google.com/web/fundamentals/instant-and-offline/web-storage/cache-api)